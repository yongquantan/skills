---
name: setup-devin-slack
description: Set up the Devin Slack integration end-to-end — create a Slack account/workspace if needed, install the Devin app, link the user's profile, and verify with a live @Devin session. Use when the user wants to tag @Devin in their own Slack workspace, including clean-room setups where no Slack account exists yet. Drive the user's own browser via Playwriter.
---

# Set up Devin in a Slack workspace

End state: tagging `@Devin` in a channel starts a Devin cloud session that replies in-thread. Works on the free Slack plan — only Slack's own AI sidebar needs paid; `@Devin` tags and slash commands are free.

Drive the user's real browser (Playwriter on their Chrome/Comet). Signed-in Gmail/Google in the browser solves email verification codes and reCAPTCHA risk scoring without extra accounts.

## Phase A — Slack account and workspace (skip if both exist)

1. `https://slack.com/get-started#/createnew` → enter email → dismiss cookie banner first.
2. **reCAPTCHA** appears under the email field. Click the checkbox via `frameLocator('iframe[src*="recaptcha"][src*="anchor"]').locator('#recaptcha-anchor')` — on a real browser with a live Google session it usually auto-passes. If an image challenge opens, hand the browser to the user.
3. Slack emails a 6-character code (format `XXX-XXX`). Read it from the Gmail tab (`#search/from:slack+newer_than:1d` — the code is in the row snippet) or ask the user. Type digits into the first `textbox "digit 1 of 6"`; they auto-advance.
4. Click **Create a new workspace** on the landing page — even if the email already owns a Slack account and is in other workspaces.
5. Wizard: workspace name → display name → **Skip** teammate invites → confirm **Don't Invite Anyone**.
6. **Onboarding modals keep returning.** The segmentation survey ("How do you want to use Slack?" → Work → familiarity → "Get started") and a plan-upsell dialog reappear until fully answered. They overlay the message composer and eat clicks — finish or dismiss them before typing anything.
7. Slack auto-assigns a URL like `name-hq.slack.com`. Change it at `https://<domain>.slack.com/admin/name`. Common names are usually taken — expect "That URL is unavailable" and retry with a fallback (e.g. brand + country code).
8. Workspace icon: `/customize/icon` → `setInputFiles` on `input[type=file]` → `#image_upload_button` → `#cropbtn`. SVGs don't upload; render to ≥512px PNG first (load remote SVG in a page and screenshot it — `file://` img loads are blocked in setContent pages).

## Phase B — install the Devin app

1. `app.devin.ai` → **Settings → Connections → Slack → Connect**.
2. The Slack OAuth page opens — **it defaults to the user's most-active workspace, not the intended one.** Open `oauth-team-picker-button` and explicitly select the target workspace before clicking Allow. Installing on the wrong workspace is the easiest mistake in this whole flow.
3. The flow chains into `slack-signin-callback` (`flow=slack_user_link`) — click **Accept and Continue**. This links the Slack user to the Devin user; every workspace member must do this once.
4. Back on the settings page, the card shows the connected workspace name. `chain_user_link=1` in the URL confirms the link ran.
5. Slack email must match the Devin account email, or linking silently fails.

## Phase C — verify

1. Open the Slack client channel. Devin **only hears mentions in channels it's a member of**: `/invite @Devin`, or tag it and accept the "not in channel" invite prompt. `app_mentions` never reaches it otherwise.
2. The composer has **multiple `div[contenteditable="true"]` editors** — hidden ones make `.last()` clicks hang forever. Pick the visible one (`aria-label "Message to <channel>"`).
3. Type `@Devin`, pick the AGENT entry from the autocomplete, add a short task, send. Expect "Devin · Thinking…" then a threaded reply within ~60s. That's the end-to-end proof.

## Playwriter notes for this flow

- Slack client pages are heavy: keep `--timeout` ≥30s, use scoped `snapshot({ locator })` or `search`, and prefer screenshots over full snapshots on the client.
- reCAPTCHA/country modals are iframes — scope `frameLocator` by `src*="anchor"`; the `title*="reCAPTCHA"` selector matches both anchor and challenge frames and throws strict-mode violations.
- OAuth popups are relocated to tabs; watch `context.pages()` for the newest URL.

## Known broken (verified 2026-11-17)

- `google-productivity` plugin MCP (`google-mcp.devin.ai/mcp`) cannot sign in: "needs a pre-registered OAuth client ID and secret" in both Desktop and webapp, Organization **and** Personal sign-in modes. Cognition-side gap — report, don't configure around it.
- `slack-remote` plugin MCP fails `tools/list` the same way.
- `google-workspace` (Drive/Docs) works — separate plugin, unaffected.
