# Cordis Memory with DeepSeek Harness Integration

[← 桌面端与安装包](https://github.com/Missher12/Missher-DeepseekHarness-Desktop) · [全部插件](https://github.com/Missher12/Missher-DeepseekHarness-Desktop/blob/main/plugins/README.zh.md) · [通用安装指南](https://github.com/Missher12/Missher-DeepseekHarness-Desktop/blob/main/docs/cookbook/install-cordis-plugins.zh.md)

## 新手上手：项目记忆

按项目保存、审核和检索长期记忆，恢复项目决定、进度、经验与下一步。

| 你需要知道的事 | 说明 |
| --- | --- |
| 插件包名 | `dsh-missher-memory` |
| 当前源码版本 | `0.3.1` |
| 装好后在哪里使用 | 设置 → 项目记忆；Agent 的 memory_search 工具 |
| 下载 / 源码 | [下载 0.3.1 安装包](https://github.com/Missher12/Missher-DSH-Memory/releases/tag/v0.3.1) |

### 安装、启用与第一次使用

1. 先从[桌面端主页](https://github.com/Missher12/Missher-DeepseekHarness-Desktop)下载适合电脑的应用，完成模型配置。这个仓库是可选插件，不是独立桌面应用。
2. 阅读[通用安装指南](https://github.com/Missher12/Missher-DeepseekHarness-Desktop/blob/main/docs/cookbook/install-cordis-plugins.zh.md)及本页原有安装说明，核对宿主与插件版本。桌面版使用“插件 → 添加插件”；Web/CLI 使用自己的目标配置组，不混用两种安装位置。
3. 安装后按宿主提示启用并重新加载，进入上表列出的入口。更新已有插件前保留配置和数据，不同时启用旧包名与新包名。
4. 先绑定一个项目并检查来源，再用 memory_search 查询。待审核候选不会自动当成已审核记忆。

### 使用前了解这些边界

这是独立记忆项目，当前说明中的验证基线是 Harness 0.1.5-rc.2 / Cordis 4.0.2；不能据此承诺当前桌面全部兼容。

如果页面或功能没出现，先检查当前应用版本、插件是否启用以及加载错误。反馈时附版本、复现步骤和已脱敏错误；不要上传 API Key、真实会话、账号 Cookie 或学习数据库。Git 中的代码更新不会自动替换电脑上已安装的插件。

### 继续阅读

下文保留本插件的详细行为、配置、开发和验证说明。跨平台是否实际通过，以对应版本的验证记录为准；桌面安装包能启动，不代表全部插件和外部服务都已验收。

---

English | [中文](README.zh.md)

`0.3.1` targets official Harness `0.1.5-rc.2` / Cordis `4.0.2` and provides project-scoped memory reviewed before search. Install the fixed-version package; the [installation and acceptance guide](INSTALL.md) covers DSH Market, the one-line command, and an Agent setup prompt.

[![Cross-platform Harness verification](https://github.com/Missher12/Missher-DSH-Memory/actions/workflows/cross-platform.yml/badge.svg)](https://github.com/Missher12/Missher-DSH-Memory/actions/workflows/cross-platform.yml)

`dsh-missher-memory` is an independently installable DeepSeek Harness bundle for recovering architecture, decisions, progress, failed approaches, and next steps in long-running projects. The current package includes indexed recall and reversible duplicate consolidation. It neither changes Harness core nor copies or modifies an existing legacy database.

For a fresh computer, use [the Cordis service guide](CORDIS.md) for a reusable `dsh-missher-memory/core` plugin, or [the Harness Agent guide](AGENT.md) for the existing Bundle. Core provides `missherMemoryService` without Harness services. Brain is optional and only controls Harness automatic recall.

## Install

Prerequisites: official DeepSeek Harness `0.1.5-rc.2`, `dsh` on PATH, and Node `^22.19.0` or `>=24.0.0`. This package was verified with Cordis `4.0.2`; other host versions need separate acceptance. Python, native builds, Brain, and a legacy database are not required. Brain only enables optional automatic recall.

In DSH Market, search for `dsh-missher-memory`, confirm the repository is `Missher12/dsh-missher-memory`, and inspect the listed version before installing. The market uses a separately maintained catalog; if it still lists `0.3.0-cordis.0`, use the fixed `0.3.1` command below.

```sh
dsh plugin --profile web add https://github.com/Missher12/Missher-DSH-Memory/releases/download/v0.3.1/dsh-missher-memory-0.3.1.tgz
```

[Download the 0.3.1 package](https://github.com/Missher12/Missher-DSH-Memory/releases/download/v0.3.1/dsh-missher-memory-0.3.1.tgz) (176873 bytes; SHA-256: `5bde1f688d6791954d890e2b958775abbdaffe06532464c6b23de362cb06ed49`). For offline installation, replace the URL with the downloaded file path.

After installing, run `dsh --profile web --dump-config` and confirm both `dsh-missher-memory` and `missher-memory`. Restart Harness using the same `web` profile. Configuration proves composition only; continue with Settings → Project Memory, explicit binding, and a real `memory_search` tool call.

One sentence for your Agent:

> Follow https://github.com/Missher12/Missher-DSH-Memory/blob/main/INSTALL.md to check host compatibility, install the fixed 0.3.1 package in the current web profile, help me confirm the project binding in Settings → Project Memory, and call memory_search to verify activation while preserving existing data.

A new installation needs no `vectors.db`; binding initializes `$DSH_HOME/missher-memory/state.db`. For an existing read-only legacy source, follow the [installation guide](INSTALL.md).


## Platform support

The Core is pure JavaScript and imports only Node built-ins at runtime; the Harness adapter uses Host peer packages. CI is configured to build one canonical `.tgz` and verify the same bytes on macOS Intel, macOS Apple Silicon, Windows x64, and Linux x64. The checks cover Cordis containers, package safety, CLI install/uninstall, and synthetic data. The CLI matrix is pinned to DeepSeek Harness Desktop 0.3.6 / Harness 0.1.1-rc.2. A configured matrix is not evidence that an unpublished candidate passed every platform; consult the current delivery report.

Windows ARM and Linux ARM are not claimed until stable native runners and a shipped Harness target are available. There is no platform-specific database payload or native addon inside the package.

## How it works

- `memory_search` searches external memory and reviewed plugin memory only through the active session's confirmed project binding, with source, time, and a stable reference on each result.
- cwd creates an in-memory binding candidate only. Durable state keeps an irreversible project key, basename, short hash, and encrypted external session identifiers; it never stores an absolute cwd.
- Project memory and personal preferences use separate scopes. Project search cannot read another project, and personal search does not read the external project database.
- Candidate capture defaults on for newly bound projects. Session disposal creates review candidates only after the user explicitly binds the project; candidates never become approved memory automatically.
- Automatic recall defaults on for newly bound projects. It contributes reviewed atoms, reversible capsules, and optional legacy rows to the Desktop Brain Hub; the Hub is the only component that appends one visible, source-attributed recall message.
- Old, unpinned, exact duplicate reviewed atoms are consolidated automatically after seven days. Sources are archived rather than deleted, and rolling back a capsule restores every source and FTS row exactly.
- Missing, damaged, unsafe, or timed-out databases return stable states and fail open without blocking Harness startup or a session.

## First binding

1. Open a top-level session in the target project so Settings shows its basename and short-hash candidate.
2. Review the source list, which contains record counts and time ranges but no record text, and select only sources that belong to the project.
3. Confirm a new binding or link another worktree candidate to an existing project.
4. Candidate capture and automatic recall start enabled for a newly bound project and remain independently switchable. Existing project settings are never migrated or overwritten.

The legacy database has no trustworthy project id. The plugin never classifies sources from cwd, text similarity, or time. A wrong source choice assigns history to the wrong project, so the first binding requires human review.

## Search

The model or user can explicitly call:

```text
memory_search({ query: "packaged smoke", scope: "project", limit: 5 })
```

A newly bound project should return `status: "ready"` with empty `results`; this proves an actual tool call, not stored memory. Complete binding if it returns `project-unbound`; inspect the profile and host services if the tool is absent. See [INSTALL.md](INSTALL.md) for a synthetic capture, approval, and retrieval check.

`scope` is `project` or `personal`. The query is interpreted as literal text rather than FTS operators, and results are bounded by count and UTF-8 bytes. Search never creates `state.db` and never triggers candidate capture.

## Candidate review and recall

After capture is enabled, the plugin buffers only direct user and assistant text from top-level sessions; it ignores tool output, plugin injections, and delegated sessions. If any message matches a credential, private key, connection string, identity number, financial number, or sensitive user path, the entire session produces no candidate.

Settings lets the user edit, merge, approve, pin, or forget candidates. Only approved memories can be searched or recalled; pinning changes order only. Project deletion removes that project's bindings, settings, candidates, project memory, and personal memory derived from its candidates without touching the external database.

Automatic recall uses only reviewed content and explicitly bound external sources. It has an independent switch, result limit, and byte budget. Errors, timeout, or invalid state contribute nothing. The optional legacy reader code is bundled, but no legacy database, user state, credential, or path is included in the package.

## Data and uninstall

Plugin-owned state lives under `$DSH_HOME/missher-memory/`, primarily in permission-restricted `state.db` and a local key. Candidate and approved text remain readable in `state.db`; project aliases are irreversible digests and external session identifiers are encrypted with the local key. `DATA-RETENTION.md` defines the complete retention rules and `SECURITY.md` defines the threat model.

Optionally export project memory before uninstalling. Keep project data if you want reinstall recovery:

```sh
dsh plugin --profile web remove dsh-missher-memory
dsh --profile web --dump-config
```

Uninstall removes the bundle and profile patch but preserves `$DSH_HOME/missher-memory/` by default for reinstall recovery. After confirming backups and retention requirements, the user may delete that exact directory. Do not delete or move the external `vectors.db`.

## Status reference

- `not connected (optional)`: the legacy directory or `vectors.db` is absent; built-in project memory remains available and the plugin does not create an external database.
- `unsafe path`: the directory, database, or plugin state is a link, a non-regular file, or fails containment checks.
- `incompatible`: the external tables, FTS5 schema, or plugin state schema is unsupported.
- `corrupt`: SQLite cannot validate the database read-only.
- `timeout`: the Worker was terminated and will be rebuilt for the next search.

Before distribution, run:

```sh
node scripts/verify-package.mjs dist/dsh-missher-memory-0.3.1.tgz
node scripts/native-smoke.mjs --archive dist/dsh-missher-memory-0.3.1.tgz
```

`native-smoke.mjs` uses synthetic data only. Passing `--cli /absolute/path/to/dsh-cli.js` additionally installs, composes, and removes the tarball in a temporary profile.

## Previous Cordis prerelease

`0.3.0-cordis.0` is a prerelease. Download the prebuilt package and check version-specific verification in [the release notes](https://github.com/Missher12/Missher-DSH-Memory/releases/tag/v0.3.0-cordis.0). Store inclusion is separate and depends on the curated registry accepting the entry.

The Cordis upgrade removes the mandatory Brain dependency. The earlier 117-test maintenance evidence below predates this upgrade; see CORDIS.md and the current delivery report for fresh verification. CLI install/remove evidence is separate from runtime activation. The packaged smoke reports `runtimeMode: cordis-with-synthetic-host-services` and `realHostActivationVerified: false`, and checks reinstall restoration using temporary synthetic data. No real Desktop Brain or UI acceptance is claimed by this smoke.

Reviewed memory remains untrusted historical data, never new authorization. Forgetting removes derived atoms and capsules, while retaining the forgotten candidate for review history. Project JSON export currently excludes capsules and archived atoms and is not a full backup.
