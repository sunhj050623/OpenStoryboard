# OpenStoryboard

OpenStoryboard is an end-to-end text-to-image workflow for structured visual content creation.

It turns source text into production-ready image sets through a deterministic pipeline:

1. Analyze source content
2. Build a visual outline
3. Generate per-image prompts
4. Render images in batch
5. Export run artifacts and JSON reports

Supported use cases include image cards, infographics, comics, article illustrations, diagrams, and cover images.

## Key Features

- Multi-mode generation (`image-cards`, `infographic`, `comic`, `article-illustrator`, `diagram`, `cover-image`)
- Unified CLI and Web console
- LLM-assisted analysis/outline/prompt construction
- Pluggable image backends through `skills/t2i-imagine`
- Deterministic run artifacts under `runs/`
- Docker-ready deployment

## Repository Structure

```text
.
|-- openstoryboard/                # Core workflow package (CLI + engine + web)
|-- skills/                        # Prompting and mode-specific skill definitions
|   |-- t2i-imagine/               # Image rendering backend scripts
|   |-- t2i-image-cards/
|   |-- t2i-infographic/
|   |-- t2i-comic/
|   |-- t2i-article-illustrator/
|   |-- t2i-diagram/
|   `-- t2i-cover-image/
|-- runs/                          # Runtime outputs
|-- .env.example                   # Environment template
|-- docker-compose.yml
`-- Dockerfile
```

## Requirements

- Python 3.11+
- Node.js 20+ (required by `skills/t2i-imagine/scripts/main.ts`)
- Valid API credentials for text and image models

## Quick Start

### 1) Install dependencies

```bash
pip install -r requirements.txt
```

### 2) Create environment file

```bash
# PowerShell
Copy-Item .env.example .env

# Bash
cp .env.example .env
```

Edit `.env` and set your credentials.

Minimum required keys:

```env
OPENSTORYBOARD_TEXT_API_KEY=...
OPENSTORYBOARD_TEXT_BASE_URL=https://api.openai.com/v1
OPENSTORYBOARD_TEXT_MODEL=gpt-4o-mini

OPENAI_API_KEY=...
OPENAI_BASE_URL=https://api.openai.com/v1
OPENAI_IMAGE_MODEL=gpt-image-1
```

### 3) Inspect integrated skills/backend

```bash
python -m openstoryboard.cli inspect --project-root .
```

### 4) Run a workflow

```bash
python -m openstoryboard.cli run \
  --project-root . \
  --mode image-cards \
  --topic "How to design a reliable prompt pipeline" \
  --content-file ./example/input.md \
  --style minimal \
  --layout balanced \
  --palette warm \
  --image-count 4 \
  --aspect-ratio 3:4 \
  --quality 2k
```

## CLI Reference

### `inspect`

```bash
python -m openstoryboard.cli inspect --project-root .
```

Returns paths for modes and backend script availability.

### `run`

```bash
python -m openstoryboard.cli run --mode <mode> --topic <text> [options]
```

Required:

- `--mode`: one of
  - `image-cards`
  - `infographic`
  - `comic`
  - `article-illustrator`
  - `diagram`
  - `cover-image`
- `--topic`

Common options:

- `--project-root` (default: `.`)
- `--content`
- `--content-file`
- `--style`
- `--layout`
- `--palette`
- `--image-count` (default: `4`)
- `--aspect-ratio` (default: `3:4`)
- `--quality` (`normal` or `2k`, default: `2k`)
- `--provider`
- `--model`
- `--image-api-dialect`
- `--output-root` (default: `runs`)
- `--thread-id` (default: `openstoryboard-thread`)
- `--dry-run`
- `--no-generate`
- `--no-anchor-chain`
- `--fail-fast`
- `--skip-analysis-llm`
- `--skip-outline-llm`

## Web Console

Start locally with Uvicorn:

```bash
uvicorn openstoryboard.web.app:app --host 0.0.0.0 --port 8000
```

Then open `http://127.0.0.1:8000`.

## Docker

Build image:

```bash
docker build -t openstoryboard:latest .
```

Run container:

```bash
docker run --rm -p 8001:8000 -v %cd%:/app -w /app openstoryboard:latest
```

With compose:

```bash
docker compose up --build
```

## Output Artifacts

Each run writes to:

```text
runs/<mode>/<topic-slug>-<timestamp>/
```

Typical artifacts:

- `source-*.md`
- `analysis.md`
- `outline.md`
- `prompts/*.md`
- `*.png` (generated images)
- `report.json`

## Notes

- This repository was renamed to `OpenStoryboard` and Python module path is now `openstoryboard`.
- If you are migrating from old setup, update command paths, env keys, and local config folders accordingly.
