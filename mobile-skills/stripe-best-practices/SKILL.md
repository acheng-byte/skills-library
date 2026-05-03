---
name: stripe-best-practices
description: "Stripe API最佳实践参考指南，覆盖一次性支付、订阅、Connect平台、嵌入式金融账户等集成场景的路由选择。当用户提到Stripe集成、支付接入、订阅计费、Stripe API时触发。"
---

Latest Stripe API version: **2026-04-22.dahlia**. Always use the latest API version and SDK unless the user specifies otherwise.

## Integration routing

| Building…                                                                | Recommended API                     | Details                  |
| ------------------------------------------------------------------------ | ----------------------------------- | ------------------------ |
| One-time payments                                                        | Checkout Sessions                   | <references/payments.md> |
| Custom payment form with embedded UI                                     | Checkout Sessions + Payment Element | <references/payments.md> |
| Saving a payment method for later                                        | Setup Intents                       | <references/payments.md> |
| Connect platform or marketplace                                          | Accounts v2 (`/v2/core/accounts`)   | <references/connect.md>  |
| Subscriptions or recurring billing                                       | Billing APIs + Checkout Sessions    | <references/billing.md>  |
| Embedded financial accounts / banking                                    | v2 Financial Accounts               | <references/treasury.md> |
| Security (key management, RAKs, webhooks, OAuth, 2FA, Connect liability) | See security reference              | <references/security.md> |

Read the relevant reference file before answering any integration question or writing code.

## Key documentation

When the user’s request does not clearly fit a single domain above, consult:

- [Integration Options](https://docs.stripe.com/payments/payment-methods/integration-options.md) — Start here when designing any integration.
- [API Tour](https://docs.stripe.com/payments-api/tour.md) — Overview of Stripe’s API surface.
- [Go Live Checklist](https://docs.stripe.com/get-started/checklist/go-live.md) — Review before launching.