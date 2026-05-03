---
name: building-admin-dashboards
description: Builds secure Streamlit admin dashboards with YAML-based authentication (streamlit-authenticator), interactive data editor (st.data_editor), Firebase Firestore integration, and PDF preview. Use when creating back-office tools, admin consoles, quote review systems, or any internal Streamlit dashboard requiring protected access and data manipulation.
---

# Streamlit Admin Dashboard

Use when building the Admin Console for quote review, pricing management, or any internal tool.

For advanced patterns: [FIRESTORE_INTEGRATION.md](FIRESTORE_INTEGRATION.md) | [DATA_EDITOR_PATTERNS.md](DATA_EDITOR_PATTERNS.md)

---

## Project Structure

```
backend_python/
  admin_tool/
    app.py                 ← Entry point (streamlit run app.py)
    pages/
      1_📋_Quotes.py      ← Quote review + st.data_editor
      2_💰_Price_Book.py  ← Master Price Book editor
    config/
      auth.yaml            ← Credenziali (gitignored!)
    requirements.txt
```

## Authentication (streamlit-authenticator)

```bash
pip install streamlit-authenticator bcrypt
```

Generate hashed password once:
```python
import streamlit_authenticator as stauth
hashed = stauth.Hasher(["mypassword"]).generate()
print(hashed[0])  # → copy into auth.yaml
```

`config/auth.yaml`:
```yaml
credentials:
  usernames:
    admin:
      name: Admin SYD
      password: $2b$12$...   # bcrypt hash, NOT plain text
      roles: [admin]
cookie:
  name: sydbio_admin_cookie
  key: $ENV_RANDOM_SECRET_32CHARS  # from env var
  expiry_days: 1
```

> **MANDATORIO**: `auth.yaml` nel `.gitignore`. Non committare mai password hashate nel repo pubblico.

`app.py`:
```python
import streamlit as st
import streamlit_authenticator as stauth
import yaml
import os

with open("config/auth.yaml") as f:
    config = yaml.safe_load(f)

authenticator = stauth.Authenticate(
    config["credentials"],
    config["cookie"]["name"],
    os.environ["AUTH_COOKIE_KEY"],  # ← from env, not hardcoded
    config["cookie"]["expiry_days"]
)

name, auth_status, username = authenticator.login("🔐 SYD Admin Login", "main")

if auth_status:
    authenticator.logout("Logout", "sidebar")
    st.sidebar.success(f"Benvenuto, {name}")
    # ← Navigation via pages/ auto-loaded by Streamlit
elif auth_status is False:
    st.error("Credenziali errate")
elif auth_status is None:
    st.warning("Inserisci username e password")
    st.stop()
```

## Quote Review Page (`pages/1_📋_Quotes.py`)

```python
import streamlit as st
import pandas as pd
from google.cloud import firestore

db = firestore.Client()

def load_quotes(status_filter: str = "draft") -> list[dict]:
    docs = db.collection("projects").where("quote.status", "==", status_filter).stream()
    return [d.to_dict() for d in docs]

st.title("📋 Revisione Preventivi")

col1, col2 = st.columns([3, 1])
with col1:
    status = st.selectbox("Filtra per stato", ["draft", "pending_review", "approved"])
with col2:
    if st.button("🔄 Aggiorna"):
        st.cache_data.clear()

quotes = load_quotes(status)
project_id = st.selectbox("Seleziona progetto", [q["id"] for q in quotes])

if project_id:
    quote_ref = db.collection("projects").document(project_id)\
                  .collection("private_data").document("quote")
    quote_data = quote_ref.get().to_dict()
    
    df = pd.DataFrame(quote_data["items"])
    
    # Interactive editor
    edited_df = st.data_editor(
        df,
        num_rows="dynamic",
        column_config={
            "unit_price": st.column_config.NumberColumn("€/Unit", format="€%.2f"),
            "qty": st.column_config.NumberColumn("Quantità", min_value=0),
            "total": st.column_config.NumberColumn("Totale", disabled=True),
            "manual_override": st.column_config.CheckboxColumn("Override"),
        },
        key="quote_editor"
    )
    
    # Approval actions
    st.divider()
    col1, col2, col3 = st.columns(3)
    admin_notes = st.text_area("Note admin")
    
    if col1.button("✅ Approva e Invia", type="primary"):
        # Ricalcola totali con i valori editati
        items = edited_df.to_dict("records")
        for item in items:
            item["total"] = round(item["qty"] * item["unit_price"], 2)
            item["manual_override"] = True
        
        subtotal = sum(i["total"] for i in items)
        quote_data["items"] = items
        quote_data["financials"]["subtotal"] = subtotal
        quote_data["financials"]["vat_amount"] = round(subtotal * 0.22, 2)
        quote_data["financials"]["grand_total"] = round(subtotal * 1.22, 2)
        quote_data["status"] = "approved"
        quote_data["admin_notes"] = admin_notes
        
        quote_ref.set(quote_data)
        # Trigger PDF generation + n8n via FastAPI
        import httpx
        httpx.post(f"{BACKEND_URL}/quote/{project_id}/approve",
                   json={"decision": "approve", "notes": admin_notes})
        st.success("✅ Preventivo approvato e inviato!")
    
    if col3.button("❌ Rifiuta"):
        quote_ref.update({"status": "rejected", "admin_notes": admin_notes})
        st.warning("Preventivo rifiutato.")
```

## Running

```bash
cd backend_python/admin_tool
streamlit run app.py --server.port 8501
```

> In produzione, proteggere con VPN o IP allowlist. Non esporre su internet pubblico senza auth aggiuntiva (Cloudflare Access, IAP, ecc.).
