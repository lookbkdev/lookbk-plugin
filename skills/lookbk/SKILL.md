---
name: lookbk
description: Find and install a Lookbk visual skill. Use when the user wants to make a launch video, product film, kinetic title, website hero, landing page, slide deck, OG or link-preview image, or diagram "with a Lookbk skill", asks to "find a Lookbk skill", or asks which skill can render a visual result for them. Searches the Lookbk catalog, shows the real rendered previews, and installs the chosen skill only after the user approves the exact command.
---

# Lookbk: find a visual skill and install it

Lookbk (lookbk.dev) is a catalog of agent skills that render a finished visual result on the user's machine: launch films and motion video, website heroes and landing pages, slide decks, and link-preview images. Every skill has a page that shows what it actually rendered, so the user can judge the look before installing anything.

This skill uses the `lookbk` MCP server that comes with this plugin. The server is read-only: it searches the catalog and returns information. It never installs anything by itself. Installing happens only when you run a command in the user's terminal, and only after the user says yes.

## Steps

### 1. Understand what the user wants to make

Before searching, pin down three things, asking at most one short question if they are unclear:

- **The result**: a video, a slide deck, an image, or a web page, and what it is for (a product launch, a pitch, a blog post, a waitlist).
- **The material**: what the user already has, such as a product URL, screenshots, a README, notes, or a logo.
- **Constraints**: aspect ratio or length for video, and whether they only want free skills.

### 2. Search the catalog

Call `search_skills` with the request in plain words, for example `query: "product launch video"`. Narrow it when it helps: `output` is one of `video`, `slides`, `image`, `web`, and `free: true` returns free skills only. For a broad video request you can also call `list_video_skills`.

Each result has a `slug`, a `name`, a one-line `summary`, `access` (`free` or `pro`), and a `url` for its preview page.

### 3. Show the user two or three picks

Choose the two or three results that fit the request best and present each one as:

- the name and the one-line summary,
- free or Pro,
- the preview page, written as `https://lookbk.dev/skills/<slug>`, where the user can watch or view the real rendered result.

Say in one line why each pick fits. Ask the user to open the preview pages and choose one. Do not install anything at this step.

### 4. Check the chosen skill fits

Call `get_skill` with the chosen slug. Read what the skill makes, what it needs on the user's machine (for example Node.js, a browser, or FFmpeg), what is in the package, and what it is **not** for. If the skill does not fit the user's request or setup, say so plainly and go back to step 3 with another pick.

### 5. Get the install command and show it

Call `get_install_command` with the slug. When you are running in Claude Code, pass `agent: "claude-code"`. Leave `scope` out to install into the user's home folder (every project sees the skill), or pass `scope: "project"` if the user wants it only in the current project.

Show the user the exact command the server returned, unchanged, in a code block, and explain in plain words what it does:

- It uses Node.js to download the Lookbk installer from lookbk.dev, which downloads that one skill package from lookbk.dev and copies it into the user's skills folder, such as `~/.claude/skills/`.
- Free skills install with no account or sign-in.
- Pro skills need a Lookbk account: the command prints a sign-in link if the user is not connected, and an upgrade link if their plan does not include the skill. Running the command never charges anything.

Then ask: "Do you want me to run this command?"

### 6. Run it only after the user says yes

- If the user says yes and you can run terminal commands, run exactly the command from step 5 and report its output.
- If the user says no, or you cannot run terminal commands here, give them the command to run in their own terminal and stop.
- Never change the command, never run any other install command, and never run it before the user has agreed.

### 7. Tell the user how to start using it

When the install finishes, tell the user to start a new session so the agent loads the new skill, and give them a first prompt to try based on what they told you in step 1.

## Troubleshooting

- **The `lookbk` tools are missing or the MCP server is not connected.** Check that the plugin is enabled, then reconnect the `lookbk` server in `/mcp`. The server address is `https://lookbk.dev/api/mcp` and needs no login. If it still fails, send the user to `https://lookbk.dev/skills` to browse; every skill page shows its own install command.
- **The command is blocked, or Node.js is not installed.** The install command needs Node.js 18 or newer. If the user's permission settings block it, do not work around them: show the command and let the user run it in their own terminal. If Node.js is missing, tell the user to install it from nodejs.org first.
- **A Pro skill asks the user to sign in.** This is expected. The user opens the printed link, signs in to Lookbk, and runs the same command again. Do not ask the user to paste passwords, tokens, or keys into the chat.
- **No result fits.** Say so honestly instead of pushing the closest match, and point the user to `https://lookbk.dev/skills` to browse by category.
