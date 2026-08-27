# 🕌 Al-Mizan Online Quran Academy

> Marketing website **+** RAG-powered AI assistant for **Al-Mizan Online Quran Academy** — founded by **Qari Hafiz Ubaid ur Rehman**, a certified Hafiz, Aalim, and rare *Saba Qira'at* (Seven Styles of Recitation) specialist with Ijazah and 15+ years of teaching experience.

![Python](https://img.shields.io/badge/Python-3.9+-3776AB?logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-app-FF4B4B?logo=streamlit&logoColor=white)
![Groq](https://img.shields.io/badge/Groq-Llama%203.3%2070B-F55036)
![FAISS](https://img.shields.io/badge/FAISS-vector%20search-0467DF)
![RAG](https://img.shields.io/badge/RAG-retrieval%20augmented-6C4AB6)

**🌐 Live website:** https://ub207.github.io/al-mizan-academy/
**🤖 Live AI assistant:** https://al-mizan-academy.streamlit.app

---

## What's in this repo

One repository ships **two** deliverables that deploy to two different places:

| Component | File(s) | Deploys to |
|-----------|---------|-----------|
| Academy website (single self-contained page) | `index.html` | **GitHub Pages** → `ub207.github.io/al-mizan-academy/` |
| RAG AI chatbot (Streamlit) | `app.py` + `quran_academy_kb.txt` | **Streamlit Cloud** → `al-mizan-academy.streamlit.app` |

The website embeds the chatbot in an `<iframe>`, so visitors can chat with the assistant right on the site.

## ✨ Features

- **24/7 AI assistant** for course, pricing, scheduling, and enrollment questions
- **Grounded answers (RAG)** — replies come from a curated knowledge base, so pricing and course details aren't hallucinated
- **Knows the full catalogue** — 18 courses across 5 categories, plus the *Saba Qira'at* and *Ijazah* specializations
- **Typo / transliteration tolerant** — understands variations like `gardan` → `Gardaan`
- **Warm, culturally-aware tone** with natural Islamic greetings
- **Stays in its lane** — politely redirects religious rulings (Fatwa) to qualified scholars
- **Free-trial guidance** — nudges prospective students toward booking a free class
- **Elegant, responsive website** with the chatbot embedded inline

## 🧠 How the AI assistant works (RAG)

```
quran_academy_kb.txt
  → split into Q&A chunks
  → embed each chunk (all-MiniLM-L6-v2, sentence-transformers)
  → store in a FAISS IndexFlatL2 vector index (in-memory, no external DB)

user question
  → embed → retrieve the top-6 most relevant chunks
  → inject [retrieved context] + [authoritative course catalogue] + [last 6 turns]
  → Groq chat completion (llama-3.3-70b-versatile)
  → grounded answer
```

The full course catalogue also lives directly in the system prompt, so the bot **always** knows every course by name — even if a specific question doesn't retrieve the right chunk.

## 🛠️ Tech stack

- **Python 3.9+**
- **Streamlit** — chat UI
- **Groq API** — `llama-3.3-70b-versatile` (Llama 3.3 70B)
- **sentence-transformers** — `all-MiniLM-L6-v2` embeddings
- **FAISS** (`faiss-cpu`) — vector similarity search
- **langchain-text-splitters** — fallback chunking
- **python-dotenv** — local secrets

## 📁 Project structure

```
al-mizan-academy/
├── index.html                  <- Academy website (single self-contained page)
├── app.py                      <- RAG AI chatbot (Streamlit + Groq)
├── quran_academy_kb.txt        <- Chatbot knowledge base (Q&A format)
├── requirements.txt            <- Python dependencies
├── .streamlit/
│   └── secrets.toml.example    <- API-key template for Streamlit Cloud
├── CLAUDE.md                   <- Repo guide for AI coding assistants
└── README.md
```

## 🚀 Run locally

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Add your Groq API key (free at https://console.groq.com)
echo 'GROQ_API_KEY=your_key_here' > .env

# 3. Launch — opens http://localhost:8501
streamlit run app.py
```

> **Windows note:** if `streamlit` / `pip` aren't on your PATH, use the `py` launcher:
> `py -m pip install -r requirements.txt` and `py -m streamlit run app.py`.

## ☁️ Deployment

### Chatbot → Streamlit Community Cloud (free)

1. Push this repo to GitHub.
2. Go to [share.streamlit.io](https://share.streamlit.io) and sign in with GitHub.
3. **New app** → select this repo → set **Main file path** to `app.py`.
4. **Advanced settings → Secrets**, paste this at the **top level** (not under a `[section]` header):
   ```toml
   GROQ_API_KEY = "your_key_here"
   ```
5. **Deploy.** Every push to `main` auto-redeploys the app.

### Website → GitHub Pages (free)

1. Repo **Settings → Pages**.
2. **Source:** *Deploy from a branch* → `main` → `/ (root)`.
3. Your site publishes at `https://<username>.github.io/al-mizan-academy/`.

### Connect the two

The website embeds the chatbot via an `<iframe>`. The Streamlit URL is hardcoded in **two** places in `index.html` — keep them in sync:

- the iframe `src` (~line 1176)
- the `CHATBOT` JavaScript variable (~line 1217)

## 📚 Updating the knowledge base (important)

The assistant answers from **`quran_academy_kb.txt`**, *not* from the website. **If you change the website, mirror the change in the KB** — otherwise the bot won't know about it.

- **Keep the `Q:` / `A:` format.** The loader chunks the KB by splitting on lines that start with `Q:` (one chunk per Q&A pair). Breaking that structure silently degrades retrieval quality.
- **Refresh the cache after editing.** The KB and its embeddings are cached (`@st.cache_resource`), so changes won't appear until you **⋮ → Clear cache** (or restart the app).
- **New/renamed course?** Update it in **both** `quran_academy_kb.txt` **and** the course catalogue in the system prompt inside `app.py`, so the bot always recognizes it.

## 📞 Contact

- 🌐 **Website:** https://ub207.github.io/al-mizan-academy/
- ✉️ **Email:** usmanubaidurrehman@gmail.com
- 💬 **Free trial class** available via WhatsApp

---

© 2026 Al-Mizan Online Quran Academy — Qari Hafiz Ubaid ur Rehman. Built with ❤️ using Streamlit & Groq AI.
