# claude-mods

Parent folder for Claude Code mod projects. Each mod is its own project in a sibling folder named `claude-mod-<name>`, with its own git repo and its own GitHub repo. This parent folder is itself the marketplace repo (`0xnicholasy/claude-mods`, catalog at `.claude-plugin/marketplace.json`). It lists each mod from its own repo; mods are not merged here. This file tells a session how to set up a NEW mod so it matches the existing ones. Existing mods are the reference: copy their shape, do not invent a new one.

## Layout

- `claude-mods/claude-mod-<name>/` is one git repo. Default branch is `main`.
- Create the GitHub repo private by default: `gh repo create 0xnicholasy/claude-mod-<name> --private --source . --push`.
- A mod's code lives at `.claude/skills/<mod>/`, where `<mod>` is the plugin name (for example `agents-office`).

## Scaffold checklist

Create these files in the new repo:

- `.claude/skills/<mod>/.claude-plugin/plugin.json`: `{ "name": "<mod>", "version": "0.1.0", "description": "<one line>", "types": "./types/index.d.ts" }`
- `.claude/skills/<mod>/hooks/hooks.json`: `{ "modules": ["./register.tsx"] }`
- `.claude/skills/<mod>/hooks/register.tsx`: exports `register: Register`, importing `atom`, `read`, `update` and `type Register` from `'claude-code'`. Atoms are declared as `atom({ plugin: '<mod>', key: '<key>' } as const, initial)`.
- `.claude/skills/<mod>/types/index.d.ts`: `declare module 'claude-code' { interface PluginState { '<mod>': { ...atoms } } }`. Declare every atom type inline; `claude plugin validate` rejects a PluginState entry that points at a type alias.
- `package.json`: `name`, `"private": true`, devDependency `typescript` `^5.9.3`, and scripts:
  - `typecheck`: `tsc -p tsconfig.json`
  - `validate`: `claude plugin validate .claude/skills/<mod>`
  - `test`: `claude plugin test .claude/skills/<mod>`
  - `check`: `npm run validate && npm run typecheck && npm run test`
- `tsconfig.json`: target and lib `es2023`, `types: []`, module `esnext`, moduleResolution `bundler`, `strict`, `noImplicitAny`, `noUncheckedIndexedAccess`, `noEmit`, `skipLibCheck`, `jsx: react` with `jsxFactory: h` and `jsxFragmentFactory: Fragment`. `include`: `vendor/claude-code/claude-code.d.ts`, `.claude/skills/<mod>/hooks/**/*`, `.claude/skills/<mod>/types/**/*`, `.claude/skills/<mod>/**/*.test.ts`.
- `.gitignore`: `node_modules/`, `.claude/worktrees/`, `.claude/skills/<mod>/.claude-plugin/types/`, `*.log`.
- `.github/workflows/ci.yml`: runs on push and pull_request for `main` and `feat/**`; one `typecheck` job on `ubuntu-latest` with `actions/checkout@v4`, `actions/setup-node@v4` (node 22, `cache: npm`), `npm ci`, `npm run typecheck`. CI runs typecheck only because validate and test need the local claude CLI.
- `CLAUDE.md`: copy the template below.
- `README.md`: title, how to run it, what it does, requirements, develop (`npm run check`), known limits.
- Marketplace entry: add the mod to `.claude-plugin/marketplace.json` in the parent folder (see Marketplace).

## Marketplace

- Every new mod gets an entry in `.claude-plugin/marketplace.json` as part of setup.
- Use a `git-subdir` source: `url` = the mod repo `.git` URL, `path` = `.claude/skills/<mod>`. The plugin is not at the repo root, so a plain `github` source would point at the wrong directory.
- `name` must equal the `name` in the mod's `plugin.json`.
- Copy `description` and `version` from `plugin.json`; bump the entry when the plugin version bumps.
- No relative `./` sources: mods live in other repos.
- Do not list duplicate clones (for example `claude-mod-agents-rpg-v3`).
- After editing the catalog, run `claude plugin validate .` from the parent folder.

## API types

- Load the `plugin-authoring` skill before writing any hook.
- Vendor the skill's `types/claude-code.d.ts` to `vendor/claude-code/claude-code.d.ts`. Never edit it; regenerate it by loading the skill again.
- Record the Claude Code version the types came from in the project `CLAUDE.md`.

## Shared rules

- No emoji in code.
- No `any` or `unknown` without a comment that justifies it.
- Never silence a TypeScript error with `// eslint-disable`.
- State lives in `$.state` atoms declared in `types/index.d.ts`, never in module variables.
- Put pure logic in its own module under `hooks/` with a `*.test.ts` beside it (tests import from `'claude-code/testing'`). `register.tsx` only wires hooks to that logic.
- `npm run check` is the gate. Validate and test need the local claude CLI; CI runs typecheck only.

## Running a mod

- Load it in a session: `claude --plugin-dir <repo>/.claude/skills/<mod>`.
- Or hot reload in a running session, following the `plugin-authoring` skill.

## Per-project CLAUDE.md template

```markdown
# claude-mod-<name>

<One sentence: what the mod shows or does.> Written in TypeScript (TSX) as a plugin of function hooks that hot-reloads in a session.

## Stack and commands

- Package manager: npm. Dev dependency: TypeScript 5.x.
- Mod path: `.claude/skills/<mod>/` (manifest in `.claude-plugin/plugin.json`, hooks in `hooks/`, state contract in `types/index.d.ts`).
- Claude Code version the API types came from: <x.y.z>. The API declarations are vendored at `vendor/claude-code/claude-code.d.ts`; never edit that file, regenerate it by loading the plugin-authoring skill.
- `npm run check` is the gate. It runs `validate` (`claude plugin validate`), `typecheck` (`tsc -p tsconfig.json`) and `test` (`claude plugin test`). Validate and test need the claude CLI and run locally only; CI runs typecheck.

## Rules

- No emoji in code.
- No `any` or `unknown` without a comment that justifies it.
- Never silence a TypeScript error with `// eslint-disable`.
- State lives in `$.state` atoms declared in `types/index.d.ts`, never in module variables.

## Delivery

- Build with a `sonnet` implementation agent.
- Tests: `npm run check`.
- Docs to update: `README.md`.
```

## Current mods

- `claude-mod-agents-rpg`: Agents Office pane, shows the session's agents as pixel characters.
- `claude-mod-todo-list`: Todo pane, tracks Claude's TodoWrite/Task list and nudges to create one for non-trivial tasks.
- `claude-mod-collapse-tools`: collapses each tool-call row to one line; click a row or run `/collapse-tools` to expand.
