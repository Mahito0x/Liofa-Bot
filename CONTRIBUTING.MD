# Contributing to Liofa

Thanks for wanting to contribute. This document explains how to set up a local dev environment, test changes, and add new commands or events in a way that works with the code already in the repository.

## Quick summary (TL;DR)
- Node.js >= 18
- Add a `.env` file with `BOTTOKEN` (and optionally `BOTADMIN`, `TESTINGSERVER`)
- Install deps: `npm install`
- Run locally: `node start.js`
- Commands live in `./Commands`, events in `./events`, shared helpers in `functions.js`

## 1. Prerequisites
- Node.js 18.x (the project was developed and tested on Node 18).
- Git and a GitHub account (for PRs).

## 2. Local setup

1. Clone the repo and install dependencies:

```pwsh
git clone <repo-url>
cd Liofa-Bot
npm install
```

2. Create a `.env` in the project root with at least the bot token:

```text
# .env (example)
BOTTOKEN=your_bot_token_here
# Optional
BOTADMIN=your_user_id
TESTINGSERVER=your_test_guild_id
```

3. Create `Server Data/` directory (the bot will create it automatically when needed, but you can pre-create it during development):

```pwsh
mkdir "Server Data"
```

4. Run the bot:

```pwsh
node start.js
```

If you want to avoid committing secrets, make sure `.env` and `Server Data/` are ignored by git (this repo's `.gitignore` already includes them).

## 3. Project structure (relevant parts)
- `start.js` — bot entrypoint. It registers commands from `./Commands` and events from `./events`.
- `Commands/*.js` — individual command modules. Each command should export at least `data` and `execute`.
- `events/*.js` — event handlers (e.g., `interactionCreate`, `ready`).
- `functions.js` — shared helper functions (language detection, config read/write, permissions, etc.).
- `Read Only/Settings.json` — template used to create per-server config files in `Server Data/`.

## 4. How to add or modify a command

Commands follow the pattern used in existing files (see `Commands/help.js`). Minimum template:

```javascript
const { SlashCommandBuilder } = require('@discordjs/builders');

module.exports = {
   data: new SlashCommandBuilder()
      .setName('example')
      .setDescription('Describe the command'),

   everyone: true, // or false — used by the permission system

   async execute(interaction) {
      // your command code here
   }
};
```

Notes:
- Use `functions.liofaRead(interaction.guild.id)` to load per-server config.
- Update `Read Only/Settings.json` only when you intend to change default values for new servers. Existing servers' files in `Server Data/` are not changed automatically.

### Buttons and Select Menus
Commands can also export `buttons` and `menu` objects with named handlers. `events/interactionCreate.js` routes button/select interactions to `command.buttons[name]` and `command.menu[name]`.

## 5. How to add or modify an event

Events should export a `name` and `execute` function (see `events/interactionCreate.js`). When adding a new file, follow the shape already used in this folder. `start.js` loads all files from `./events` and registers them.

## 6. Testing changes
- Start the bot locally with `node start.js` and use a testing guild (set `TESTINGSERVER` in `.env` if you want special behavior for the developer).
- Use the bot in Discord to exercise commands and interactions.

## 7. Code style and linting
- The project uses ESLint (devDependency). Run `npx eslint .` to check for issues.
- Keep code style consistent with existing files: semicolons, indentation, and the CommonJS module format.

## 8. Commit and pull request process
1. Create a branch for your feature/bugfix:

```pwsh
git checkout -b feature/your-feature-name
```

2. Make focused commits with clear messages. Example:

```pwsh
git add Commands/example.js
git commit -m "feat(command): add example command"
```

3. Push your branch and open a PR against `main`.

When opening a PR, include:
- A short description of the change
- Any setup steps required to test it
- Screenshots or example interactions if relevant

## 9. Security and sensitive data
- Never commit tokens, private keys, or server-specific JSON files from `Server Data/`.
- Use `.env` for secrets and ensure it's in `.gitignore`.

## 10. Troubleshooting
- If the bot errors on startup with ENOENT for `Server Data/...`, ensure the `Server Data/` directory exists or let the bot create it when it joins a server (the code auto-creates the directory in `functions.liofaJoin`).
- If you see permission errors, confirm the bot has required channel permissions (ViewChannel, SendMessages, ManageMessages).

## 11. Questions
If something is unclear, open an issue describing what you're trying to do and attach relevant logs or screenshots.

Thanks for helping improve Liofa! 🎉