# Generic RAG System - Colab Version

Build your own Knowledge DB from your own PDFs. Offline. No cloud. No API key.

This is **not** a legal tool.
I am experimenting with Karnataka legal as a test subject. Legal is not the main product. The RAG system is the main product.


Karnataka legal laws are used here only as **one test example** to check if the tool works.
Tomorrow you can use the same tool with medical books, college notes, company manuals, or any other PDFs.

---

## How to use this repo - Fully on Google Colab

This repo is **fully on Google Colab**. No RunPod. No PC needed.

It does **NOT** build the index. It **uses a ready-made index**.

### Quickstart

Click the **Open in Colab** badge above to launch the notebook directly in Google Colab, or run the steps manually below.

### Step 1: Install Ollama & Models

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

### Step 2: Launch Application

```python
!git clone https://github.com/rsvikas/generic-rag-colab.git
%cd karnataka-legal-rag-assistant
!python scripts/legal_rag_app.py
```

---

## Where does the index come from?

The app needs a ready-made index. It looks in this order:

1. `./indices` - if already in repo folder
2. Google Drive - `/content/drive/MyDrive/Legal_RAG_Backup/indices` - your own backup
3. GitHub Release - `v1.0.0/indices.zip` (in `generic-rag-colab` repo) - downloads and unzips automatically

Index contains:
- `faiss.index` - FAISS vector index
- `metadata.json` - chunk text + source + chunk_id
- `bm25.pkl` - BM25 keyword index for hybrid search

If none of these exist, the app will stop and say it cannot find the index.

---

## What does it do?

You give it PDFs (via the index). It answers your questions **only from those PDFs**.

Test example now:
- Karnataka property laws PDFs -> Ask about Karnataka property law

Future, same tool:
- College notes -> Ask about college notes
- Company manual -> Ask about company manual

Same tool. Different data.

---

## Test Example Included: Karnataka Legal

We test this tool with Karnataka property laws like KAOA, KAOR, KOFA, RERA, KCSA, TOPA, RDPR, IGR.

Why this test?
- Legal PDFs are hard: scanned pages, tables, mixed portrait + landscape, OCR spelling errors.
- If the tool works here, it will work for simpler PDFs too.

This test does **not** give legal advice. It only finds text from your PDFs.

---

## How it works in this Colab version

```
Ready-made index (FAISS + BM25)
  ↓
Your question -> Expand acronyms (KAOA -> Karnataka Apartment Ownership Act, etc.)
  ↓
Hybrid search: FAISS (meaning) + BM25 (keywords) -> Best 5 chunks
  ↓
Local LLM (qwen2.5:7b) answers only from those chunks
```

If answer is not in your PDFs, it says:

> Not found in provided documents

It does not guess. It does not use outside knowledge.

Extra feature in this version:
- If you type a short follow-up (< 4 words), it joins it with your last question. Example: Last question "What is KAOA?" + you type "under RERA" -> it searches "What is KAOA? under RERA".

---

## To build a new index for a new subject

This repo cannot build an index. To build a new index:

1. Use the other repo: `generic-rag-system`
2. Build index there on RunPod GPU
3. Publish the new `indices.zip` as a GitHub Release, or put it in Google Drive
4. This Colab app will then use the new index

---

## Technologies Used

- Python 3.10+
- FAISS + BM25 hybrid search
- Ollama (local LLM runner in Colab)
- nomic-embed-text + qwen2.5:7b
- Open-source local LLMs only

---

## Current Status

- Query / RAG: ✅ Working in Colab
- Hybrid search: ✅ FAISS + BM25
- Acronym expansion: ✅ Karnataka test acronyms
- Index building: ❌ Not in this repo - use `generic-rag-system`

Note: Current code is tuned for the Karnataka legal test (prompt says legal, acronyms are Karnataka legal). The concept is generic, code will be made fully generic in next update.

---

## Limitations

- Needs ready-made index. Cannot build index here.
- Answer quality depends on scan quality of original PDFs
- Tables may lose some visual structure
- Tool finds text, it does not interpret or advise
- Always verify against your original PDFs

---

## Disclaimer

This tool only retrieves text from documents you give it.

It does not give legal advice, medical advice, or any professional advice.

For the Karnataka legal test: verify all answers against official Acts.

---

## Repo Map

- `generic-rag-colab` (this repo) = Fully on Google Colab. Uses ready-made index. No build step.
- `generic-rag-system` = Build on RunPod GPU, Use on PC. Main pipeline to build new index.
