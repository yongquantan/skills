# agent-skills

Agent skills (the open `SKILL.md` format) by Yong Quan Tan / Kairos Labs.

## Skills

| Skill | What it does |
| --- | --- |
| [setup-devin-slack](skills/setup-devin-slack) | Set up the Devin Slack integration end-to-end — create the Slack account/workspace if needed, install the Devin app, link the user, verify with a live `@Devin` session. Covers the clean-room path and the undocumented traps (reCAPTCHA, taken workspace URLs, the OAuth picker defaulting to the wrong workspace, hidden composer editors, channel-invite requirement). |

## Install

```bash
npx skills add yongquantan/skills
```

Or a single skill:

```bash
npx skills add yongquantan/skills/skills/setup-devin-slack
```

These skills assume a browser-driving agent. `setup-devin-slack` uses [Playwriter](https://playwriter.dev) against the user's own Chrome/Chromium browser; equivalent computer-use tooling works with adaptation.

## License

MIT
