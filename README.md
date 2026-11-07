# 2Chat plugin for Grok

Connect Grok to [2Chat](https://2chat.co), the business messaging platform for WhatsApp,
WhatsApp Business API (WABA), SMS and phone calls. Read, summarize and reply to customer
conversations, manage WABA templates and contacts, review phone calls, and manage virtual
phone numbers, all through the hosted 2Chat MCP server.

## Installation

In Grok, open `/plugin`, search for **2Chat**, and install.

On first connection, Grok opens the 2Chat sign-in in your browser. Authorize with the
2Chat account you want Grok to work with. No API key is needed and nothing should be
pasted into chat.

Requires a 2Chat account at https://2chat.co.

## What you get

- **MCP server** `2chat` at `https://mcp.2chat.io/mcp` (Streamable HTTP, OAuth 2.1).
- **Skill** `2chat`: how to use the 2Chat tools well (discover channels before acting,
  E.164 phone numbers, the WABA 24-hour window, confirm every send and every
  destructive action).

### Tools

| Area | Tools |
|---|---|
| Account | `get_who_am_i`, `get_billing_info`, `list_account_users` |
| WhatsApp | `get_whatsapp_numbers`, `get_whatsapp_number`, `connect_whatsapp_number`, `disconnect_whatsapp_number`, `check_if_number_is_on_whatsapp`, `list_whatsapp_conversations`, `get_whatsapp_messages`, `send_whatsapp_message`, `list_whatsapp_groups`, `list_whatsapp_group_participants`, `get_whatsapp_group_messages`, `set_whatsapp_text_status`, `set_whatsapp_image_status`, `set_whatsapp_video_status`, `list_whatsapp_catalog_products` |
| WhatsApp Business API | `get_waba_numbers`, `get_waba_number`, `get_waba_templates`, `get_waba_template`, `create_waba_template`, `delete_waba_template`, `sync_waba_templates`, `calculate_waba_template_cost`, `get_waba_conversation_window`, `send_waba_message`, `get_waba_messages`, `get_waba_message`, `get_waba_received_messages`, `list_waba_conversations`, `subscribe_to_waba_webhook` |
| SMS | `list_sms_channels`, `get_sms_channel`, `list_sms_conversations`, `get_sms_messages`, `get_sms_message`, `send_sms` |
| Contacts | `list_contacts`, `search_contacts`, `get_contact`, `create_contact`, `update_contact`, `delete_contact` |
| Phone numbers | `get_number_regions`, `get_number_cities`, `get_did_types`, `get_number_requirements`, `search_number_groups`, `get_available_numbers`, `reserve_number`, `list_number_reservations`, `cancel_number_reservation`, `purchase_number` |
| Voice | `list_virtual_numbers`, `delete_virtual_number`, `get_call_history`, `get_call_details`, `get_call_price` |

Every tool carries MCP annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`,
`openWorldHint`), so Grok can tell a lookup from a send or a deletion before calling it.
Forty-one of the sixty tools are read-only.

## Example prompts

- "Which 2Chat account am I connected to?"
- "Summarize my most recent WhatsApp conversations."
- "Show the WhatsApp Business templates approved on my number."
- "Reply to the last message from +14155550123 and tell them the order has shipped."
- "Show the phone calls I received yesterday on my virtual numbers."

## Authentication and network

The plugin connects only to `https://mcp.2chat.io`. Authentication is OAuth 2.1
authorization code with PKCE (S256) and dynamic client registration; Grok handles the
flow.

Network endpoints:

- `https://mcp.2chat.io/mcp`: hosted MCP (Streamable HTTP)
- `https://mcp.2chat.io/authorize`, `/token`, `/register`, `/revoke`: OAuth 2.1 + DCR
- `https://mcp.2chat.io/.well-known/oauth-authorization-server`, `/.well-known/oauth-protected-resource`: discovery metadata
- `https://auth.2chat.io`: human sign-in and consent screen during authorization

Credentials: a 2Chat account. The MCP server exchanges the grant for a token scoped to
that account and sends it as `Authorization: Bearer` on `/mcp`. The plugin stores no API
key and requests no filesystem or shell access. Tools act only on channels and data the
signed-in user can already see in 2Chat.

Messaging tools deliver real messages to real phone numbers and can incur carrier or
Meta charges. The bundled skill instructs Grok to confirm every send, status post,
deletion, disconnection and purchase with the user before calling the tool, and never to
send bulk or unsolicited messages.

## License

MIT for the files in this plugin. Use of the hosted MCP server is governed by the
[2Chat terms of service](https://2chat.co/legal/terms-of-service) and
[privacy policy](https://2chat.co/legal/privacy-policy).
