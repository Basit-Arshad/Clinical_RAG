# 🩺 CARA — Clinical Answer Retrieval Assistant

> A Retrieval-Augmented Generation (RAG) system for evidence-based clinical question answering, built on the DiReCT (Diagnostic Reasoning for Clinical Text) dataset.

---

## 📌 Overview

**CARA** is a clinical NLP application that answers medical questions by retrieving relevant clinical notes and generating grounded, evidence-based responses. Unlike general-purpose medical chatbots, CARA is strictly constrained to answer **only** from the retrieved clinical documents — never hallucinating or generalizing beyond the provided context.

---

## 🏗️ Architecture

```
User Query
    ↓
Embedding Model (zentom/embedding_model)
    ↓
FAISS Vector Search (index.faiss)
    ↓
Top-K Retrieved Clinical Notes
    ↓
LLM (Llama-3.1-8B via Groq / Phi-3-mini local)
    ↓
Grounded Clinical Answer
```

---

## 🚀 Features

- 🔍 **Semantic Retrieval** — Uses sentence embeddings + FAISS for fast and accurate document retrieval
- 🤖 **Two LLM Backends** — Cloud (Groq/Llama-3) or Local (Phi-3-mini)
- 🛡️ **Hallucination-Free** — Strict prompt engineering ensures answers are grounded in retrieved evidence only
- 🖥️ **Two UI Options** — Streamlit (app.py) or Gradio (app1.py)
- 🏥 **Clinical Focus** — Trained on the DiReCT dataset with diagnosis-categorized clinical notes

---

## 📁 Project Structure

```
CARA/
├── app.py                        # Streamlit UI — Groq + Llama-3.1-8B (recommended)
├── app1.py                       # Gradio UI — Phi-3-mini local (GPU required)
├── index.faiss                   # FAISS vector index of clinical documents
├── preprocessed_documents.jsonl  # Preprocessed clinical notes (DiReCT dataset)
├── requirements.txt              # Python dependencies
├── runtime.txt                   # Python version specification
└── README.md                     # This file
```

---

## ⚙️ Setup & Installation

### Prerequisites

- Python 3.11.4
- pip

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/CARA.git
cd CARA
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Set Your Groq API Key

Open `app.py` and replace the placeholder with your actual Groq API key:

```python
GROQ_API_KEY = "your_groq_api_key_here"
```

> 🔑 Get your free API key at [console.groq.com](https://console.groq.com)

---

## ▶️ Running the App

### Option A — Streamlit (Recommended, Cloud LLM)

```bash
streamlit run app.py
```

Then open your browser at `http://localhost:8501`

### Option B — Gradio (Local LLM, GPU Required)

```bash
python app1.py
```

> ⚠️ This option downloads and runs **Phi-3-mini-4k-instruct** locally. Requires a CUDA-compatible GPU with at least 8GB VRAM.

---

## 💡 Usage

1. Launch the app using one of the commands above
2. Type a clinical question in the input box

**Example queries:**
- *"What are the symptoms of COPD exacerbation?"*
- *"What treatment was given to the patient with pneumonia?"*
- *"Describe the clinical findings in tuberculosis cases."*

3. Click **Run** — the system will:
   - Retrieve the top 3–5 most relevant clinical notes
   - Display the retrieved evidence with diagnosis labels
   - Generate a grounded answer from the context

---

## 📦 Dependencies

| Package | Purpose |
|---|---|
| `streamlit` | Web UI (app.py) |
| `gradio` | Web UI (app1.py) |
| `sentence-transformers` | Query & document embedding |
| `faiss-cpu` | Fast vector similarity search |
| `groq` | Groq API client for Llama-3 |
| `numpy` | Numerical operations |
| `torch` | Required for local Phi-3 (app1.py) |
| `transformers` | Phi-3-mini model loading (app1.py) |

---

## 🤖 Models Used

| Component | Model | Source |
|---|---|---|
| Embedding | `zentom/embedding_model` | HuggingFace |
| LLM (Cloud) | `llama-3.1-8b-instant` | Groq API |
| LLM (Local) | `microsoft/Phi-3-mini-4k-instruct` | HuggingFace |

---

## 🧠 How the RAG Pipeline Works

1. **Preprocessing** — Clinical notes from the DiReCT dataset are chunked and stored in `preprocessed_documents.jsonl`
2. **Indexing** — Each note is encoded into a dense vector and stored in a FAISS index (`index.faiss`)
3. **Retrieval** — At query time, the question is embedded and the top-K most similar notes are retrieved
4. **Generation** — The retrieved notes are injected into a strict prompt, and the LLM generates an answer based solely on that context

### Strict Grounding Prompt Rules

The LLM is instructed to:
- ✅ Answer ONLY using facts explicitly stated in the retrieved context
- ❌ NEVER mention lab tests, imaging, or symptoms unless they appear verbatim in the context
- ❌ NEVER use phrases like "typical findings", "may include", or "commonly seen"
- 🔄 If context is insufficient → respond with: *"The provided clinical notes do not contain sufficient information."*

---

## 📊 Dataset

This project uses the **DiReCT** (Diagnostic Reasoning for Clinical Text) dataset — a collection of clinical notes organized by diagnosis categories, designed to support clinical NLP research.

---

## ⚠️ Disclaimer

> CARA is a **research and educational tool only**. It is **not** intended for real clinical decision-making or patient care. Always consult a qualified healthcare professional for medical advice.

---

## 📄 License

This project is for academic and research purposes. Please check the DiReCT dataset license before any commercial use.

---

## 🙌 Acknowledgements

- [DiReCT Dataset](https://github.com/nlpie/DiReCT) — Clinical notes corpus
- [Groq](https://groq.com) — Ultra-fast LLM inference API
- [FAISS](https://github.com/facebookresearch/faiss) — Facebook AI Similarity Search
- [Sentence Transformers](https://www.sbert.net/) — Embedding model framework
- [Microsoft Phi-3](https://huggingface.co/microsoft/Phi-3-mini-4k-instruct) — Local LLM