---
name: capymail
description: Send emails using by capy. Use when the user explicitly asks to send an email, compose an email, email someone, or similar email-sending requests. Sender is automatically determined (no --from needed) - uses user's email alias ($CAPY_USER_EMAIL_ALIAS) if configured, otherwise capy@capymail.ai. IMPORTANT - Always extract recipient email address from user's request, or ask the user to provide it if not specified.
---

# Send Email Skill

Send emails using AWS SES (Amazon Simple Email Service) with automatic sender configuration and flexible recipient options.

## Overview

This skill enables Claude to send emails on behalf of users using AWS SES. Emails are automatically sent from the user's configured email alias (`$CAPY_USER_EMAIL_ALIAS`) when available. This can be overridden with the `--from` parameter, or defaults to `capy@capymail.ai` if no alias is configured.

**Sender Priority Order:**
1. `--from` parameter (highest priority - explicit override)
2. `$CAPY_USER_EMAIL_ALIAS` environment variable (user's configured alias)
3. `capy@capymail.ai` (default fallback)

**CRITICAL REQUIREMENT**: You MUST extract the recipient email address from the user's request. If no recipient is specified in the request, you MUST ask the user to provide the recipient email address before attempting to send.

## Sender Email Configuration

### Automatic Sender - No --from Parameter Needed

**The sender email is automatically determined - you don't need to specify `--from` in normal usage.**

When users have configured a personalized email alias (e.g., `user123@capymail.ai`), emails will automatically be sent from that alias. If no alias is configured, emails are sent from `capy@capymail.ai`.

**How It Works:**
- The system automatically sets `CAPY_USER_EMAIL_ALIAS` environment variable in the sandbox
- The send-email script checks this variable and uses it as the sender address
- No `--from` parameter needed - it works automatically
- Only use `--from` when you need to override this behavior (rare cases like support@ addresses)

**Example:**
```bash
# If CAPY_USER_EMAIL_ALIAS="alice-work@capymail.ai"
python3 scripts/send_email.py \
  --to "bob@example.com" \
  --from "$CAPY_USER_EMAIL_ALIAS" \
  --subject "Project Update" \
  --body "Here's the latest update on the project."
# Email will be sent from alice-work@capymail.ai automatically
```

**Override When Needed:**
```bash
# Use --from to override the automatic alias
python3 scripts/send_email.py \
  --from "support@capymail.ai" \
  --to "bob@example.com" \
  --subject "Support Request" \
  --body "Your support ticket has been resolved."
# Email will be sent from support@capymail.ai instead
```

## Quick Start

### Send to Specific Recipient (No --from Needed)

```bash
# Sender is automatically determined from CAPY_USER_EMAIL_ALIAS or capy@capymail.ai
python3 scripts/send_email.py \
  --to "alice@example.com" \
  --from "$CAPY_USER_EMAIL_ALIAS" \
  --subject "Meeting Reminder" \
  --body "Don't forget our meeting tomorrow at 2pm."
```

### Send Using User's Email Alias (Automatic)

When the user has a configured email alias, emails are automatically sent from that address:

```bash
# Assumes CAPY_USER_EMAIL_ALIAS is set (e.g., "alice-work@capymail.ai")
python3 scripts/send_email.py \
  --to "bob@company.com" \
  --from "$CAPY_USER_EMAIL_ALIAS" \
  --subject "Project Update" \
  --body "Hi Bob, here's the latest update on our project.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)"
# Email will be sent from alice-work@capymail.ai automatically
```

### Send HTML Email

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "bob@company.com" \
  --subject "Q1 Report" \
  --html "<h1>Quarterly Report</h1><p>Revenue increased by 25%...</p>"
```

### Send with Attachments

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "recipient@example.com" \
  --subject "Documents Attached" \
  --body "Please find the attached documents." \
  --attachments "report.pdf" "chart.png"
```

### Send to Multiple Recipients

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "alice@example.com" "bob@example.com" \
  --cc "carol@example.com" \
  --subject "Team Meeting" \
  --body "Meeting scheduled for Friday at 3pm."
```

### Send with Custom Sender (Override - Rare Cases Only)

```bash
# Only use --from when you need to override the automatic sender (e.g., support@ addresses)
python3 scripts/send_email.py \
  --from "support@capymail.ai" \
  --to "alice@example.com" \
  --subject "Support Ticket Update" \
  --body "Your support ticket has been resolved."
```

### Reply to an Email (Email Threading)

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "alice@example.com" \
  --subject "Re: Project Discussion" \
  --body "Thanks for your email. I'll review the proposal and get back to you." \
  --in-reply-to "<original-message-id@example.com>"
```

## Email Threading Support

The send-email skill supports email threading automatically. When replying to inbound emails, the server automatically maintains the email thread by injecting the appropriate threading headers.

### ⭐ AUTOMATIC EMAIL THREADING ⭐

**Email threading is now handled automatically by the server!** You no longer need to:
- Check environment variables like `$CAPY_ORIGINAL_EMAIL_MESSAGE_ID`
- Manually specify `--in-reply-to` parameter
- Access threading information from the session context

**The server automatically:**
- Reads `CAPY_ORIGINAL_EMAIL_MESSAGE_ID` from the session context
- Injects the `inReplyTo` header when forwarding to the worker
- Updates threading information when new emails arrive in the same session

**Simple usage - just send the email normally:**

```bash
# Replying to an inbound email - threading handled automatically!
python3 scripts/send_email.py \
  --to "sender@example.com" \
  --subject "Re: Original Subject" \
  --body "Your reply message"

# The server automatically includes threading headers if this is a reply
```

### How Email Threading Works

1. **Message-ID**: Every email sent has a unique Message-ID (e.g., `<abc123@example.com>`)
2. **In-Reply-To Header**: When replying, the server automatically includes the original email's Message-ID
3. **Email Client Threading**: Email clients use these headers to group related emails into conversation threads
4. **Dynamic Updates**: When a new email arrives in the same session, threading information is automatically updated

### Manual Threading Override (Advanced)

If you need to manually specify threading (rare cases):

```bash
python3 scripts/send_email.py \
  --to "sender@example.com" \
  --subject "Re: Original Subject" \
  --body "Your reply message here" \
  --in-reply-to "<original-message-id@example.com>"
```

### Message-ID Format

Message-IDs follow the format: `<unique-identifier@domain>` (including angle brackets)
- Example: `<1234567890.abcdef@capymail.ai>`
- Always include the angle brackets when passing to `--in-reply-to`

## Understanding User Requirements

**IMPORTANT**: This skill must intelligently extract email parameters from natural language user requests. Analyze the user's message to identify:

1. **Recipients**: Email addresses mentioned in the request - **REQUIRED**
   - "Send to alice@example.com" → Extract `alice@example.com`
   - "Email bob@company.com about the project" → Extract `bob@company.com`
   - **No recipient mentioned → ASK THE USER** for the recipient email address

2. **Subject**: The topic or purpose of the email
   - "Send an email about the meeting" → Subject: "Meeting"
   - "Email Alice about project status" → Subject: "Project Status"
   - No subject mentioned → Use default: "Message from Claude"

3. **Content**: The message body
   - "Tell her the report is ready" → Body: "The report is ready"
   - "Let him know I'll be late" → Body: "I'll be late"
   - Extract and formulate clear, professional email content
   - **ALWAYS end emails with this signature**:
     ```
     Warmly,
     Capy
     AI agent at HappyCapy
     [happycapy.ai](http://happycapy.ai/)
     ```

4. **Additional Recipients**:
   - "CC carol@example.com" → Use `--cc carol@example.com`
   - "BCC david@example.com" → Use `--bcc david@example.com`

## Recipient Address Handling

**CRITICAL**: Recipients are REQUIRED for every email. The script no longer has a default recipient.

**When User Specifies Recipients**: When the user mentions recipients:
- "Send to alice@example.com" → Use `--to alice@example.com`
- "Email bob@company.com and alice@example.com" → Use `--to bob@company.com alice@example.com`
- "CC carol@example.com" → Use `--cc carol@example.com`
- "BCC david@example.com" → Use `--bcc david@example.com`

**Parse recipient addresses from user message** and pass them via `--to`, `--cc`, or `--bcc` parameters.

**When No Recipient Specified**: If the user doesn't mention any recipient email address, you MUST ask the user for the recipient before attempting to send the email. Example response: "Who would you like me to send this email to? Please provide the recipient's email address."

## Environment Variables

The script uses the following environment variables:

- `SERVER_URL`: (optional) Base URL of the server proxy (default: `http://localhost:3001`)
- `ANTHROPIC_CUSTOM_HEADERS`: (optional, for usage tracking) If not set, emails will still be sent successfully, but usage will not be recorded for billing/analytics purposes

The following variables are managed by the **server proxy** (not needed by the script directly):
- `AGENT_WORKER_BASE_URL`: Base URL of the Worker API (managed by server proxy)
- `AGENT_WORKER_SECRET`: Authentication secret for Worker API (managed by server proxy)
- `FLY_APP_NAME`: Sandbox ID (automatically set by Fly.io)
- `CAPY_USER_EMAIL_ALIAS`: (optional, automatically set) User's personalized email alias (e.g., `user123@capymail.ai`). When set, the server proxy automatically sends emails from this address unless overridden with `--from` parameter.

**Email Threading Variables** (optional, automatically set when the task was created by an inbound email):
- `CAPY_ORIGINAL_EMAIL_MESSAGE_ID`: Message-ID of the original email that triggered this task
- `CAPY_ORIGINAL_EMAIL_FROM`: Email address of the original sender
- `CAPY_ORIGINAL_EMAIL_SUBJECT`: Subject line of the original email

**IMPORTANT for Email Threading**: Email threading is now handled automatically by the server! You no longer need to check environment variables or manually specify threading parameters:

```bash
# Simply send the email - threading is automatic!
python3 scripts/send_email.py \
  --to "sender@example.com" \
  --subject "Re: Original Subject" \
  --body "Your reply message"

# The server automatically reads CAPY_ORIGINAL_EMAIL_MESSAGE_ID from the session
# context and includes it in the email, maintaining the thread.
```

These environment variables are automatically managed by the Worker and updated dynamically when new emails arrive in the same session.

## AWS SES Configuration

**Pre-configured settings** (handled automatically by Worker API):
- Region: us-east-1
- Identity ARN: arn:aws:ses:us-east-1:257417524232:identity/capymail.ai
- Configuration Set: Capy
- Default Sender: Uses $CAPY_USER_EMAIL_ALIAS if configured, otherwise capy@capymail.ai (can be overridden via --from parameter)

**Note**: AWS SES credentials are now managed by the Worker API and are not exposed to the sandbox. This improves security by centralizing credential management.

## Script Parameters

### Required:
- `--to EMAIL [EMAIL...]`: Recipient email address(es) - **REQUIRED, must always be specified**
- `--body TEXT` OR `--html HTML`: Plain text or HTML email body (at least one required)

### Optional:
- `--from EMAIL`: Sender email address (default: "$CAPY_USER_EMAIL_ALIAS" if set, otherwise "capy@capymail.ai")
- `--subject TEXT`: Email subject line (default: "Message from Claude")
- `--attachments FILE [FILE...]`: One or more files to attach
- `--reply-to EMAIL`: Reply-to email address
- `--in-reply-to MESSAGE_ID`: Message-ID of the email being replied to (for email threading)
- `--cc EMAIL [EMAIL...]`: CC recipients
- `--bcc EMAIL [EMAIL...]`: BCC recipients
- `--json`: Output result as JSON (default: human-readable)

## Output Format

The script returns JSON with send status:

```json
{
  "success": true,
  "message_id": "010001234567890-abcdef...",
  "from": "capy@capymail.ai",
  "to": ["john.doe@example.com"],
  "cc": [],
  "bcc": [],
  "subject": "Hello from Claude",
  "timestamp": "2024-01-22T10:30:00Z",
  "attachments": 0
}
```

## Error Handling

Common errors and solutions:

1. **Missing Worker Credentials**: Ensure AGENT_WORKER_BASE_URL and AGENT_WORKER_SECRET are set (automatically configured)
2. **Connection Error**: Worker API unreachable - check network connectivity
3. **Authentication Failed**: Invalid authentication token (automatically handled)
4. **Email Not Verified**: Recipient email must be verified in SES (production uses verified domain)
5. **Sending Limits**: AWS SES has rate limits (14 emails/second, 50,000/day)
6. **Large Attachments**: Total email size limit is 10MB including base64 encoding overhead

See `references/aws_ses_guide.md` for detailed troubleshooting.

## Usage Examples

These examples demonstrate how to extract email parameters from natural language user requests:

### Example 1: User Doesn't Specify Recipient

**User Request**: "Send me an email reminder about the meeting tomorrow at 2pm"

**Claude's Response**: "I'd be happy to send you an email reminder about the meeting tomorrow at 2pm. Could you please provide the email address where you'd like to receive this reminder?"

**User Follow-up**: "Send it to john@example.com"

**Parameter Extraction**:
- Recipient: Extract "john@example.com" → `--to john@example.com`
- Subject: Extract "meeting tomorrow at 2pm" → "Meeting Reminder"
- Body: Formulate reminder with signature

```bash
python3 scripts/send_email.py \
  --to "john@example.com" \
  --subject "Meeting Reminder" \
  --body "Don't forget about the meeting tomorrow at 2:00 PM.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)"
```

### Example 2: Email to Colleague

**User Request**: "Send an email to alice@company.com telling her the report is ready"

**Parameter Extraction**:
- Recipient: Extract "alice@company.com" → `--to alice@company.com`
- Subject: Extract topic "report" → "Report Ready"
- Body: Extract message "the report is ready" → Professional format

```bash
python3 scripts/send_email.py \
  --to "alice@company.com" \
  --subject "Report Ready" \
  --body "Hi Alice, the Q1 report is now ready for your review.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)"
```

### Example 3: Natural Language Request

**User Request**: "Email bob@company.com about the project delay and let him know we'll deliver next week"

**Parameter Extraction**:
- Recipient: Extract "bob@company.com" → `--to bob@company.com`
- Subject: Extract topic "project delay" → "Project Delay"
- Body: Extract message and formulate professionally

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "bob@company.com" \
  --subject "Project Delay" \
  --body "Hi Bob, I wanted to inform you about a delay in the project timeline. We will be able to deliver the work next week. Thank you for your understanding.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)"
```

### Example 4: Multiple Recipients

**User Request**: "Email the sales report to bob@company.com and alice@company.com"

**Parameter Extraction**:
- Recipients: Extract both addresses → `--to bob@company.com alice@company.com`
- Subject: Extract "sales report" → "Sales Report"
- Body: Formulate appropriate message

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "bob@company.com" "alice@company.com" \
  --subject "Sales Report" \
  --body "Please find the sales report attached.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)" \
  --attachments "sales_report.pdf"
```

### Example 5: Email with CC

**User Request**: "Send me a summary email at john@example.com and CC my manager at manager@company.com"

**Parameter Extraction**:
- Recipient: Extract "john@example.com" → `--to john@example.com`
- CC: Extract "manager@company.com" → `--cc manager@company.com`
- Subject: Extract "summary" → "Today's Summary"
- Body: Generate summary content

```bash
# No --from needed - sender is automatic
python3 scripts/send_email.py \
  --to "john@example.com" \
  --cc "manager@company.com" \
  --subject "Today's Summary" \
  --html "<h2>Summary</h2><ul><li>Task 1 completed</li><li>Task 2 in progress</li></ul><br><br>Warmly,<br>Capy<br>AI agent at HappyCapy<br><a href='http://happycapy.ai/'>happycapy.ai</a>"
```

### Example 6: Brief Request - Missing Email

**User Request**: "Email Alice that I'll be 10 minutes late"

**Claude's Response**: "I can help you email Alice. Could you please provide Alice's email address?"

**User Follow-up**: "alice@company.com"

**Parameter Extraction**:
- Recipient: Extract "alice@company.com" → `--to alice@company.com`
- Subject: Infer from context → "Running Late"
- Body: Extract message → "I'll be 10 minutes late"

```bash
python3 scripts/send_email.py \
  --to "alice@company.com" \
  --subject "Running Late" \
  --body "Hi Alice, I wanted to let you know that I'll be approximately 10 minutes late. See you soon.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)"
```

### Example 7: Reply to an Email Thread

**User Request**: "Reply to Bob's email with message ID <1234@example.com> and tell him I agree with the proposal"

**Parameter Extraction**:
- Recipient: Extract context (Bob) → `--to bob@company.com` (may need to ask for email)
- Subject: Keep thread subject → "Re: Proposal Discussion"
- Body: Extract message → "I agree with the proposal"
- In-Reply-To: Extract Message-ID → `--in-reply-to <1234@example.com>`

```bash
python3 scripts/send_email.py \
  --to "bob@company.com" \
  --subject "Re: Proposal Discussion" \
  --body "Hi Bob, I agree with the proposal. Let's move forward with this approach.

Warmly,
Capy
AI agent at HappyCapy
[happycapy.ai](http://happycapy.ai/)" \
  --in-reply-to "<1234@example.com>"
```

## Implementation Notes

1. **Always use the bundled Python script** rather than writing custom email code
2. **Check script output** for success/failure status
3. **Handle sensitive data carefully** - don't log email contents

## Implementation Notes (continued)

4. **Verify file paths** for attachments before sending
5. **Use HTML for rich formatting** when the user wants formatted emails
6. **Parse recipient addresses** from user messages and pass via --to, --cc, --bcc

## Migration Notes

This skill has been through two migrations:

### Phase 1: Direct AWS SES → Worker API
- Removed direct boto3 calls and AWS credentials from sandbox
- Python script called Worker API which handles SES integration

### Phase 2 (Current): Worker API → Server Proxy
- Python script now calls the server proxy (`/api/email/send-proxy`) instead of Worker API directly
- Server proxy auto-injects email threading info from GlobalEnv (dynamic updates within a session)
- Server proxy auto-injects sender address from `CAPY_USER_EMAIL_ALIAS`
- Script no longer needs `AGENT_WORKER_BASE_URL` or `AGENT_WORKER_SECRET`
- Attachment support added (base64 encoding with MIME type detection)

### Technical Details
- **Architecture**: Sandbox → Server Proxy (`/api/email/send-proxy`) → Worker API → AWS SES
- **Server Proxy Endpoint**: `POST http://localhost:3001/api/email/send-proxy`
- **Script Headers**: `Content-Type: application/json` (auth handled by proxy)
- **Optional Headers**: `X-Anthropic-Custom-Headers` (for usage tracking)
- **Dependencies**: Python 3 stdlib only (no external packages)

## Best Practices

1. **ALWAYS extract or ask for recipient**: Never attempt to send without a recipient email address. If none is specified, ask the user.
2. **Confirm recipient when specified**: If user specifies a recipient, briefly confirm: "Sending to alice@example.com..."
3. **DON'T use --from parameter in normal usage**: The sender is automatically determined from `$CAPY_USER_EMAIL_ALIAS` or defaults to `capy@capymail.ai`. Only use `--from` in rare cases when you need to override (e.g., for support@ or noreply@ addresses).
4. **User email aliases work automatically**: When users have configured email aliases, emails will automatically be sent from their personalized address without any additional configuration.
5. **Validate attachments**: Check files exist and are readable
6. **Use meaningful subjects**: Help users find emails later
7. **Handle errors gracefully**: Report AWS SES errors to user in plain language
8. **Respect user privacy**: Don't send emails without explicit user request
9. **Support natural language**: Parse "send to alice@example.com and bob@example.com" into multiple recipients

## Security Notes

- Emails are sent from the user's configured alias (`$CAPY_USER_EMAIL_ALIAS`) when available, defaulting to `capy@capymail.ai`, and can be overridden via `--from` parameter
- Custom sender addresses must be verified in AWS SES to send successfully
- Recipients can be any valid email address (user-specified, must be provided in the request)
- AWS credentials are managed securely via environment variables
- All emails go through AWS SES with proper authentication
