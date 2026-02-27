# AGENTS.md

## Cursor Cloud specific instructions

### Project overview

This is a lightweight monorepo with two components:

| Component | Path | Stack |
|-----------|------|-------|
| **Vox CLI** (core product) | `src/vox/`, `tests/` | Python 3.8+, Click, Pydantic, matrix-nio |
| **Marketing website** | `website/` | Next.js 16, React 19, Tailwind CSS 4, TypeScript |

### Running services

- **Python CLI**: After `pip install -e ".[dev]"`, the `vox` command is available at `~/.local/bin/vox`. Ensure `~/.local/bin` is on `PATH`.
- **Website dev server**: `cd website && npm run dev` (runs on port 3000).
- No databases, Docker, or external service setup required. The Matrix homeserver is remote at `https://80-225-209-87.sslip.io`.

### Testing

- **Python tests**: `pytest tests/ -v` (20 tests, all use mocks — no network required).
- **Linting**: `black --check src/ tests/`, `isort --check-only src/ tests/`, `flake8 src/ tests/`, `mypy src/`. Note: existing code has pre-existing style/type issues; these are not regressions.
- **Website lint**: `cd website && npx eslint .` (2 pre-existing warnings in Hero.tsx and Navigation.tsx).
- **Website build**: `cd website && npm run build`.

### Gotchas

- The `vox` CLI binary installs to `~/.local/bin/` (user install). You must `export PATH="$HOME/.local/bin:$PATH"` before using it.
- `mypy` config in `pyproject.toml` targets Python 3.8 which mypy no longer supports; it still runs but warns. This is a pre-existing issue.
- `vox send` now accepts raw Matrix IDs directly (e.g., `vox send "@user:matrix.org" "msg"`). It auto-saves the contact.
- The initial sync may print `Error validating response: 'room_id' is a required property` — this is a harmless nio/Conduit validation warning; messaging still works.
- The homeserver at `80-225-209-87.sslip.io` can be intermittently unreachable (TLS resets). If `vox init` or `vox send` fails with connection errors, wait and retry.
- `vox inbox` has a ~30s timeout when syncing with the Matrix homeserver.
- Website has `package-lock.json`, so use `npm` (not pnpm/yarn) for the website.
