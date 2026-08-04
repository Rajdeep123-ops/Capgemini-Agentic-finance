# HiveLogic

* **Author:** Ribhvan Pal
* **Co-Authors:** Ritam Sur, Rajdeep Moulik, Ankan Paul, Protyusha Mondal

---

**HiveLogic** is a multi-agent financial research platform that analyzes a company, extracts evidence from SEC filings and uploaded PDFs, generates a structured research report, and lets users chat with that report through a session-based RAG chatbot.

It is designed as a prototype that demonstrates:

- multi-agent orchestration with LangGraph
- report generation from SEC filings and PDFs
- FAISS-based retrieval for evidence-backed answers
- contagion / GraphRAG analysis
- session-based chatbot memory
- React + Vite frontend
- FastAPI backend with SQLAlchemy and Supabase PostgreSQL

---

## What HiveLogic Does

1. You enter a stock ticker such as `AAPL` or `NVDA`.
2. HiveLogic fetches the latest SEC 10-K filing and recent news.
3. If you upload a PDF, it can prioritize the PDF content for that analysis.
4. The system extracts financial metrics, sentiment, risks, and contagion relationships.
5. It generates a structured report with citations.
6. A chatbot is created for that report, so you can ask follow-up questions without resending the report each time.
7. The chatbot retrieves evidence from the report-specific FAISS vector store and answers using only that company’s stored research context.

---

## Key Features

- **Company analysis by ticker**
- **SEC 10-K ingestion**
- **PDF upload support**
- **FAISS vector store per report**
- **Financial metrics extraction**
- **News collection and sentiment analysis**
- **Risk analysis with verification and citations**
- **GraphRAG / contagion analysis**
- **Interactive relationship graph visualization**
- **Live watchlist with real-time market metrics**
- **Session-based chat assistant**
- **Report-specific retrieval, no cross-report contamination**
- **React dashboard, reports page, graph page, and chatbot UI**

---

## Architecture

### System Overview
![System Overview](screenshots/System-overview.png)

### Multiagent System
![Multiagent System](screenshots/Multiagent-system.png)

### RAG Flow
![RAG Flow](screenshots/rag-flow.png)

### How the architecture works

- **Frontend** provides the UI for analysis, reports, graph view, watchlist, and chat.
- **FastAPI backend** orchestrates all analysis and chat endpoints.
- **LangGraph pipeline** runs the multi-agent workflow.
- **FAISS** stores chunk embeddings for each report separately.
- **Supabase/PostgreSQL** stores reports, sessions, messages, and watchlist data.
- **Groq** powers the final LLM responses.
- **GraphRAG** builds and visualizes company relationships, sector dependencies, macroeconomic risks, and contagion paths.

---

## Fallback Strategy

HiveLogic is designed to continue operating even when individual data sources are unavailable.

### Filing Retrieval

**Primary Source**

* SEC EDGAR 10-K filings

**Fallback**

* User-uploaded PDF documents

If a filing cannot be retrieved, analysis can continue using the uploaded report.

---

### Financial Metrics

**Primary Source**

* yfinance

**Fallback**

* Report generation continues without market metrics

The remaining analysis pipeline can still execute using filings, news, sentiment, and GraphRAG data.

---

### News Collection

**Primary Source**

* NewsAPI

**Fallback**

* Yahoo Finance RSS feeds

If NewsAPI is unavailable or returns insufficient results, HiveLogic falls back to Yahoo Finance RSS data. Sentiment analysis and risk analysis can still execute even when news coverage is limited.

---

### LLM Provider

**Primary Provider**

* Groq (Llama 3.3 70B)

**Optional Alternatives**

* Ollama (local inference)
* OpenAI
* Azure OpenAI

The architecture allows switching providers through environment configuration with minimal code changes.

---

### Retrieval

**Primary Source**

* Report-specific FAISS vector stores

**Fallback**

* Stored report context and database records

Even if vector retrieval fails, HiveLogic can still answer using the generated report, financial metrics, citations, and risk analysis stored in PostgreSQL/Supabase.

---

### Graph Analysis

**Primary Source**

* GraphRAG relationship store with interactive graph visualization

**Fallback**

* Risk analysis based on filings, financial metrics, and sentiment signals

Report generation can continue even when graph relationships are unavailable. GraphRAG enhances risk reasoning but is not required for the core report generation pipeline.

---

## Multi-Agent Pipeline

HiveLogic uses the following pipeline:

1. **Compliance Agent**
   - Blocks prohibited financial advice requests.

2. **Filing Agent**
   - Downloads the latest SEC filing or uses an uploaded PDF.
   - Chunks the text.
   - Builds a FAISS index for retrieval.

3. **Metrics Agent**
   - Fetches market cap, revenue, P/E, beta, debt-to-equity, and more.

4. **News Agent**
   - Fetches recent articles related to the company.

5. **Sentiment Agent**
   - Scores sentiment from news.

6. **Risk Agent**
   - Summarizes company risks using filing, metrics, sentiment, and graph context.

7. **Verification Agent**
   - Checks whether claims are supported by retrieved evidence.

8. **Citation Agent**
   - Builds citations from verified evidence and retrieved sources.

9. **Summary Agent**
   - Generates the final report and saves it to the database.
   - Copies the FAISS index into a report-specific vector store.

---

## Chat System

HiveLogic includes a chatbot that is tied to a specific report.

### Session flow

- A report is generated.
- A `ChatSession` is created for that report.
- The chatbot uses `session_id` only.
- The backend loads the linked `report_id`.
- The backend retrieves evidence from `data/vectorstores/{report_id}`.
- The chatbot answers using the report, metrics, citations, contagion risks, and retrieved evidence.

### Why this matters

This avoids sending the entire report every time and keeps the conversation isolated per report.

---

## Tech Stack

### Backend
- FastAPI
- LangGraph
- LangChain
- FAISS
- SQLAlchemy
- Supabase / PostgreSQL
- Groq
- HuggingFace embeddings
- yfinance
- newsapi-python
- BeautifulSoup
- pdfplumber

### Frontend
- React
- Vite
- Axios
- React Router
- React Flow
- CSS

---

## Folder Structure

```text
HIVELOGIC/
├── agents/
├── api/
├── compliance/
├── db/
├── frontend/
├── graph/
├── rag/
├── services/
├── requirements.txt
├── .gitignore
└── README.md
