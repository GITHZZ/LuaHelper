---
name: plugin-version-sync
description: Enforces explicit version synchronization for LuaHelper VS Code plugin changes. Use when modifying the VS Code plugin, LuaHelper extension code, LSP backend, syntax highlighting, CHANGELOG, package version, server binary, or VSIX packaging.
---

# Plugin Version Sync

## When To Use

Use this skill before changing anything related to the LuaHelper VS Code plugin, including:

- VS Code extension code under `luahelper-vscode`
- Lua LSP backend code under `luahelper-lsp`
- TextMate syntax highlighting files
- Plugin settings in `package.json`
- `CHANGELOG.md`
- `luahelper-vscode/server/lualsp.exe`
- `.vsix` packaging or release output

## Mandatory Version Check

Before editing code, changing configuration, compiling binaries, or packaging the extension, confirm the target plugin version with the user.

If the user has not explicitly stated the target version, stop and ask:

```text
本次插件修改要同步到哪个版本？例如保持当前版本、提升 patch 版本，或指定为 0.2.xx。
```

Do not proceed with plugin code edits, version changes, server binary builds, or VSIX packaging until the user answers.

## Synchronization Checklist

Once the target version is confirmed, keep these files and outputs consistent when they are relevant to the change:

- `luahelper-vscode/package.json`: extension `version`
- `luahelper-lsp/langserver/lsp_server.go`: `clientVerStr`
- `luahelper-vscode/CHANGELOG.md`: entry for the target version
- `luahelper-vscode/server/lualsp.exe`: rebuild when Go LSP behavior changes
- `luahelper-vscode/*.vsix`: regenerate after version, extension, or server binary changes

## Workflow

1. Identify whether the request affects plugin code, LSP behavior, syntax highlighting, settings, versioning, changelog, or packaging.
2. Confirm the target version if it is not already explicit.
3. Make the scoped code/configuration changes.
4. Synchronize version numbers and changelog entries for the confirmed version.
5. If Go backend code changed, run `go test ./...` in `luahelper-lsp` and rebuild `luahelper-vscode/server/lualsp.exe`.
6. If packaging is requested or version/output changed, run `npm run package` in `luahelper-vscode`.
7. In the final response, report the confirmed version, changed sync points, validation commands, and generated VSIX path.

## Defaults

- For feature or bug-fix changes without a user-specified version, ask whether to bump the patch version.
- For source-only exploratory changes, still ask for the target version if the user expects an installable plugin output.
- Do not amend commits or push unless the user explicitly asks.
