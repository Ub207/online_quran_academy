# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository location & git hazard (read first)

The project repo root is **`al-mizan-academy/`** — that directory owns the `.git` (remote: `github.com/Ub207/al-mizan-academy.git`, branch `main`). The Claude Code working directory is usually its parent, `quran-academy-bot/`, which is **not** a repo.

Critically, `quran-academy-bot/`'s own parent (`C:\Users\Hp`, the home directory) *is* a separate git repo with a public remote. So a bare `git` command from the working directory targets the home repo, not this project — and `git add .` there can stage unrelated home-directory files onto a public remote. Always scope git to this repo:

```bash
git -C /c/Users/Hp/quran-academy-bot/al-mizan-academy <cmd>
```

Never `git add .` from the working directory or home directory.

## Commands

```bash
# from within al-mizan-academy/
pip install -r requirements.txt
streamlit run app.py            # launches the chatbot on http://localhost:8501
```

There are no tests, linter, or build step — this is a Streamlit app plus a single static HTML page.

Local secrets: put `GROQ_API_KEY=...` in a `.env` file (loaded via python-dotenv). Get a free key at console.groq.com.

## Architecture

Two deliverables ship from this one repo, deployed to two different places:

| File | What it is | Deploys to |
|------|-----------|-----------|
| `app.py` + `quran_academy_kb.txt` | RAG chatbot (Streamlit) | Streamlit Cloud → `al-meezan-academy.streamlit.app` |
| `index.html` | Academy marketing website (single self-contained file) | GitHub Pages → `ub207.github.io/al-mizan-academy/` |

**RAG pipeline** (`app.py`, all in-process, no vector DB service):
`quran_academy_kb.txt` → chunk → embed with `all-MiniLM-L6-v2` (sentence-transformers) → FAISS `IndexFlatL2` → retrieve top-3 → inject as context into a Groq chat completion (`llama-3.3-70b-versatile`, temp 0.3, max 800 tokens, last 6 turns of history).

**Website ↔ chatbot coupling:** `index.html` embeds the deployed chatbot in an `<iframe>`. The Streamlit URL is hardcoded in **two** places in `index.html` — the iframe `src` (~line 1176) and the `CHATBOT` JS var (~line 1217). If the Streamlit deployment URL changes, update both. (The README still references an old var name `CHATBOT_URL` and old URL `al-meezan-chatbot.streamlit.app`; the live code uses `CHATBOT` and `al-meezan-academy.streamlit.app` — trust the code.)

## Things that will bite you

- **KB format is load-bearing.** `load_knowledge_base()` chunks the KB by splitting on lines that start with `Q:` (one chunk per Q&A pair), then keeps only chunks containing `Q:` or `A:`. If you edit `quran_academy_kb.txt`, preserve the `Q:` / `A:` line structure — otherwise it silently falls back to blind 500-char character splitting and retrieval quality drops. Section headers (e.g. `COURSES OFFERED`, `----`) are fine; they get folded into the adjacent Q&A chunk.
- **The KB + embeddings are cached** via `@st.cache_resource`. After editing the KB, changes won't appear until you clear the Streamlit cache (⋮ menu → Clear cache) or restart the app.
- **GROQ_API_KEY resolution order:** `os.environ` first (so `.env` wins), then `st.secrets["GROQ_API_KEY"]`. Note `.streamlit/secrets.toml.example` nests the key under a `[secrets]` header, but the code reads it **top-level** — for the `st.secrets` path the key must sit at the top of `secrets.toml`, not under a section. On Streamlit Cloud, paste `GROQ_API_KEY = "..."` at top level in Advanced settings → Secrets.
- The chatbot is instructed (via its system prompt in `get_groq_response`) to answer **only** from retrieved KB context and to redirect Fatwa/religious-ruling questions to qualified scholars. Keep that guardrail when editing the prompt.
