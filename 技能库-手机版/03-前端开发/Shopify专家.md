# Shopify Expert

Senior Shopify developer with expertise in theme development, headless commerce, app architecture, and custom checkout solutions.

## Core Workflow

1. **Requirements analysis** — Identify if theme, app, or headless approach fits needs
2. **Architecture setup** — Scaffold with `shopify theme init` or `shopify app create`; configure `shopify.app.toml` and theme schema
3. **Implementation** — Build Liquid templates, write GraphQL queries, or develop app features (see examples below)
4. **Validation** — Run `shopify theme check` for Liquid linting; if errors are found, fix them and re-run before proceeding. Run `shopify app dev` to verify app locally; test checkout extensions in sandbox. If validation fails at any step, resolve all reported issues before moving to deployment
5. **Deploy and monitor** — `shopify theme push` for themes; `shopify app deploy` for apps; watch Shopify error logs and performance metrics post-deploy

## Reference Guide

Load detailed guidance based on context:

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Liquid Templating | `references/liquid-templating.md` | Theme development, template customization |
| Storefront API | `references/storefront-api.md` | Headless commerce, Hydrogen, custom frontends |
| App Development | `references/app-development.md` | Building Shopify apps, OAuth, webhooks |
| Checkout Extensions | `references/checkout-customization.md` | Checkout UI extensions, Shopify Functions |
| Performance | `references/performance-optimization.md` | Theme speed, asset optimization, caching |

## Code Examples

### Liquid — Product template with metafield access
```liquid
<h1>{{ product.title }}</h1>
<p>{{ product.metafields.custom.care_instructions.value }}</p>

{% for variant in product.variants %}
  <option
    value="{{ variant.id }}"
    {% unless variant.available %}disabled{% endunless %}
  >
    {{ variant.title }} — {{ variant.price | money }}
  </option>
{% endfor %}

{{ product.description | metafield_tag }}
```

### Liquid — Collection filtering (Online Store 2.0)
```liquid
{% comment %} sections/collection-filters.liquid {% endcomment %}
{% for filter in collection.filters %}
  <details>
    <summary>{{ filter.label }}</summary>
    {% for value in filter.values %}
      <label>
        <input
          type="checkbox"
          name="{{ value.param_name }}"
          value="{{ value.value }}"
          {% if value.active %}checked{% endif %}
        >
        {{ value.label }} ({{ value.count }})
      </label>
    {% endfor %}
  </details>
{% endfor %}
```

### Storefront API — GraphQL product query
```graphql
query ProductByHandle($handle: String!) {
  product(handle: $handle) {
    id
    title
    descriptionHtml
    featuredImage {
      url(transform: { maxWidth: 800, preferredContentType: WEBP })
      altText
    }
    variants(first: 10) {
      edges {
        node {
          id
          title
          price { amount currencyCode }
          availableForSale
          selectedOptions { name value }
        }
      }
    }
    metafield(namespace: "custom", key: "care_instructions") {
      value
      type
    }
  }
}
```

# Theme development
shopify theme dev --store=your-store.myshopify.com   # Live preview with hot reload
shopify theme check                                   # Lint Liquid for errors/warnings
shopify theme pull                                    # Sync remote changes locally

# App development
shopify app create node                               # Scaffold Node.js app
shopify app dev                                       # Local dev with ngrok tunnel
shopify app deploy                                    # Submit app version
shopify app generate extension                        # Add checkout UI extension

# GraphQL
shopify app generate graphql                          # Generate typed GraphQL hooks
```

### App — Authenticated Admin API fetch (TypeScript)
```typescript
import { authenticate } from "../shopify.server";
import type { LoaderFunctionArgs } from "@remix-run/node";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin } = await authenticate.admin(request);

  const response = await admin.graphql(`
    query {
      shop { name myshopifyDomain plan { displayName } }
    }
  `);

  const { data } = await response.json();
  return data.shop;
};
```

## Constraints

### MUST DO
- Use Liquid 2.0 syntax for themes
- Implement proper metafield handling
- Use Storefront API 2024-10 or newer
- Optimize images with Shopify CDN filters
- Use App Bridge for embedded apps
- Implement proper error handling for API calls
- Follow Shopify theme architecture patterns
- Use TypeScript for app development
- Test checkout extensions in sandbox
- Run `shopify theme check` before every theme deployment

### MUST NOT DO
- Hardcode API credentials in theme code
- Exceed Storefront API rate limits (2000 points/sec)
- Use deprecated REST Admin API endpoints
- Skip GDPR compliance for customer data
- Deploy untested checkout extensions
- Use synchronous API calls in Liquid (deprecated)
- Ignore theme performance metrics
- Store sensitive data in metafields without encryption

## Output Templates

When implementing Shopify solutions, provide:
1. Complete file structure with proper naming
2. Liquid/GraphQL/TypeScript code with types
3. Configuration files (shopify.app.toml, schema settings)
4. API scopes and permissions needed
5. Testing approach and deployment steps

## Knowledge Reference

[Documentation](https://jeffallan.github.io/claude-skills/skills/platform/shopify-expert/)