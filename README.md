# ScienceDirect Skills for Claude Code

A set of [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills and an agent for interacting with ScienceDirect (Elsevier) via Chrome DevTools MCP. Search papers, browse journals, extract metadata, export citations, and download PDFs — all from the CLI.

## Skills

| Skill | Description |
|-------|-------------|
| `sd-search` | Basic keyword search on ScienceDirect |
| `sd-advanced-search` | Filtered search (author, journal, year, title, keywords) |
| `sd-parse-results` | Re-parse the current search results page (internal) |
| `sd-navigate-pages` | Pagination, sort order, results-per-page |
| `sd-paper-detail` | Extract full article metadata (abstract, authors, DOI, etc.) |
| `sd-journal-browse` | Browse journal info, impact factor, issues, articles |
| `sd-download` | Download article PDFs to local disk |
| `sd-export` | Export citations as RIS/BibTeX/text, push to Zotero |

## Agent

| Agent | Description |
|-------|-------------|
| `sd-researcher` | Research assistant that coordinates all skills above |

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Chrome DevTools MCP server (e.g. [`anthropics/chrome-devtools`](https://www.npmjs.com/package/@anthropic-ai/chrome-devtools-mcp))
- For PDF download & citation export: institutional or personal ScienceDirect access
- For Zotero push: [Zotero](https://www.zotero.org/) desktop running locally (Connector API on port 23119)

## Installation

### 1. Copy skills into your project

Copy the `skills/` and `agents/` directories into your project's `.claude/` directory:

```
your-project/
  .claude/
    skills/
      sd-search/SKILL.md
      sd-advanced-search/SKILL.md
      sd-parse-results/SKILL.md
      sd-navigate-pages/SKILL.md
      sd-paper-detail/SKILL.md
      sd-journal-browse/SKILL.md
      sd-download/SKILL.md
      sd-export/SKILL.md
      sd-export/scripts/push_to_zotero.py
    agents/
      sd-researcher.md
```

### 2. Configure Chrome DevTools MCP

Add the Chrome DevTools MCP server to your `.claude.json` with anti-detection flags:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "@anthropic-ai/chrome-devtools-mcp@latest",
        "--ignoreDefaultChromeArg=--enable-automation",
        "--ignoreDefaultChromeArg=--disable-infobars",
        "--chromeArg=--disable-blink-features=AutomationControlled"
      ]
    }
  }
}
```

The `--ignoreDefaultChromeArg` and `--chromeArg` flags prevent Cloudflare bot detection. Without them, PDF downloads will be blocked by a captcha loop.

## Usage

### Invoke individual skills

```
/sd-search machine learning
/sd-paper-detail S0957417426005245
/sd-download S0957417426005245
/sd-export S0957417426005245 format: zotero
```

### Use the research agent

The `sd-researcher` agent coordinates all skills for complex workflows:

```
Search ScienceDirect for recent papers on digital twins in manufacturing,
show me the top 3, export their citations to Zotero, and download the PDFs.
```

## Anti-Detection

ScienceDirect uses Cloudflare for bot protection. This project handles it at three layers:

1. **Chrome flags** (MCP config): Removes `--enable-automation`, adds `--disable-blink-features=AutomationControlled`
2. **initScript** (every navigation): Hides `navigator.webdriver` from page scripts
3. **Turnstile auto-click** (fallback): If a Cloudflare checkbox appears, skills auto-click it via CDP

After solving one captcha per session, subsequent navigations work without interruption.

## Zotero Integration

The `sd-export` skill can push citations directly to a running Zotero instance:

- **RIS import**: Sends RIS data to Zotero's `/connector/import` endpoint
- **JSON import**: Structured metadata via `/connector/saveItems` with optional PDF attachment
- **Idempotent**: Uses deterministic session IDs (content hash) — duplicate pushes are safely ignored

The bundled `push_to_zotero.py` script can also be used standalone:

```bash
python scripts/push_to_zotero.py --ris-file citations.ris
python scripts/push_to_zotero.py --json paper_data.json
python scripts/push_to_zotero.py --list  # show Zotero collections
```

## License

MIT
