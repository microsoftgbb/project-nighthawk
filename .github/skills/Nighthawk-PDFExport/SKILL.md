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

## Option B — Python + weasyprint (macOS fallback, supports Mermaid)

This is the verified working approach on macOS. It handles Mermaid diagrams by pre-rendering them to hi-res PNG via Puppeteer, then embedding them inline before weasyprint processes the document.

> **Do NOT use SVG** — weasyprint renders SVG shapes but drops all text (missing font resolution). Always use PNG.

### Step 1 — Install dependencies

```bash
python3 -m venv /tmp/pdf-venv
/tmp/pdf-venv/bin/pip install --quiet markdown weasyprint
```

### Step 2 — Pre-render Mermaid diagrams to PNG

Run this for each report that contains a ```` ```mermaid ```` block:

```bash
# Extract the mermaid source
awk '/^```mermaid$/,/^```$/' notes/Nighthawk-YYYY-MM-DD-topic.md | sed '1d;$d' > /tmp/diagram.mmd

# Render to hi-res PNG (--width 3000 --scale 3 ensures readable text in PDF)
npx @mermaid-js/mermaid-cli -i /tmp/diagram.mmd -o /tmp/diagram.png --width 3000 --scale 3
```

### Step 3 — Generate PDF with embedded diagram

Write the following to `/tmp/build_pdf.py` and run with the venv:

```python
import pathlib, re, base64, markdown
from weasyprint import HTML

MD_IN  = "/Volumes/Extreme SSD/src/project-nighthawk/notes/Nighthawk-YYYY-MM-DD-topic.md"
PNG_IN = "/tmp/diagram.png"   # omit if no Mermaid diagram
PDF_OUT = "/Volumes/Extreme SSD/src/project-nighthawk/notes/Nighthawk-YYYY-MM-DD-topic.pdf"

CSS = """
body { font-family: Georgia, serif; font-size: 11pt; line-height: 1.6; padding: 40px; max-width: 900px; margin: auto; }
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
img { max-width: 100%; height: auto; margin: 16px 0; }
"""

md_text = pathlib.Path(MD_IN).read_text(encoding="utf-8")

# Replace mermaid fenced block with embedded hi-res PNG
png_path = pathlib.Path(PNG_IN)
if png_path.exists():
    png_b64 = base64.b64encode(png_path.read_bytes()).decode()
    png_tag = f'<img src="data:image/png;base64,{png_b64}" alt="Diagram" />'
    md_text = re.sub(r'```mermaid\n.*?```', png_tag, md_text, flags=re.DOTALL)

body = markdown.markdown(md_text, extensions=["tables", "fenced_code", "toc"])
html = f"<!DOCTYPE html><html><head><meta charset='utf-8'><style>{CSS}</style></head><body>{body}</body></html>"
HTML(string=html).write_pdf(PDF_OUT)

size = pathlib.Path(PDF_OUT).stat().st_size
print(f"PDF written: {PDF_OUT} ({size:,} bytes / {size//1024} KB)")
```

```bash
/tmp/pdf-venv/bin/python3 /tmp/build_pdf.py
```

## Notes

- The venv at `/tmp/pdf-venv` persists for the session; reuse it without reinstalling.
- Markdown extensions used: `tables`, `fenced_code`, `toc` — covers standard GFM features present in Nighthawk reports.
- Output PDF is saved alongside the source `.md` file in `notes/`.
- Reports without Mermaid blocks: set `PNG_IN` to a non-existent path — the `if png_path.exists()` guard skips the substitution cleanly.


