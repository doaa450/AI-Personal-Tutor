# 🚀 [Tips Hindawi](https://www.tipshindawi.com/) Internship (August–October) 2026

> 🎓 This project was built during the [ **Tips Hindawi** ](https://www.tipshindawi.com/) **Internship (August–October) 2026**.

## 👤 Participant

| Field            | Value                                |
| ---------------- | ------------------------------------ |
| Full Name        | Doaa Ahmed Mohammed                    |
| Project Name     | AI Personal Tutor (RAG)              |
| GitHub Username  | doaa450<img width="1180" height="554" alt="2" src="https://github.com/user-attachments/assets/37801596-c160-4a3c-a920-5106d7e4381d" />
              |
| Internship Batch | August–October 2026                  |
| Training Program | Large Language Models (LLMs) Program |
| Organization     | [**Edrak for Ai**](https://edrak4ai.com/en)                         |

---

# 📖 Project Overview

**AI Personal Tutor** is a RAG (Retrieval-Augmented Generation) tutor for lecture PDFs. You upload an English PDF, ask questions about it, take a multiple-choice quiz generated from it, get graded, and see your score history.

The project follows one design rule learned from testing a small 7B model: **the model writes plain text or JSON only, and the code handles everything that must be reliable** (page numbers, sources, example lines, grading).

Test document: Stanford CS329S slides, *Data Distribution Shifts on Streaming Data* (36 chunks).

---

# ✨ Features

* **Ask:** answers only from the PDF, says "I don't know" when the PDF does not contain the answer, and lists sources by page.
* **Hybrid retrieval:** dense search + BM25, merged with RRF, then reranked with a cross-encoder.
* **Quiz:** multiple-choice questions generated from the PDF with structured output (Pydantic) and automatic retry on invalid JSON.
* **Grading by code:** the selected option is compared with the correct option (score 100 or 0); the model only writes the feedback.
* **Score history:** average score and weakest topic, saved in `scores.json`.
* **Upload any English PDF** from the UI; the index is rebuilt automatically.

---

# 🛠️ Technologies Used

* **LLM:** Qwen2.5-7B-Instruct (4-bit, bitsandbytes) through LangChain `ChatHuggingFace`
* **Embeddings:** all-MiniLM-L6-v2 · **Vector DB:** ChromaDB
* **Keyword search:** rank_bm25 · **Reranker:** cross-encoder/ms-marco-MiniLM-L-6-v2
* **Orchestration:** LangChain, Pydantic (structured output)
* **Backend:** FastAPI + Uvicorn · **UI:** Streamlit · **Tunnel:** ngrok
* **Runtime:** Kaggle notebook with GPU

Settings: chunk size 800, overlap 120, dense top-10, BM25 top-10, RRF k=60, final top-4.

**Pipeline:** PDF → chunks → hybrid search → rerank → Qwen2.5-7B → FastAPI → Streamlit

---

# ⚙️ Installation

The whole project is one Kaggle notebook (GPU on, Internet on).

1. Install the packages (one cell each, in this order):
```bash
pip install -q -U opentelemetry-api opentelemetry-sdk opentelemetry-exporter-otlp-proto-grpc
pip install -q langchain-community langchain-text-splitters pypdf requests
pip install -q langchain-chroma langchain-huggingface sentence-transformers
pip install -q rank-bm25
pip install -q -U "bitsandbytes>=0.46.1" accelerate
pip install -q streamlit pyngrok
```
2. Add your ngrok token as a Kaggle Secret named `NGROK_TOKEN` (Add-ons → Secrets, and enable it for the notebook).
3. Run the notebook cells in order: load PDF and chunks → embeddings and Chroma → BM25 and hybrid → reranker → load Qwen → `ask` → quiz and grading → score history → FastAPI backend → upload endpoint → write `app.py` → start Streamlit and ngrok.

Known fixes:
* `opentelemetry` import error: run the first pip line, then restart the kernel.
* 4-bit loading needs `bitsandbytes>=0.46.1`.
* The `max_new_tokens` / `max_length` warning is harmless.

---

# 🚀 Usage

1. Run the last cell and open the printed ngrok link (click *Visit Site* on the first warning page).
2. **Ask tab:** type an English question about the PDF.
3. **Quiz tab:** enter a topic, generate questions, choose an answer, press *Check*.
4. **History tab:** see your average score and weakest topic.
5. **Sidebar:** upload another English PDF and press *Use this PDF* (this also clears the old score history).

To use another PDF without the UI: change the URL in Step 1, run `!rm -rf chroma_db`, and rerun Steps 1–3.

Note: Kaggle files (like `scores.json`) can be lost when the session ends. Download them if you need them.

---

# 📸 Demo

> Add screenshots here: Ask tab, Quiz tab with a graded answer, History tab, and the sidebar upload.

```
![Ask](screenshots/ask.png)<img width="1180" height="554" alt="2" src="https://github.com/user-attachments/assets/6c5bd787-2bbf-4797-bd81-08f87e0f7669" />

![Quiz](screenshots/quiz.png)
![History](screenshots/history.png)
```

---

# 📈 Results

* The full flow works end to end on Kaggle: Ask, Quiz, Grading, History, and PDF upload (tested with the CS329S slides and a text-based MLOps course PDF).
* Answers on the test PDF matched the slide text in manual checks.
* Retrieval quality has **not** been measured yet (no hit@4 numbers).

**Lessons learned**
1. Qwen 7B ignores multi-part formatting instructions, so the code builds pages, sources and example lines.
2. LLM grading scored a correct choice 30/100, so grading moved to exact match in code.
3. "Best match: page N" is an estimate from the cross-encoder, not proof of a citation.
4. A silent bug (empty `/ask` response, because the function displayed the result instead of returning it) was found by testing through the API, not only in the notebook.

**Known limits:** English only; tables are flattened by the PDF loader; the topic outline works best for slides, not text-heavy PDFs; comparison questions may need a larger top-K; RAG cannot summarize a whole PDF or count things.

---

# 🔮 Future Improvements

* Measure retrieval quality: hit@4 for dense vs hybrid vs hybrid + rerank on ~10 questions with known pages.
* Better table reading (pdfplumber or PyMuPDF) and better outlines for text PDFs.
* Filter generated quiz questions with the reranker before showing them.
* Persistent storage (SQLite) and Docker deployment instead of a notebook.
* Monitor the app itself (latency, logs, answer quality), for example with MLflow.
* Arabic support with multilingual embeddings and reranker.

---

# 📚 About the Internship

This project was developed as part of the [**Tips Hindawi**](https://www.tipshindawi.com/) **Internship (August–October) 2026**, and it will be showcased on the official [Tips Hindawi](https://www.tipshindawi.com/) website.

[Tips Hindawi](https://www.tipshindawi.com/) is the internships department of [**Edrak for Ai**](https://edrak4ai.com/en), and the internship encourages participants to build real-world projects, apply practical skills, and showcase their work through GitHub.

For more information about the internship, training programs, and upcoming batches, visit the official [Tips Hindawi](https://www.tipshindawi.com/) website.

---

# 📄 License

This project is shared for educational and portfolio purposes.
