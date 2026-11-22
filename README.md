# Karnataka Legal RAG Assistant - Fully Colab

Current code is for Karnataka legal only. Not generic yet.

I am experimenting with Karnataka legal as a test subject. Legal is not the main product. The RAG system is the main product.
But in current code, only the legal test is implemented. Generic RAG for any subject is the future goal, not yet in code.

---

## What this repo actually does - from code

This repo is fully on Google Colab. No RunPod. No PC needed.

It does NOT build the index. It uses a ready-made index.

### Step 1: Install Ollama & Models in Colab

```python
!apt-get update -qq && apt-get install -y zstd
!curl -fsSL https://ollama.com/install.sh | sh
!pip install faiss-cpu rank_bm25 numpy requests

import subprocess, time
subprocess.Popen(["ollama", "serve"], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
time.sleep(5)

!ollama pull nomic-embed-text
!ollama pull qwen2.5:7b
```

### Step 2: Launch App

```python
!git clone https://github.com/rsvikas/generic-rag-colab.git
%cd generic-rag-colab
!python scripts/legal_rag_app.py
```

### What `legal_rag_app.py` actually does

- Looks for index in this order:
  1. `./indices`
  2. Google Drive: `/content/drive/MyDrive/Legal_RAG_Backup/indices` - your personal Drive path, hard-coded
  3. GitHub Release: `https://github.com/rsvikas/generic-rag-colab/releases/download/v1.0.0/indices.zip`

- Index must contain: `faiss.index` + `metadata.json` + `bm25.pkl` - FAISS + BM25 hybrid
- Expands acronyms: KAOA, KAOR, KOFA, RERA, KCSA, TOPA, RDPR, IGR - hard-coded Karnataka only
- Hybrid search: FAISS (meaning) + BM25 (keywords) - Best 5 chunks
- Asks local LLM `qwen2.5:7b` with prompt: strict Legal AI Assistant
- If you type short follow-up (< 4 words), it joins it with last question
- If not in PDFs, says: Not found in provided documents

---

## Test Data: Karnataka Legal

Test data is Karnataka property laws: KAOA, KAOR, KOFA, RERA, KCSA, TOPA, RDPR, IGR.

This test does NOT give legal advice. It only finds text from your PDFs.

---

## To build a new index for a new subject

This repo cannot build an index. To build a new index:

1. Use the other repo: `generic-rag-system`
2. Build index there on RunPod GPU
3. Publish new `indices.zip` as GitHub Release, or put it in Google Drive
4. This Colab app will then use the new index

Note: That build in other repo is FAISS only. This Colab app needs FAISS + BM25. You need an extra hybrid build step (not in Git yet, only in Documents backup).

---

## Hard-coded in code - what you must know

- Models: `nomic-embed-text` + `qwen2.5:7b` - hard-coded
- Acronyms: Karnataka only - hard-coded
- Drive path: `/content/drive/MyDrive/Legal_RAG_Backup/indices` - personal path, will fail for others
- Release URL: `v1.0.0/indices.zip` - always Karnataka index
- No `config.yaml`, no `requirements.txt`

To use a different subject today, you must edit code + build new index. That is why it is not generic yet.

---

## Limitations - from code

- Needs ready-made index. Cannot build index here.
- Prompt always says Legal, even if data is not legal.
- Chunking in build repo only understands legal headings.
- Tables may lose visual structure.
- Always verify against original PDFs.

---

## Disclaimer

This tool only retrieves text from documents you give it. It does not give legal advice.

---

## Repo Map

- `generic-rag-colab` (this repo) = Fully on Google Colab. Uses ready-made index. Legal code only for now.
- `generic-rag-system` = Build on RunPod GPU, Use on PC. Legal code only for now.
