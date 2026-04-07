---
name: nighthawk-pdf-export
description: Export Nighthawk markdown reports to PDF on macOS. Use this skill when the user asks to generate, export, or produce a PDF from a Nighthawk notes file.
---

# Nighthawk PDF Export

Converts a Nighthawk markdown report (`notes/Nighthawk-*.md`) to a styled PDF.

## Tool Detection Order

Always probe before choosing an approach:

```bash
which pandoc wkhtmltopdf md-to-pdf 2>/dev/null
python3 -c "import weasyprint" 2>/dev/null && echo "weasyprint: ok" || echo "weasyprint: not installed"
```

## Option A — pandoc (preferred, if installed)

```bash
pandoc "notes/Nighthawk-YYYY-MM-DD-topic.md" \
  -o "notes/Nighthawk-YYYY-MM-DD-topic.pdf" \
  --pdf-engine=wkhtmltopdf \
  -V geometry:margin=1in -V fontsize=11pt
# fallback engines in order: xelatex, pdflatex
```

## Option B — Python + weasyprint (macOS fallback)

macOS ships with an externally-managed Python. **Do not use `pip3 install` directly** — it fails with PEP 668. Always use a venv:

```bash
python3 -m venv /tmp/pdf-venv
/tmp/pdf-venv/bin/pip install --quiet markdown weasyprint
```

Then convert (substitute `INPUT` and `OUTPUT` paths):

```bash
/tmp/pdf-venv/bin/python3 - <<'EOF'
import markdown, pathlib
from weasyprint import HTML

INPUT  = "/Volumes/Extreme SSD/src/project-nighthawk/notes/Nighthawk-YYYY-MM-DD-topic.md"
OUTPUT = "/Volumes/Extreme SSD/src/project-nighthawk/notes/Nighthawk-YYYY-MM-DD-topic.pdf"

CSS = """
body { font-family: Georgia, serif; font-size: 11pt; line-height: 1.6; padding: 40px; max-width: 900px; }
h1 { font-size: 20pt; border-bottom: 2px solid #333; padding-bottom: 8px; margin-top: 24px; }
h2 { font-size: 15pt; border-bottom: 1px solid #666; margin-top: 20px; }
h3 { font-size: 13pt; margin-top: 16px; }
code { font-family: 'Courier New', monospace; background: #f4f4f4; padding: 2px 4px; border-radius: 3px; font-size: 9pt; }
pre { background: #f4f4f4; padding: 12px; border-radius: 4px; border-left: 3px solid #666; page-break-inside: avoid; }
pre code { background: none; padding: 0; }
table { border-collapse: collapse; width: 100%; margin: 12px 0; page-break-inside: avoid; }
th { background: #333; color: white; padding: 8px; text-align: left; }
td { padding: 6px 8px; border: 1px solid #ccc; }
tr:nth-child(even) { background: #f9f9f9; }
blockquote { border-left: 4px solid #999; padding-left: 16px; color: #555; margin: 12px 0; }
a { color: #0066cc; }
"""

md_text = pathlib.Path(INPUT).read_text(encoding="utf-8")
body = markdown.markdown(md_text, extensions=["tables", "fenced_code", "toc"])
html = f"<!DOCTYPE html><html><head><meta charset='utf-8'><style>{CSS}</style></head><body>{body}</body></html>"
HTML(string=html).write_pdf(OUTPUT)

size = pathlib.Path(OUTPUT).stat().st_size
print(f"PDF written: {OUTPUT} ({size:,} bytes / {size//1024} KB)")
EOF
```

## Notes

- **Mermaid diagrams** (```` ```mermaid ```` blocks) render as code blocks in the weasyprint path — acceptable for sharing. Use `md-to-pdf` (puppeteer-based) if rendered diagrams are required.
- The venv at `/tmp/pdf-venv` persists for the session; reuse it for subsequent conversions without reinstalling.
- Markdown extensions used: `tables`, `fenced_code`, `toc` — covers standard GFM features present in Nighthawk reports.
- Output PDF is saved alongside the source `.md` file in `notes/`.
