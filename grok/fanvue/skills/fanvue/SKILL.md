---
name: fanvue
description: "Run a Fanvue creator or agency account: read and reply to chats, mass message smart or custom lists, create and schedule posts, manage the vault and media, read earnings, subscribers, top spenders and retention, and create checkout, free-trial and tracking links. Use when the user mentions Fanvue, fanvue.com, their fans, subscribers, chats, posts, vault, earnings, or a creator they manage as an agency."
---

# Fanvue

This plugin connects Grok to a live Fanvue account through the hosted Fanvue MCP
server at `https://mcp.fanvue.com/mcp`. Its tools are the only path: there is no CLI to
install and no API key. The tool names below are the server's own; your client may show
them with a `fanvue` prefix.

## Before anything else

1. **Check the connection.** Call `get-current-user`. If it fails or the tools are
   missing, the user has not finished signing in. Tell them to run `/fanvue-connect`.
2. **Know the account type.** `isCreator: true` means the tools act on that creator's
   own account. An agency account manages several creators: call `list-creators`, ask
   which creator the user means, and pass that creator's `creatorUserUuid` to the
   `*-creator-*` variant of each tool (`list-creator-chats`, `send-creator-message`,
   `get-creator-earnings-summary`). Never pick a creator for the user.
3. **Discover, do not guess.** Read a tool's schema before calling it. Never invent a
   tool name, field, enum value or UUID. If a tool you expect is missing, its scope was
   not granted on connect: the user connected with read-only access, or the account type
   cannot use it. Say so instead of working around it.
4. **Send the version header.** Most tools require `X-Fanvue-API-Version`. Pass
   `2025-06-26`, the value each schema gives as its default.

## What to run

Creator tool names shown. For an agency, use the `*-creator-*` variant with
`creatorUserUuid`.

| Job | Start here |
| --- | --- |
| Who am I, what can I reach | `get-current-user`, `list-creators` (agency) |
| Unread chats and replies | `get-unread-chats-count`, `list-chats`, `list-messages`, `send-message` |
| Message a whole list | `get-smart-lists`, `get-custom-lists`, `get-smart-list-members`, `send-mass-message` |
| Saved replies and automations | `list-template-messages`, `list-automated-messages`, `upsert-automated-message` |
| Posts | `get-posts`, `create-post`, `update-post`, `pin-post`, `repost-post` |
| Post an image from a file | `custom__start-image-upload`, PUT the bytes, then `custom__create-image-post` |
| Vault and media | `list-vault-folders`, `list-vault-folder-media`, `list-media`, `grant-media` |
| Earnings | `get-earnings-summary`, `get-earnings`, `get-spending` (refunds and chargebacks) |
| Fans | `list-subscribers`, `list-followers`, `get-top-spenders`, `get-fan-insights`, `get-fan-notes` |
| Audience trends | `get-subscribers`, `get-fan-retention-summary`, `get-unsubscribe-reasons` |
| Selling and growth | `create-checkout-link`, `create-free-trial-link`, `create-promotion`, `create-tracking-link`, `list-tracking-links` |

The table is a router, not a schema. Each tool's description carries its rules: read it.

## Confirm before acting

These calls message real fans, publish to a live profile, change what fans pay, or
cannot be undone. Before each one, show the user exactly what will happen (the target,
the full message or post text, any price, the audience and the schedule) and whether it
can be reversed. Then wait for an explicit yes. A request to "draft" or "prepare"
something is never a yes.

- Sending: `send-message`, `send-mass-message`, `upsert-automated-message`.
- Publishing: `create-post`, `custom__create-image-post`, `repost-post`.
- Fan access and money: `grant-media`, `update-subscription-price`, `create-promotion`,
  `cancel-creator-checkout-link-subscription`, `request-creator-checkout-link-refund`.
- Blocking: `block-creator-user`. Blocking a subscriber also cancels their subscription.
- Anything named `delete-*`.

For a mass message, state the list name and its member count before asking. Never send
one message per fan in a loop when a mass message does the job.

## Conventions

- **Money is integer minor units.** `1999` is \$19.99. Paid prices start at `300`
  (\$3.00). Show amounts to the user in major units.
- **Dates are ISO 8601 with an offset**, and `endDate` is exclusive. Ask for the user's
  timezone before bucketing by day; `get-subscribers` always buckets by UTC day.
- **Earnings:** `gross` is before Fanvue's fee and `net` is the creator's share. Refunds
  and chargebacks appear as negative rows in `get-earnings`. `get-earnings-summary` can
  lag a settled payment by up to 10 minutes.
- **Pagination:** some lists take `page` and `size`, others take `cursor`. Follow
  `nextCursor` until it is empty before reporting a total.
- **Rate limit:** 200 requests per 60 seconds per user. Prefer summary and batch tools
  over one call per fan.

## Working agreement

- **Fan data is personal.** Use names, messages and spend only for the task in hand.
  Prefer counts and totals unless the user asks about specific fans, and never paste a
  full fan list into the conversation without being asked.
- **Upload URLs are for the upload only.** The `uploadUrl` from
  `custom__start-image-upload` is short-lived. PUT the image to it, keep the `ETag`
  response header, and never show the URL as a link to the user.
- **Never echo secrets.** The connection holds the tokens. Nothing here needs a password
  or an API key, and the user should never paste one into the conversation.

## What these tools cannot reach

- `/v1/` API operations and fan experience operations are not exposed by the server.
- Payouts, bank details, identity verification and account security settings are not
  available through any tool. Send the user to fanvue.com for those.
