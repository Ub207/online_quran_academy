# Al-Mizan Online Quran Academy

Personal website + RAG-powered AI chatbot for Al-Mizan Online Quran Academy, founded by Qari Hafiz Ubaid ur Rehman.

## Project Structure

```
al-mizan-academy/
├── index.html              <- Main academy website
├── app.py                 <- AI chatbot (Streamlit + Groq RAG)
├── quran_academy_kb.txt   <- Chatbot knowledge base
├── requirements.txt       <- Python dependencies
└── .streamlit/
    └── secrets.toml.example  <- API key template
```

## Deploy the AI Chatbot (Free - Streamlit Cloud)

1. Go to https://streamlit.io/cloud and sign in with GitHub
2. Click New app, select this repo, set Main file path to app.py
3. Under Advanced settings > Secrets, paste:
   GROQ_API_KEY = "your_key_here"
4. Get a free Groq API key at https://console.groq.com
5. Click Deploy - you will get a URL like https://your-app.streamlit.app

## Connect Chatbot to Website

After deploying, open index.html and find this line near the bottom:

   var CHATBOT = "https://al-meezan-academy.streamlit.app?embed=true&embed_options=hide_toolbar,hide_footer";

Replace it with your real Streamlit Cloud URL.

## Run Locally

pip install -r requirements.txt
streamlit run app.py

---
2025 Al-Mizan Online Quran Academy - Qari Hafiz Ubaid ur Rehman
"# finance_bot" 
