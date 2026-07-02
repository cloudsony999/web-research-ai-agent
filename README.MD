# 🔎 Web Research AI Agent

A personal, privacy-first AI research assistant that searches the web and summarizes the results using a **local LLM** — no cloud API costs, no data leaving your machine.

Built with **Ollama**, **Qwen3.5**, **Requests**, and **BeautifulSoup**, this agent takes any topic you give it, pulls the top search results, scrapes and cleans the page content, and produces a clean, timestamped **Markdown digest** you can read, archive, or share.

---

## ✨ Why This Project

Most people use ChatGPT or Claude for research, but hosted models can hallucinate on topics outside their training data, and every query leaves your machine. This agent solves that by:

- Running **entirely locally** via Ollama — your research history never leaves your laptop
- Using **live web search** to ground answers in current information
- Costing **$0 in API fees** (Ollama's free tier is sufficient)
- Producing **portable Markdown output** you can store, version, or feed into other tools

---

## 🏗️ Architecture

```
User prompt in terminal
        │
        ▼
Ollama Web Search API
        │
        ▼
   Top 5 result URLs
        │
        ▼
 requests + BeautifulSoup
        │
        ▼
    Clean page text
        │
        ▼
 Local Qwen model (via Ollama)
        │
        ▼
 Markdown digest saved to disk
```

1. You enter a research prompt in the terminal.
2. The agent calls the **Ollama Web Search API** and retrieves the top 5 results.
3. Each result URL is fetched and cleaned with `requests` + `BeautifulSoup` (scripts, nav, and footer content stripped out).
4. The cleaned page content, along with your original prompt, is sent to a **local Qwen model**.
5. The model's response is saved as a timestamped `.md` file in your working directory.

---

## 📦 Requirements

- Python 3.9+
- [Ollama](https://ollama.com) installed and a free Ollama account (for the Web Search API key)
- ~3.4 GB free disk space for the `qwen3.5:4b` model (or less for smaller variants)
- macOS, Windows, or Linux

---

## 🚀 Getting Started

### 1. Install Ollama and get an API key

Install [Ollama](https://ollama.com) and create a free account to generate an API key.

Set the key as an environment variable:

**macOS / Linux**
```bash
export OLLAMA_API_KEY="your-api-key-here"
```

**Windows (PowerShell)**
```powershell
setx OLLAMA_API_KEY "your-api-key-here"
```

### 2. Pull the Qwen model

```bash
ollama pull qwen3.5:4b
```

> 💡 Running on limited RAM? Use a smaller model instead:
> ```bash
> ollama pull qwen3.5:0.8b
> ```
> Then update the `MODEL` variable in `research_agent.py`.

### 3. Clone the repo and set up your environment

```bash
git clone https://github.com/cloudsony999/web-research-ai-agent.git
cd web-research-ai-agent

python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate

pip install ollama requests beautifulsoup4
```

### 4. Run the agent

```bash
python research_agent.py
```

You'll be prompted to enter a topic:

```
Enter your prompt: What's new in LLMs
Fetching: https://example.com/article-1
Fetching: https://example.com/article-2
...
Saved to digest
```

The result is saved as `digest-YYYY-MM-DD_HH-MM-SS.md` in the project directory.

---

## 📄 Sample Output

Running the agent with the prompt **"What's new in LLMs"** produces a structured digest like:

```markdown
# What's New in LLMs (Large Language Models)?

**Recent Developments:**
1. **Model Releases** — summary of newly announced models and capabilities
2. **Advancements** — progress in translation, summarization, and reasoning

**Key Takeaways:**
1. Phased rollouts and staged availability
2. Stronger safety and evaluation checks
3. Improved performance on coding, reasoning, and multimodal tasks

**What's Next:**
- Continued progress on multimodal understanding
- Broader real-world adoption across industries
```

> ⚠️ **Note:** Local models are smaller than hosted frontier models and can hallucinate more often. Always skim the digest and spot-check a claim or two against the original source before relying on it.

---

## 💡 Best Use Cases

This agent is a flexible building block — here are practical ways to use it:

| Use Case | How |
|---|---|
| **Daily AI/tech news digest** | Run each morning with a prompt like `"latest AI news today"` to get a summarized briefing before you start work |
| **Competitive/market research** | Research a competitor, product category, or industry trend and get a synthesized overview instead of reading 5+ articles |
| **Job market scanning** | Query `"remote data engineer job postings this week"` to get a quick pulse on hiring trends |
| **Personal knowledge base** | Since output is Markdown, pipe digests into Obsidian, Notion, or a personal wiki to build a searchable research archive over time |
| **Pre-meeting briefings** | Quickly research a company, person, or topic before a call without manually opening multiple browser tabs |
| **Fact-checking starting point** | Get a first-pass summary with sources, then verify key claims manually |
| **Academic/technical literature scanning** | Research a technical concept or recent development and get a plain-language summary with source links |
| **Content research for writing** | Gather background material and key talking points before drafting a blog post, newsletter, or report |
| **Private/sensitive research** | Because everything runs locally, it's suitable for research topics you don't want sent to a third-party API (e.g., internal company topics, personal health/legal research) |
| **Offline-friendly workflows** | Once the model is pulled, only the search/fetch step needs internet — inference itself runs on-device |

---

## ⚙️ Configuration

Key variables in `research_agent.py`:

| Variable | Description | Default |
|---|---|---|
| `API_KEY` | Read from the `OLLAMA_API_KEY` environment variable | — |
| `SEARCH_URL` | Ollama Web Search API endpoint | `https://ollama.com/api/web_search` |
| `MODEL` | Local Ollama model used for summarization | `qwen3.5:4b` |

To swap models, pull a different one with `ollama pull <model>` and update `MODEL` accordingly (e.g., `qwen3.5:0.8b`, `qwen3.5:9b`, or `llama3:latest`).

---

## 🗺️ Roadmap Ideas

- [ ] Add a `requirements.txt` for one-line dependency installs
- [ ] Support configurable number of search results (currently fixed at 5)
- [ ] Add a `--model` and `--query` CLI flag to skip interactive prompts
- [ ] Optional citation footer linking each claim back to its source URL
- [ ] Scheduled/cron-friendly mode for automated daily digests
- [ ] Export digests to PDF or HTML in addition to Markdown

Contributions and suggestions are welcome — feel free to open an issue or PR.

---

## ⚠️ Limitations

- Web scraping quality depends on target site structure; some pages (heavy JS, paywalled, or bot-protected sites) may return little to no usable text.
- Local models, especially smaller variants like `qwen3.5:0.8b`, are more prone to hallucination than hosted frontier models — always verify important claims.
- Search result quality depends on the Ollama Web Search API's coverage and ranking.

---

## 📜 License

This project currently has no explicit license. Add a `LICENSE` file (e.g., MIT) if you intend for others to reuse or contribute to this code.

---

## 🙌 Acknowledgements

- [Ollama](https://ollama.com) for local model serving and the Web Search API
- [Qwen](https://ollama.com/library/qwen3.5) for the open-weight language model
- `requests` and `beautifulsoup4` for web fetching and HTML parsing

---

## 👤 Creator

**Mr. Amitava Chattopadhyay**
Corporate Trainer — Data Analyst & Full Stack Tech

🌐 Personal Website: [https://www.techsutra.co.in/](https://www.techsutra.co.in/)
💻 GitHub: [https://github.com/cloudsony999](https://github.com/cloudsony999)
