# arxiv-cv-daily

`arxiv-cv-daily` is a Codex/OpenClaw skill plus a Python workflow for collecting arXiv `cs.CV` papers by release day, screening them against a user topic, and saving all artifacts under a chosen output directory.

## What It Does

- fetches arXiv papers for a target day in `cs.CV`
- filters papers by title and abstract against a topic or topic spec
- downloads PDFs only when full-text review is needed
- extracts text with `pdftotext`
- saves logs, manifests, screening results, PDFs, and extracted text for later analysis
- supports multi-day runs while avoiding duplicate resolved release days

## Repository Layout

- `SKILL.md`: skill contract and interaction rules
- `arxiv_cs_cv_daily.py`: launcher entry point
- `scripts/fetch_arxiv_cs_cv.py`: main workflow implementation
- `CHANGELOG.md`: change history

## Requirements

- Python 3.11+
- `pdftotext` installed and available on `PATH`
- write access to your chosen output root
- network access to `https://export.arxiv.org` and `https://arxiv.org`

## Usage

Run the launcher from the repo root:

```bash
python3 arxiv_cs_cv_daily.py \
  --topic "document understanding or OCR" \
  --date 2026-03-22 \
  --days 3 \
  --category cs.CV \
  --timezone Asia/Shanghai \
  --output-root /tmp \
  --output json
```

If you have a model-generated topic spec, pass it with `--topic-spec-file`:

```bash
python3 arxiv_cs_cv_daily.py \
  --topic "document understanding or OCR" \
  --topic-spec-file /tmp/topic_specs/document_understanding.json \
  --date 2026-03-22 \
  --days 3 \
  --category cs.CV \
  --timezone Asia/Shanghai \
  --output-root /tmp \
  --output json
```

If you omit `--output-root`, the workflow defaults to `/tmp`.

For bot or service deployments, the end user should not need to know the launcher path. Your backend should resolve that path from the installed skill location. If your product exposes an output directory choice, ask for `output_root` at the product layer; otherwise choose one server-side.

## Output Structure

Each run creates a directory under the chosen output root, for example:

```text
/tmp/2026-03-23_103000_cs-cv_document-understanding-or-ocr_2026-03-22
```

Typical contents:

- `01_request.json`
- `07_manifest.json`
- `07_summary.md`
- `activity.log`
- `days/YYYY-MM-DD/02_all_papers.json`
- `days/YYYY-MM-DD/03_title_screening.json`
- `days/YYYY-MM-DD/04_abstract_screening.json`
- `days/YYYY-MM-DD/05_matched_papers.json`
- `days/YYYY-MM-DD/06_downloads_and_text.json`
- `days/YYYY-MM-DD/downloads/*.pdf`
- `days/YYYY-MM-DD/extracted_text/*.txt`
- `cache/arxiv_api/<category>/<date>.json`

## Notes Before Publishing

- The workflow defaults to `/tmp`, but you can override that with `--output-root`.
- The skill is designed for `cs.CV` by default, though the script accepts `--category`.
- `pdftotext` is an external dependency and should be called out in any public documentation.
- If you publish this repo, add a license that matches how you want others to reuse it.
