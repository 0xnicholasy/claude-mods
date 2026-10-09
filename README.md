<div align="center">
  <h1>claude-mods</h1>
  <p>A Claude Code plugin marketplace for mods that change how the terminal looks and behaves.</p>

  [![License](https://img.shields.io/github/license/0xnicholasy/claude-mods)](LICENSE) [![Stars](https://img.shields.io/github/stars/0xnicholasy/claude-mods?style=flat)](https://github.com/0xnicholasy/claude-mods/stargazers) [![Last commit](https://img.shields.io/github/last-commit/0xnicholasy/claude-mods)](https://github.com/0xnicholasy/claude-mods/commits/main)

  <a href="#install">Install</a> · <a href="#plugins">Plugins</a> · <a href="#requirements">Requirements</a> · <a href="#how-this-repo-works">How this repo works</a>
</div>

This repo is a catalog of three Claude Code plugins. Each plugin is written as function hooks (TypeScript) and lives in its own repo.

## Install

Add the marketplace, then install the plugins you want:

```
/plugin marketplace add 0xnicholasy/claude-mods
/plugin install agents-office@claude-mods
/plugin install todo-list@claude-mods
/plugin install collapse-tools@claude-mods
```

Run `/plugin` to browse and install from the interactive plugin manager instead.

The same operations are available from the shell as `claude plugin marketplace add|list|update|remove` and `claude plugin install|update|uninstall|list`.

Update the catalog and a plugin (restart Claude Code to apply):

```
/plugin marketplace update claude-mods
/plugin update todo-list@claude-mods
```

Uninstall a plugin or remove the marketplace:

```
/plugin uninstall todo-list@claude-mods
/plugin marketplace remove claude-mods
```

## Plugins

### agents-office

Version 0.1.0. A pane that shows the session's agents as pixel characters in an office. Run `/office` to open it. The pane draws an image office in terminals that show images (headless Chromium renders it) and a text office elsewhere.

```
/plugin install agents-office@claude-mods
```

Repo: [0xnicholasy/claude-mod-agents-rpg](https://github.com/0xnicholasy/claude-mod-agents-rpg)

![The office drawn as pixel art in a terminal that shows images](https://raw.githubusercontent.com/0xnicholasy/claude-mod-agents-rpg/main/docs/images/office-image-120x40.png)

### todo-list

Version 0.3.1. Claude's task plan as a tree in a pane and the status line, with live activity. Claude writes the plan through a plan tool, and state-changing tools wait until a plan exists. Run `/todo` to reopen the pane, clear the plan, turn enforcement on or off, or set the accent color.

```
/plugin install todo-list@claude-mods
```

Repo: [0xnicholasy/claude-mod-todo-list](https://github.com/0xnicholasy/claude-mod-todo-list)

![Todo List pane showing a plan tree](https://raw.githubusercontent.com/0xnicholasy/claude-mod-todo-list/main/docs/pane.png)

### collapse-tools

Version 0.2.0. Draws each tool call in the transcript as one line, `[+] Name  arg`. Click a row to expand it, or run `/collapse-tools` to toggle all calls. `/collapse-tools color <name|#hex|reset>` sets the color of finished calls.

```
/plugin install collapse-tools@claude-mods
```

Repo: [0xnicholasy/claude-mod-collapse-tools](https://github.com/0xnicholasy/claude-mod-collapse-tools)

<img src="https://raw.githubusercontent.com/0xnicholasy/claude-mod-collapse-tools/main/.claude/skills/collapse-tools/.claude-plugin/icon.png" alt="Collapse Tools icon" width="96" height="96">

## Requirements

- A Claude Code version that supports function-hook plugins. The plugin READMEs name the version their API types came from: agents-office 2.1.289, todo-list 2.1.289, collapse-tools 2.1.295 or later. Use a recent Claude Code.
- agents-office draws in the terminal surface only. Its image office also needs Node 20 or later and headless Chromium (about 150 MB, installed through Playwright). Chromium is optional: without it the office draws as text.

## How this repo works

This repo holds only the catalog, `.claude-plugin/marketplace.json`. It contains no plugin code. Each entry uses a `git-subdir` source that points at the plugin's own repo and the folder `.claude/skills/<plugin>` inside it. The plugin name in the catalog matches the `name` in that plugin's `plugin.json`.

## Contributing and issues

Open issues and pull requests in the repo of the plugin concerned:

- [claude-mod-agents-rpg issues](https://github.com/0xnicholasy/claude-mod-agents-rpg/issues)
- [claude-mod-todo-list issues](https://github.com/0xnicholasy/claude-mod-todo-list/issues)
- [claude-mod-collapse-tools issues](https://github.com/0xnicholasy/claude-mod-collapse-tools/issues)

Use [this repo's issues](https://github.com/0xnicholasy/claude-mods/issues) for problems with the catalog itself.

## License

This repo is MIT licensed, see [LICENSE](LICENSE). Each plugin is licensed in its own repo. All three plugins are MIT.
