# Sequential plugin for Claude Code

[Sequential](https://www.usesequential.com) is session replay and product analytics for websites and mobile apps. This plugin lets Claude Code:

- **Install Sequential** in a website or an iOS, Android or React Native app, before you have an account. Claude Code creates a trial with one call, adds the SDK and checks that events arrive. You claim the trial from an email. (Skill: `install-sequential`.)
- **Read what happened to your users** over Sequential's MCP server: findings, sessions, verdicts, errors, funnels and retention. (Server: `https://api.usesequential.com/mcp`.)

## Install

```
/plugin marketplace add usesequential/claude-plugin
/plugin install sequential@usesequential
```

Then ask, for example: "Add session replay to this app with Sequential."

## Connect to your data

After you have a Sequential account, open `/mcp` in Claude Code, select **sequential** and choose **Authenticate**. Your browser opens Sequential's consent page. See [Connect Claude Code to Sequential](https://www.usesequential.com/docs/connect-claude-code).

## What a trial can do before you claim it

Up to 100 sessions for 72 hours. A website trial accepts events only from your own computer (`localhost`); a mobile trial accepts events from any build, and recording starts after the claim. No AI review of sessions until the claim. Details: [Let your agent set up a trial](https://www.usesequential.com/docs/agent-trial).

## Links

- [Docs](https://www.usesequential.com/docs) · [Agent tools](https://www.usesequential.com/docs/agent-tools) · [Privacy Policy](https://www.usesequential.com/privacy) · [Terms of Service](https://www.usesequential.com/terms)
- Questions: hello@usesequential.com

MIT licensed.
