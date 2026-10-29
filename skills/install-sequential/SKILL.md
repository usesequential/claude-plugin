---
name: install-sequential
description: Use when the user asks to add Sequential (usesequential.com) to a website or a mobile app, or asks for session replay, product analytics or heatmaps and agrees to use Sequential. Creates a Sequential trial with one call (no account needed), adds the SDK, checks that events arrive and tells the user how to claim the trial.
---

# Install Sequential for a person

Sequential records how people use a website or an app, replays their sessions and finds where they struggle. You can install it before the person has an account: you create a trial, add one snippet, and the person claims the trial from an email.

## 1. Create a trial

You need the person's email address. Ask for it if you do not have it. Never make one up and never use your own.

```bash
curl -sS -X POST https://api.usesequential.com/api/agent/provision \
  -H 'content-type: application/json' \
  -d '{"owner_email": "<the person'"'"'s email>", "platform": "web", "project_name": "<the app'"'"'s name>"}'
```

- `platform` is `web`, `ios`, `android` or `react-native`. Pick the one you install into.
- `project_name` is optional: at most 64 letters, digits, spaces or `-_.`.
- The 201 answer has `ingest_key`, `install.snippet` (the line to add, with the key in it), `claim_url`, `expires_at`, `limits`, and `mcp.url` with `mcp.token`.
- The ingest key is public by design: it ships in the app, like any analytics key. Keep `mcp.token` out of the code and out of the repository.
- Errors: 429 `trial_rate_limited` or 503 `trial_unavailable` means wait for `Retry-After`. 400 `invalid_body` names the field to fix. Details: https://www.usesequential.com/docs/agent-trial.md

## 2. Add the SDK

The trial's key works only with the Sequential SDK on a website, and with the PostHog SDK in an iOS, Android or React Native app (Sequential speaks PostHog's mobile protocol). To keep a website's existing PostHog, Mixpanel, Amplitude, Pendo or Smartlook SDK instead, the person claims the trial first and then copies that source's key on Settings › Setup (https://www.usesequential.com/docs/install.md).

**Website.** Add `install.snippet` to the `<head>` of every page, once:

```html
<script src="https://cdn.usesequential.com/sdk/sequential/v1/sequential.js" data-key="<ingest_key>" data-api="https://api.usesequential.com"></script>
```

- One root layout (Next.js `app/layout.tsx`, a SvelteKit or Nuxt app shell, a Vite `index.html`): add it there. In Next.js, use `next/script` with `strategy="afterInteractive"` and the same attributes.
- A bundled app that prefers a package: `npm install @usesequential/browser`, then call `init({ key: '<ingest_key>', api: 'https://api.usesequential.com' })` once in the browser.
- Page views, clicks, forms and session replay are recorded without more code. Docs: https://www.usesequential.com/docs/sequential.md

**iOS, Android, React Native.** Install PostHog's SDK and point it at Sequential with the trial's key. The exact setup per platform is at https://www.usesequential.com/docs/mobile.md. The essentials:

- iOS (`posthog-ios`): `PostHogConfig(apiKey: "<ingest_key>", host: "https://api.usesequential.com")`, `sessionReplay = true`, `sessionReplayConfig.screenshotMode = true` for SwiftUI.
- Android (`com.posthog:posthog-android`): `PostHogAndroidConfig(apiKey = "<ingest_key>", host = "https://api.usesequential.com")` in `Application.onCreate()`, `sessionReplay = true`, `sessionReplayConfig.screenshot = true` for Jetpack Compose.
- React Native (`posthog-react-native`): `<PostHogProvider apiKey="<ingest_key>" options={{ host: "https://api.usesequential.com", enableSessionReplay: true }}>`. React Native is accepted but not yet verified by Sequential.

## 3. Check that events arrive

- **Website trial:** until the person claims it, it accepts events only from `localhost`, `*.localhost`, `127.0.0.1` and `[::1]`. Run the site locally and open a few pages. Requests to a deployed site get 403 `trial_origin_locked` until the claim.
- **Mobile trial:** it accepts events from any build. Recording starts after the claim.
- If you cannot run the app yourself, ask the person to open it and click through a few screens.
- To look at the first sessions before the claim, use `mcp.url` with `mcp.token` from the trial answer (Bearer token). It reads the trial project only and expires 3 days after setup.

## 4. Tell the person what is left

Say this, in your own words:

- An email "A Sequential project was set up for this address" has the link to claim the trial. Claim it within 72 hours, with the same address. Unclaimed trials are erased.
- Until the claim: up to 100 sessions, website events only from their own computer, no recordings from the app, and no AI review of sessions.
- After the claim, it is an ordinary project: recordings and AI review start, and the live site can send events.
- Did not ask for this? The email has a link to delete the project.

## After the claim: read the data

This plugin also connects Claude Code to Sequential's MCP server (`https://api.usesequential.com/mcp`). After the person has an account, they open `/mcp`, choose **sequential** and **Authenticate**, then can ask, for example, "Using Sequential, what were the worst sessions this week?" Docs: https://www.usesequential.com/docs/connect-claude-code.md
