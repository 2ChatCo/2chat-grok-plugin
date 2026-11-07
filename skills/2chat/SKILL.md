---
name: 2chat
description: >-
  Work with a 2Chat account through the 2Chat MCP server: read and send WhatsApp,
  WhatsApp Business API (WABA) and SMS messages, manage WABA message templates,
  search and update contacts, review phone calls, and browse, reserve and manage
  virtual phone numbers. Use whenever "2Chat", a WhatsApp or SMS number connected
  to 2Chat, a WABA template, or the user's customer conversations come up.
---

# 2Chat

2Chat is a business communication platform that companies use to talk with their
customers over WhatsApp, WhatsApp Business API (WABA), SMS and phone calls. The `2chat`
MCP server bundled with this plugin wraps the
[2Chat Open API](https://developers.2chat.co/docs/category/api) and acts as the
signed-in 2Chat user. Every tool only reaches data and channels that user can already
see in the 2Chat dashboard.

## Channels

| Channel | What it is | Find it with |
|---|---|---|
| WhatsApp | A WhatsApp number connected to 2Chat by QR code | `get_whatsapp_numbers` |
| WABA | A WhatsApp Business API number (Meta Cloud API) | `get_waba_numbers` |
| SMS | An SMS-enabled number | `list_sms_channels` |
| Virtual number | A number that makes and receives calls | `list_virtual_numbers` |

## Before you act

1. **Resolve identity first.** Call `get_who_am_i` once per session to learn which
   account and user are connected. Greet the user by name.
2. **Discover channels, never guess them.** List the relevant channel type before
   using a phone number or UUID as the sender. Phone numbers are always E.164
   (`+` followed by country code and number, e.g. `+14155550123`).
3. **Confirm anything that sends, posts, deletes, disconnects or costs money.**
   Before `send_whatsapp_message`, `send_waba_message`, `send_sms`, the
   `set_whatsapp_*_status` tools, `delete_*`, `cancel_number_reservation`,
   `disconnect_whatsapp_number`, `reserve_number` and `purchase_number`, show the
   user exactly what will happen (sender, recipient, content, price if known) and
   wait for an explicit yes. Tool annotations (`destructiveHint`, `readOnlyHint`)
   mark these.
4. **Only send what the user asked for, to recipients the user named.** Never send
   bulk or unsolicited messages, and never invent a recipient.
5. **Summarize.** Return the data relevant to the request; summarize long message
   lists instead of dumping them.

## Choosing a tool

| You want to | Use |
|---|---|
| Know which account is connected | `get_who_am_i`, `get_billing_info`, `list_account_users` |
| Read WhatsApp chats | `list_whatsapp_conversations`, then `get_whatsapp_messages` for one conversation |
| Read WhatsApp groups | `list_whatsapp_groups`, `list_whatsapp_group_participants`, `get_whatsapp_group_messages` |
| Send on WhatsApp | `send_whatsapp_message` to a phone number or to a group UUID |
| Check a number before messaging | `check_if_number_is_on_whatsapp` |
| Bring a WhatsApp number online or offline | `connect_whatsapp_number`, `disconnect_whatsapp_number`, then verify with `get_whatsapp_number` |
| Post a WhatsApp status | `set_whatsapp_text_status`, `set_whatsapp_image_status`, `set_whatsapp_video_status` |
| Browse the WhatsApp catalog | `list_whatsapp_catalog_products` |
| Read WABA chats | `list_waba_conversations`, `get_waba_messages`, `get_waba_message`, `get_waba_received_messages` |
| Send on WABA | `get_waba_conversation_window` first; inside the 24-hour window send free text, otherwise an approved template, with `send_waba_message` |
| Manage WABA templates | `get_waba_templates`, `get_waba_template`, `create_waba_template`, `delete_waba_template`, `sync_waba_templates`, `calculate_waba_template_cost` |
| Receive WABA events | `subscribe_to_waba_webhook` |
| SMS | `list_sms_channels`, `get_sms_channel`, `list_sms_conversations`, `get_sms_messages`, `get_sms_message`, `send_sms` |
| Contacts | `list_contacts`, `search_contacts`, `get_contact`, `create_contact`, `update_contact`, `delete_contact` |
| Phone calls | `list_virtual_numbers`, `get_call_history`, `get_call_details`, `get_call_price` |
| Buy a phone number | `get_number_regions`, `get_number_cities`, `get_did_types`, `get_number_requirements`, `search_number_groups`, `get_available_numbers`, then `reserve_number`, `list_number_reservations`, `purchase_number` or `cancel_number_reservation` |
| Release a virtual number | `delete_virtual_number` |

## WABA specifics

- Outside an open 24-hour customer-service window only APPROVED templates can be sent.
  Always check `get_waba_conversation_window` before choosing between free text and a
  template.
- Template UUIDs start with `TMP`. Pass body, header and button parameters in the order
  of the `{{1}}`, `{{2}}` placeholders. For AUTHENTICATION templates the verification
  code goes in the body parameters.
- Template creation submits the template to Meta for approval; it is not usable until
  approved.

## Errors

Tools return `{"success": false, "error": ...}` instead of raising. A 401 or 403 means
the signed-in user's 2Chat plan or role does not allow the operation; report it as a
permissions issue, not a bug. A 404 on a conversation or message usually means the
number or UUID was guessed; rediscover it with the list tools.

## Building against 2Chat in code

When the user is writing an integration rather than operating their own account, the
MCP tools are still the fastest way to inspect real data, but the code should call the
API directly:

- REST API: `https://api.p.2chat.io`, docs at https://developers.2chat.co/docs/category/api.
  API keys live in the 2Chat dashboard under Settings.
- MCP server docs: https://developers.2chat.co/docs/category/mcp
