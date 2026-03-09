# ScienceDirect Skills for Claude Code

[English](#english) | [中文](#中文)

| WeChat Official Account (公众号) | WeChat Group (微信群) | Discord |
|:---:|:---:|:---:|
| <img src="qrcode_for_gh_a1c14419b847_258.jpg" width="200"> | <img src="0317.jpg" width="200"> | [Join Discord](https://discord.gg/tGd5vTDASg) |
| 未来论文实验室 | 扫码加入交流群 | English & Chinese |

---

<a id="english"></a>

## English

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills that let Claude interact with [ScienceDirect (Elsevier)](https://www.sciencedirect.com) through Chrome DevTools MCP.

Search papers, browse journals, extract metadata, export citations, download PDFs, and push to Zotero — all from the Claude Code CLI.

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- Chrome browser (institutional or personal login for PDF download & export)
- [Zotero](https://www.zotero.org/) desktop app (optional, for citation export)
- Python 3 (optional, for Zotero push script)

### Skills

| Skill | Description | Invocation |
|-------|-------------|------------|
| `sd-search` | Keyword search with structured result extraction | `/sd-search machine learning` |
| `sd-advanced-search` | Filtered search: author, journal, year, title, keywords | `/sd-advanced-search author: Smith year: 2024 deep learning` |
| `sd-parse-results` | Re-parse an existing search results page | _(internal)_ |
| `sd-navigate-pages` | Pagination, sort order, results-per-page | `/sd-navigate-pages next` |
| `sd-paper-detail` | Extract full article metadata (abstract, authors, DOI, etc.) | `/sd-paper-detail S0957417426005245` |
| `sd-journal-browse` | Browse journal info, impact factor, issues, articles | `/sd-journal-browse Nature Energy` |
| `sd-download` | Download article PDFs to local disk | `/sd-download S0957417426005245` |
| `sd-export` | Export citations as RIS/BibTeX/text, push to Zotero | `/sd-export S0957417426005245 format: zotero` |

### Agent

**`sd-researcher`** — orchestrates all 8 skills. Handles Cloudflare captcha detection (auto-clicks or pauses for manual resolution), and supports multi-step workflows like "search → detail → export to Zotero → download PDF".

### Installation

#### 1. Install Chrome DevTools MCP server

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

#### 2. Install ScienceDirect skills

```bash
git clone https://github.com/cookjohn/sd-skills.git
cd sd-skills
cp -r skills/ agents/ .claude/
```

Or add to an existing project:

```bash
git clone https://github.com/cookjohn/sd-skills.git /tmp/sd-skills
cp -r /tmp/sd-skills/skills/ your-project/.claude/skills/
cp -r /tmp/sd-skills/agents/ your-project/.claude/agents/
```

#### 3. Configure anti-detection (recommended)

Add to your `.claude.json` to prevent Cloudflare captcha loops on PDF downloads:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--ignoreDefaultChromeArg=--enable-automation",
        "--ignoreDefaultChromeArg=--disable-infobars",
        "--chromeArg=--disable-blink-features=AutomationControlled"
      ]
    }
  }
}
```

#### 4. Launch Claude Code

```bash
claude
```

Skills and agent are picked up automatically. Try `/sd-search deep learning` to verify.

### Project Structure

```
skills/
├── sd-search/SKILL.md              # Basic keyword search
├── sd-advanced-search/SKILL.md     # Filtered search (author, journal, year, etc.)
├── sd-parse-results/SKILL.md       # Parse existing results page
├── sd-navigate-pages/SKILL.md      # Pagination & sorting
├── sd-paper-detail/SKILL.md        # Paper metadata extraction
├── sd-journal-browse/SKILL.md      # Journal info & issue browsing
├── sd-download/SKILL.md            # PDF download with Cloudflare handling
└── sd-export/                      # Citation export & Zotero
    ├── SKILL.md
    └── scripts/
        └── push_to_zotero.py       # Zotero Connector API client
agents/
└── sd-researcher.md                # Agent: orchestrates all skills
```

---

<a id="中文"></a>

## 中文

| 公众号 | 微信交流群 | Discord |
|:---:|:---:|:---:|
| <img src="qrcode_for_gh_a1c14419b847_258.jpg" width="200"> | <img src="0317.jpg" width="200"> | [加入 Discord](https://discord.gg/tGd5vTDASg) |
| 未来论文实验室 | 扫码加入交流群 | 中英文交流 |

让 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 通过 Chrome DevTools MCP 操作 [ScienceDirect (Elsevier)](https://www.sciencedirect.com) 的技能集。

支持论文检索、期刊浏览、元数据提取、引用导出、PDF 下载、推送到 Zotero 等功能，全部在 Claude Code 命令行中完成。

### 前置要求

- 已安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- Chrome 浏览器（下载和导出需机构或个人账号登录）
- [Zotero](https://www.zotero.org/) 桌面端（可选，用于引用导出）
- Python 3（可选，用于 Zotero 推送脚本）

### 技能列表

| 技能 | 功能 | 调用方式 |
|------|------|----------|
| `sd-search` | 关键词检索，返回结构化结果 | `/sd-search machine learning` |
| `sd-advanced-search` | 高级检索：作者、期刊、年份、标题、关键词 | `/sd-advanced-search author: Smith year: 2024 deep learning` |
| `sd-parse-results` | 重新解析当前搜索结果页 | _（内部调用）_ |
| `sd-navigate-pages` | 翻页、排序、每页显示数量 | `/sd-navigate-pages next` |
| `sd-paper-detail` | 提取论文完整元数据（摘要、作者、DOI 等） | `/sd-paper-detail S0957417426005245` |
| `sd-journal-browse` | 浏览期刊信息、影响因子、期次、文章列表 | `/sd-journal-browse Nature Energy` |
| `sd-download` | 下载论文 PDF 到本地 | `/sd-download S0957417426005245` |
| `sd-export` | 导出引用为 RIS/BibTeX/纯文本，推送到 Zotero | `/sd-export S0957417426005245 format: zotero` |

### 智能体

**`sd-researcher`** — 统一调度全部 8 个技能。自动处理 Cloudflare 验证码（自动点击或暂停等待用户手动完成），支持"检索 → 详情 → 导出到 Zotero → 下载 PDF"等复合工作流。

### 安装方法

#### 1. 安装 Chrome DevTools MCP 服务器

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

#### 2. 安装 ScienceDirect 技能

```bash
git clone https://github.com/cookjohn/sd-skills.git
cd sd-skills
cp -r skills/ agents/ .claude/
```

添加到已有项目：

```bash
git clone https://github.com/cookjohn/sd-skills.git /tmp/sd-skills
cp -r /tmp/sd-skills/skills/ your-project/.claude/skills/
cp -r /tmp/sd-skills/agents/ your-project/.claude/agents/
```

#### 3. Configure anti-detection（推荐）

在 `.claude.json` 中添加以下配置，优化 PDF 下载体验：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--ignoreDefaultChromeArg=--enable-automation",
        "--ignoreDefaultChromeArg=--disable-infobars",
        "--chromeArg=--disable-blink-features=AutomationControlled"
      ]
    }
  }
}
```

#### 4. 启动 Claude Code

```bash
claude
```

技能和智能体会自动加载。输入 `/sd-search deep learning` 验证是否正常。

---

## License

MIT
