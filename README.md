# LMCtrlF

A cross-platform desktop app for semantic search and AI chat over your local documents. Files are indexed and searched on-device, so they never leave your computer. Built as a team project for ENGG 400 at the University of Calgary (Feb–Apr 2026).

> This is my fork of the team repository [iBobbyTS/LMCtrlF](https://github.com/iBobbyTS/LMCtrlF). The section below describes my contributions.

## Features

- **Semantic search** over local documents (e.g. PDFs) using vector embeddings
- **AI chat** that answers questions using your indexed documents
- **Configurable model providers**: choose the base URL, embedding model and chat model
- **Local-first**: indexing and storage stay on your machine

## Tech stack

| Layer | Technology |
| --- | --- |
| Desktop app | Electron, React 19, TypeScript, Vite |
| Backend | Python, FastAPI, Uvicorn, Pydantic |
| Storage and search | SQLite (via Peewee ORM), LanceDB (vectors) |
| Document parsing | pypdf |
| Testing | Vitest, React Testing Library, pytest |

The repo is a pnpm monorepo: `apps/desktop` (Electron + React), `services/backend` (FastAPI) and `packages/shared`.

## My contributions

I worked on the desktop app's model settings, in `apps/desktop/src/App.tsx`:

- **Settings validation.** Built client-side validation for model provider settings. Provider name, base URL, embedding model and chat model are trimmed and checked before saving, with clear error messages instead of failed backend calls.
- **Embedding-model change guard.** Changing the embedding model makes every existing vector index incompatible, which would silently break search. I added a confirmation dialog that explains this and offers two paths:
  - **Revert**: restores the saved settings without changing anything
  - **Continue**: saves the new model, then automatically re-indexes every indexed document across all projects
- **Tests.** Wrote unit tests (Vitest + React Testing Library) covering the dialog appearing, the revert path, and the save-and-reindex path.

All changes went through the team's pull-request workflow.

## Running it locally

Requires Node.js 24+, pnpm and Conda. Full details are in [docs/setup.md](docs/setup.md).

```bash
# Python backend environment
conda env create -f environment.yml
conda activate lmctrlf-dev

# Node dependencies
corepack enable
pnpm install

# Start the app (Electron launches the FastAPI backend automatically)
pnpm dev:desktop
```

Indexing and chat use a local model server: start [LM Studio](https://lmstudio.ai)'s local server with an embedding model and a chat model loaded.

Run the frontend tests with:

```bash
pnpm test:desktop
```
