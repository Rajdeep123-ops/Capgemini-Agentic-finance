🐝 HiveLogic – Multi-Agent Financial Research & RAG PlatformA comprehensive AI-powered financial research platform that orchestrates LangGraph agents to analyze SEC filings, extract financial metrics, and generate evidence-backed reports with an interactive, session-based RAG chatbot.📖 OverviewHiveLogic is a prototype multi-agent system designed to streamline financial research. By analyzing a company's data, extracting evidence from SEC EDGAR filings and uploaded PDFs, and evaluating contagion risks, it generates structured, citation-backed research reports.Once a report is generated, users can seamlessly chat with it through a session-based Retrieval-Augmented Generation (RAG) chatbot. The platform ensures strict data isolation by creating a unique FAISS vector store for each report, eliminating cross-report contamination while providing deep, contextual insights.✨ Features🏢 Company Analysis: Deep-dive research by stock ticker (e.g., AAPL, NVDA).📄 Document Ingestion: Automated SEC 10-K retrieval and local PDF upload support.🧠 Multi-Agent Orchestration: Powered by LangGraph for modular, step-by-step reasoning.📊 Financial & Market Data: Live extraction of market cap, P/E, beta, and more.📰 Sentiment & Risk Analysis: Real-time news collection, sentiment scoring, and verifiable risk assessments.🕸️ GraphRAG & Contagion Analysis: Interactive relationship graph visualizations to track supply chain, competitor, and macroeconomic risks.💬 Session-Based Chatbot: Context-aware assistant linked exclusively to report-specific FAISS vector stores.📈 Live Watchlist: Real-time market metric tracking.⚡ Full-Stack Interface: Responsive React dashboard backed by a robust FastAPI and Supabase PostgreSQL architecture.🛠 Tech StackCategoryTechnologiesBackendFastAPI, SQLAlchemy, Supabase (PostgreSQL)FrontendReact, Vite, Axios, React Router, React FlowAI / OrchestrationLangGraph, LangChainLLMs & EmbeddingsGroq, HuggingFaceVector StorageFAISSData Processingyfinance, newsapi-python, BeautifulSoup, pdfplumber🏗 System ArchitecturePlaintextUser Input (Ticker / PDF)
   │
   ▼
Multi-Agent Pipeline (LangGraph)
   ├── Filing Agent (SEC 10-K / PDF)
   ├── Metrics Agent (yfinance)
   ├── News & Sentiment Agent (NewsAPI)
   └── Risk & Contagion Agent (GraphRAG)
   │
   ▼
Verification & Citation Building
   │
   ▼
Report Generation
   │
   ▼
Supabase (PostgreSQL)  <======>  Report-Specific FAISS Vector Store
(Stores sessions/reports)           (Stores chunked embeddings)
   │                                     │
   ▼                                     ▼
React Frontend         <======>  Session-Based RAG Chatbot
(Dashboard/Graph UI)                (Isolated Context per Report)
⚙️ How the Architecture WorksFrontend: Provides the UI for analysis, reports, graph view, watchlist, and chat.FastAPI Backend: Orchestrates all analysis and chat endpoints.LangGraph Pipeline: Runs the multi-agent workflow sequentially.FAISS: Stores chunk embeddings for each report separately.Supabase/PostgreSQL: Stores reports, sessions, messages, and watchlist data.Groq: Powers the final LLM responses (Llama 3.3 70B).GraphRAG: Builds and visualizes company relationships, sector dependencies, macroeconomic risks, and contagion paths.🤖 Multi-Agent PipelineHiveLogic utilizes a specialized sequence of agents to build its reports:Compliance Agent: Blocks prohibited financial advice requests.Filing Agent: Downloads the latest SEC filing (or uses an uploaded PDF), chunks the text, and builds a FAISS index.Metrics Agent: Fetches market cap, revenue, P/E, beta, debt-to-equity, etc.News Agent: Fetches recent articles related to the company.Sentiment Agent: Scores sentiment based on recent news.Risk Agent: Summarizes company risks using filings, metrics, sentiment, and graph context.Verification Agent: Checks whether claims are fully supported by retrieved evidence.Citation Agent: Builds citations from verified evidence and retrieved sources.Summary Agent: Generates the final report, saves it to the database, and isolates the FAISS index into a report-specific vector store.🔄 Example Application WorkflowUser enters a ticker and/or an optional PDF.Backend analyzes the filing, news, metrics, sentiment, and risks via LangGraph.Final report is generated and stored in the PostgreSQL database.FAISS index is copied to a designated, report-specific folder (e.g., api/data/vectorstores/{report_id}/).User opens the report in the Reports page.User initializes a Chat Session tied directly to that report_id.User asks follow-up questions.Backend retrieves evidence exclusively from that report’s vector store.Chatbot responds with highly grounded, evidence-backed answers.🛡️ Fallback StrategyHiveLogic is designed for high resilience. If primary data sources fail, the system gracefully degrades:ComponentPrimary SourceFallback MechanismFiling RetrievalSEC EDGAR 10-KUser-uploaded PDFsFinancial MetricsyfinanceContinues without market metricsNews CollectionNewsAPIYahoo Finance RSS feedsLLM ProviderGroq (Llama 3.3 70B)Ollama, OpenAI, or Azure OpenAIVector RetrievalReport-specific FAISSPostgreSQL database records and generated report contextGraph AnalysisGraphRAG relationship storeStandard risk analysis via filings, metrics, and sentiment signals📂 Project StructurePlaintextHIVELOGIC/
│
├── agents/       # Agent nodes, orchestration, and LLM handling
├── api/          # FastAPI routes
├── compliance/   # Guardrails and compliance checks
├── db/           # SQLAlchemy models and Supabase database setup
├── frontend/     # React application (pages, components, services)
├── graph/        # GraphRAG generation, contagion analysis, and visual APIs
├── rag/          # FAISS ingestion and retrieval logic
├── services/     # Helper services for sessions and chat messages
├── requirements.txt
├── .gitignore
└── README.md
💾 Database ModelsReports: Stores the final analysis (id, ticker, final_report, financial_metrics, sentiment_score, citations, contagion_risks, etc.).ChatSession: Links a chat interface to a specific report (id, report_id, ticker, title).ChatMessage: Stores conversational history (id, session_id, role, content).Watchlist: Tracks user-monitored tickers, pulling live yfinance data (price, daily change, market cap).🚀 InstallationClone RepositoryBashgit clone https://github.com/RibhvanPal/HiveLogic.git
cd HiveLogic
Backend SetupBashpython -m venv venv
Activate Environment (Windows):Bashvenv\Scripts\activate
Activate Environment (Linux/macOS):Bashsource venv/bin/activate
Install Dependencies:Bashpip install -r requirements.txt
Frontend SetupBashcd frontend
npm install
cd ..
Environment VariablesCreate a .env file in the project root:Code snippetGROQ_API_KEY=your_groq_api_key
GROQ_MODEL=llama-3.3-70b-versatile

NEWS_API_KEY=your_newsapi_key

DATABASE_URL=your_supabase_database_url

FAISS_INDEX_PATH=data/faiss_index
GRAPH_STORE_PATH=data/graph_store.json

ENV=development
PORT=8000
(Note: Alternative provider keys like OPENAI_API_KEY, AZURE_OPENAI_API_KEY, or OLLAMA_MODEL can be added if not using Groq).Run ApplicationStart the FastAPI Backend:Bashcd api
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
(⚠️ Deployment Note: Ensure the backend is launched from the correct working directory as the project currently utilizes relative data paths).Start the React Frontend (in a separate terminal):Bashcd frontend
npm run dev
📸 ScreenshotsDashboard & Analysis ResultsCore InterfacesReports PageChat PageWatchlist PageGraphRAG & Relationship VisualizationDisclaimer: HiveLogic is a research and analysis prototype. It does not provide financial advice. Always consult a qualified financial advisor before making investment decisions.👨‍💻 AuthorsRibhvan Pal (Author)Co-Authors:Ritam SurRajdeep Moulik (LinkedIn)Ankan PaulProtyusha Mondal⭐ If you found this project useful, consider giving it a star!
