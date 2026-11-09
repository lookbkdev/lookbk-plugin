# Lookbk

Find a visual agent skill on [Lookbk](https://lookbk.dev), see what it really renders, and install the one you pick.

Lookbk is a catalog of agent skills that make a finished visual result on your machine: launch films and motion video, website heroes and landing pages, slide decks, and link-preview (OG) images. Each skill has a page with the result it actually rendered, so you can judge the look before you install anything.

This plugin adds one skill and connects the read-only Lookbk catalog server. Ask Claude something like "make a launch video for my app with a Lookbk skill" or "find a Lookbk skill for a pitch deck", and it will:

1. ask what you want to make and what material you have,
2. search the catalog and show you two or three picks, each with its preview page on lookbk.dev and whether it is free or Pro,
3. check the one you choose against your request (what it needs, what it is not for),
4. show you the exact install command for that skill and explain what it does,
5. run the command only after you say yes, then tell you to start a new session so the skill loads.

## Install

In Claude Code:

```
/plugin marketplace add lookbkdev/lookbk-plugin
/plugin install lookbk@lookbk-plugin
```

Once the plugin is listed in Anthropic's plugin directory, you can also add it from the directory on claude.ai.

## What this plugin contains, sends, and runs

- **Contains:** one Markdown skill (`skills/lookbk/SKILL.md`), the manifest, an MCP configuration file, this README, the license, and the icon. There are no scripts, hooks, or executables in the plugin.
- **Connects to:** the Lookbk MCP server at `https://lookbk.dev/api/mcp` (streamable HTTP, no login). It has four read-only tools: `search_skills`, `get_skill`, `list_video_skills`, and `get_install_command`. Claude sends it your search words and the slug of the skill you look at. It does not receive your files or code. The same server is listed as the Lookbk connector in Anthropic's directory.
- **Runs:** nothing on its own. When you pick a skill, Claude shows you the one-line install command returned by `get_install_command`. If you approve, Claude runs it in your terminal. That command uses Node.js 18 or newer to download the installer from lookbk.dev, which then downloads that one skill package from lookbk.dev and copies it into your skills folder, such as `~/.claude/skills/`. Free skills need no account. Pro skills ask you to sign in to Lookbk in your browser, and the installer then keeps your Lookbk sign-in in `~/.config/lookbook/auth.json` (readable only by you). Running the command never charges anything.

Privacy policy: https://lookbk.dev/privacy. Terms: https://lookbk.dev/terms.

## Support

Open an issue at https://github.com/lookbkdev/lookbk-plugin/issues or email support@lookbk.dev.

## License

MIT. See [LICENSE](LICENSE).
