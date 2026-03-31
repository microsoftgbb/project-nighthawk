# Nighthawk Dev Container

This dev container provides Mermaid diagram support and markdown tooling for Nighthawk. **Optimized for GitHub Codespaces.**

## What's Included

### Mermaid Support
- **VS Code Extensions** - Live Mermaid preview in markdown files
- **@mermaid-js/mermaid-cli** - Export diagrams to PNG/SVG/PDF
- Built-in Chromium support (via Codespaces Universal image)

### Markdown Tools
- **markdownlint** - Linting and style checking
- **markdown-link-check** - Validate all links
- **markdown-all-in-one** - TOC generation, formatting, shortcuts
- **markdown-preview-enhanced** - Advanced markdown preview

### Developer Tools
- **GitHub CLI** - Pre-installed in Codespaces
- **Node.js 20** - For npm packages and tooling
- **Git** - Version control

## Quick Start with Codespaces

1. **Create Codespace**: Click "Code" → "Create codespace on main"
2. **Wait for setup**: Container builds in ~1-2 minutes
3. **Open any markdown file**: Mermaid diagrams render automatically in preview

## Usage

### View Mermaid Diagrams

**In VS Code Preview:**
1. Open any `.md` file with Mermaid diagrams
2. Press `Cmd+Shift+V` (Mac) or `Ctrl+Shift+V` (Windows/Linux)
3. Diagrams render automatically - no build step needed!

**Side-by-side Preview:**
- Press `Cmd+K V` to open preview alongside editor
- Live updates as you type

### Export Diagrams (Optional)
### Export Diagrams (Optional)

If you need standalone image files:

```bash
# Export single diagram to PNG
mmdc -i diagram.mmd -o diagram.png

# Export all Mermaid blocks from markdown
mmdc -i notes/my-note.md -o output/

# Different formats
mmdc -i diagram.mmd -o diagram.svg
mmdc -i diagram.mmd -o diagram.pdf

# Dark theme
mmdc -i diagram.mmd -o diagram.png -t dark
```

### Lint Markdown

```bash
# Lint all markdown files
markdownlint notes/**/*.md

# Fix auto-fixable issues
markdownlint --fix notes/**/*.md
```

### Check Links

```bash
# Check all links in a file
markdown-link-check notes/my-note.md

# Check all markdown files
find notes -name "*.md" -exec markdown-link-check {} \;
```

## Customization

### Add More Extensions

Edit `.devcontainer/devcontainer.json` and add to the `extensions` array:

```json
"customizations": {
  "vscode": {
    "extensions": [
      "your.extension.id"
    ]
  }
}
```

### Install Additional Tools

Edit `postCreateCommand` in `devcontainer.json`.

### Mermaid Themes

Available themes: `default`, `dark`, `forest`, `neutral`

Change in VS Code settings or use CLI flag: `mmdc -t dark`

## Tips

1. **Live Preview**: Use `Cmd+K V` to open preview side-by-side
2. **TOC Generation**: Right-click in markdown → "Create Table of Contents"
3. **Diagram Snippets**: Type `mermaid` and press Tab for code block
4. **Export from Preview**: Right-click on rendered diagram → "Copy as PNG"
5. **Zen Mode**: `Cmd+K Z` for distraction-free writing

## Troubleshooting

### Mermaid Not Rendering

If diagrams don't appear in preview:
```bash
# Verify extensions are installed
code --list-extensions | grep -i mermaid

# Reload window
# Cmd+Shift+P → "Developer: Reload Window"
```

### Export Errors

If `mmdc` command fails:
```bash
# Test Mermaid CLI
mmdc --version

# Check Node.js version (should be 20)
node --version
```

### Slow Container Startup

First launch takes 1-2 minutes to install npm packages. Subsequent launches use cached layers.

### Extension Not Loading

If an extension doesn't appear:
1. Check the Extensions panel
2. Reload window: `Cmd+Shift+P` → "Developer: Reload Window"

## Codespaces-Specific Tips

- **Prebuilds**: Enable prebuilds in repository settings for instant startup
- **Machine Type**: Default 2-core works fine for documentation
- **Auto-save**: Enabled by default (1 second delay)
- **Secrets**: Use Codespaces secrets for API keys (Settings → Codespaces → Secrets)

## Resources

- [Mermaid Documentation](https://mermaid.js.org/) - All diagram types and syntax
- [Mermaid Live Editor](https://mermaid.live/) - Test diagrams interactively
- [Markdown Guide](https://www.markdownguide.org/) - Markdown syntax reference
- [GitHub Codespaces Docs](https://docs.github.com/codespaces) - Codespaces features and settings
