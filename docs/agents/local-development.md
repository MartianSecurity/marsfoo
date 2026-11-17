# Local development and evaluation

Use the repository's `.nvmrc`, `package.json` engines and lockfiles. With nvm installed, run `source ~/.nvm/nvm.sh` then `nvm use` before Node tools. The repository uses Node >=22.22.0 and npm 11 for its release-age policy (`engine-strict=true` in `.npmrc`); pnpm/yarn are supported alternatives. Use an appropriate task-local tool setup rather than globally upgrading npm during unrelated work.

A fresh worktree has its own dependencies. Use the repository's install procedure (`npm ci` with the supported npm version) before treating `ERR_MODULE_NOT_FOUND` as an application defect. Keep credentials outside test fixtures and do not print them. See [dependency management](dependency-management.md) for package changes.

## Servers

`npm run dev` starts the server/API and frontend; `npm run dev:server` and `npm run dev:app` start them individually. Expected URLs are `http://localhost:15500` and `http://localhost:3000/`; confirm startup output or listeners rather than assuming Vite's default port. Reuse a compatible running instance, or choose an available port for a task-owned instance; do not stop another task's server.

Keep an agent-started server attached to a live terminal session. Backgrounding with `&` or `nohup` can exit silently in agent shells. Do not run `npm run local -- view` unless explicitly asked: it serves the static production build without hot reload.

## Local evals

Run from the repository root and pass `--` before script flags:

```bash
npm run local -- eval -c path/to/promptfooconfig.yaml --no-cache -o output.json
```

Use `--env-file .env` or another user-designated path only when the eval needs credentials and the file exists. The ignored env file is not a prerequisite for credential-free checks. Never place secret values directly in commands, reports or source.

`--no-cache` gives fresh development results without deleting the user's cache. Inspect exported `success`, `score`, `error`, provider output, traces and relevant red-team findings. Exit code 0 means the configured `PROMPTFOO_PASS_RATE_THRESHOLD` was met, not that every case passed. Keep generated outputs outside tracked source unless the requested deliverable belongs there.

For debugging, add `--verbose` or set `LOG_LEVEL=debug`, observing the sanitization rules in [logging](logging.md). Inspect the failed request and persisted result rather than treating a scanner alert or generated payload as proof of a vulnerability.

## Local state

The default cache is `~/.promptfoo/cache`, configurable with `PROMPTFOO_CACHE_PATH` or `PROMPTFOO_CONFIG_DIR`. Do not clear it without authorization. The default SQLite database is `~/.promptfoo/promptfoo.db`; never delete or reset it during a check. Use isolated configuration/data folders and test helpers for reproductions, following the database instructions.
