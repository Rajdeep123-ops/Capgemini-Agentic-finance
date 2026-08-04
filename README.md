## Platform HiveLogic – Multi-Agent Financial Research & RAG

An AI-driven financial research prototype that orchestrates LangGraph agents to analyze SEC filings, extract market metrics, and power a session-based GraphRAG chatbot for evidence-backed insights.

expand

tune

chat_spark

## Overview

HiveLogic is a multi-agent financial research platform designed to automate complex company analysis. By taking a stock ticker or an uploaded PDF, the system extracts critical evidence from SEC filings, financial metrics, and live news to generate a structured, citation-backed research report.

Beyond standard report generation, HiveLogic builds a session-based RAG chatbot tied explicitly to the generated report. This ensures that follow-up questions retrieve evidence directly from a report-specific FAISS vector store—eliminating cross-report contamination and maintaining strict, localized context.

## Features

- AAPL , NVDA ). Automated Company Analysis: Fetch insights instantly using just a stock ticker (e.g.,

- PDFs. Dynamic Data Ingestion: Process the latest SEC 10-K filings or prioritize user-uploaded

- contamination. Report-Specific RAG: Individual FAISS vector stores for every report to prevent data

- ratios. Live Financial Metrics: Real-time extraction of market cap, P/E, beta, and debt-to-equity

- Sentiment & Risk Analysis: Automated news collection, sentiment scoring, and verifiable risk summarization.

- 🕸️ GraphRAG Contagion Analysis: Interactive relationship graph visualizations mapping macroeconomic and sector dependencies.

- Isolated Chat Sessions: Query the generated report in a persistent chat interface without needing to resend context.

- Interactive Watchlist: Track live market metrics for saved tickers.

## Tech Stack

Category

Technologies

Backend

FastAPI, Python

Frontend

React, Vite, Axios, React Router, React Flow, CSS

Agent Orchestration

LangGraph, LangChain

Vector Store

FAISS


Supabase, PostgreSQL, SQLAlchemy

Database

Groq (Llama 3.3 70B), HuggingFace Embeddings

LLM & Embeddings

yfinance, newsapi-python, BeautifulSoup, pdfplumber

Data Collection

## System Architecture

## Multi-Agent Flow

```
User Input (Ticker / PDF)
│
LangGraph Multi-Agent Pipeline
├── 🛡️ Compliance Agent (Filters requests)
├── Filing Agent (SEC 10-K / PDF) ──▶ FAISS Vector Store
├── Metrics Agent (yfinance)
├── News & Sentiment Agents (NewsAPI)
└── ⚠️ Risk & Verification Agents
│
🕸️ GraphRAG Contagion Analysis
│
Summary Agent (Report & Citations)
│
🗄️ PostgreSQL / Supabase
│
Session-Based Chatbot (Report-Specific Retrieval)
│
🖥️ React Frontend (Dashboard, Graph View, Chat)
```

(Note: Architecture diagrams for System Overview, Multiagent System, and RAG Flow can be found

in the screenshots/ directory).

## Application Workflow

- 1. User Request: User enters a stock ticker and an optional PDF document.

- 2. Compliance Check: Agent blocks prohibited financial advice requests.

- 3. Data Ingestion: Filing Agent downloads the SEC 10-K (or reads the PDF), chunks the text, and builds a FAISS index.

- 4. Market & News Gathering: Metrics Agent fetches financial data; News & Sentiment Agents collect and score recent articles.

- 5. Risk & Verification: Risk Agent summarizes company vulnerabilities based on the gathered data, while the Verification Agent ensures all claims are supported by retrieved evidence.

- 6. Citation & Summary: The system builds citations and generates the final report.


- 7. Storage: The final report is saved to the database, and the FAISS index is copied to a report- specific folder (api/data/vectorstores/{report_id}/ ).

- 8. Interactive Chat: The user opens a chat session linked only to that specific report_id , allowing the chatbot to answer using strictly grounded, isolated evidence.

## 🛡️ Resilience & Fallback Strategy

HiveLogic is designed for high availability, utilizing graceful degradation if primary data sources fail.

| Component Primary Source | Fallback Mechanism |
| --- | --- |
| Filing Retrieval SEC EDGAR 10-K Financial Metrics yfinance News Collection NewsAPI Retrieval FAISS Vector Store Graph Analysis GraphRAG Store | User-uploaded PDFs Pipeline continues without market metrics Yahoo Finance RSS feeds LLM Provider Groq (Llama 3.3 70B) Ollama (Local), OpenAI, Azure OpenAI PostgreSQL stored report context/citations Basic risk analysis via metrics/sentiment |

## 🗄️ Database Models

- Reports: Stores the final analysis (id , ticker , final_report , financial_metrics , sentiment_score , citations , contagion_risks ).

- ChatSession: Links a chat to a specific report (id , report_id , ticker , title ).

- ChatMessage: Stores conversation history (id , session_id , role , content ).

- Watchlist: Tracks tickers for real-time monitoring (Current price, daily % change, market cap).

## Project Structure

```
HIVELOGIC/
│
├── api/ # FastAPI backend routes & main app
│ ├── agents/ # LangGraph nodes and LLM handling
│ ├── db/ # SQLAlchemy models and database setup
│ ├── graph/ # GraphRAG analysis and visualization APIs
│ ├── rag/ # FAISS ingestion and retrieval
│ └── services/ # Helper services (sessions, messages)
│
├── frontend/ # React + Vite application
│ ├── src/pages/ # Dashboard, Reports, Graph, Chat, Watchlist
│ ├── src/components/ # Reusable UI pieces & Navbar
│ └── src/services/ # API client
│
├── compliance/ # Safety and financial advice filters
├── requirements.txt
├── .env # Environment configurations
└── README.md
```


## 1. Clone Repository

```
git clone https://github.com/RibhvanPal/HiveLogic.git
cd HiveLogic
```

## 2. Setup Backend Environment

## Windows:

```
python -m venv venv
venv\Scripts\activate
```

## Linux / macOS:

```
python -m venv venv
source venv/bin/activate
```

## Install backend dependencies:

```
pip install -r requirements.txt
```

## 3. Setup Frontend Environment

```
cd frontend
npm install
cd ..
```

## 4. Configure Environment Variables

Create a .env file in the project root:

```
GROQ_API_KEY=your_groq_api_key
GROQ_MODEL=llama-3.3-70b-versatile
NEWS_API_KEY=your_newsapi_key
DATABASE_URL=your_supabase_database_url
FAISS_INDEX_PATH=data/faiss_index
GRAPH_STORE_PATH=data/graph_store.json
ENV=development
PORT=8000
```


## 5. Run Application

## Terminal 1 (Backend):

```
cd api
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Note: Because the project uses relative data paths, ensure the backend is launched from the api directory before production deployment.

## Terminal 2 (Frontend):

```
cd frontend
npm run dev
```

## 🕸️ GraphRAG Visualization

HiveLogic provides an interactive React Flow graph view that visualizes:

- Supply chain & revenue dependencies

- Competitor relationships

- Currency & regulatory risks

- Macroeconomic exposure

Relationships are dynamically mapped using known dataset connections, yfinance industry data, and LLM-generated insights, allowing users to explore complex contagion paths.

## Screenshots

(Ensure images are located in the screenshots/ directory relative to this README)

Dashboard & Analysis

## Reports & Watchlist

## Chat & GraphRAG

## ⚠️ Disclaimer

HiveLogic is a research and analysis tool. It does not provide financial advice. Always consult a qualified financial advisor before making investment decisions.

## Team

Author: Ribhvan Pal

Co-Authors: Ritam Sur, Rajdeep Moulik, Ankan Paul, Protyusha Mondal
