<div align="center">

# ✈️ AI Travel Planner

**A multi-agent AI system that researches flights and hotels in parallel and delivers a personalised travel itinerary — powered by LangGraph, Amadeus API, and Streamlit.**

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![LangGraph](https://img.shields.io/badge/LangGraph-Multi--Agent-FF6B35?style=flat-square)](https://langchain-ai.github.io/langgraph/)
[![Streamlit](https://img.shields.io/badge/Streamlit-Web%20UI-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)](https://streamlit.io)
[![Amadeus](https://img.shields.io/badge/Amadeus-API-00A0E3?style=flat-square)](https://developers.amadeus.com)
[![Status](https://img.shields.io/badge/Status-Completed-22c55e?style=flat-square)]()

[How It Works](#-how-it-works) · [Architecture](#-architecture) · [Features](#-features) · [Setup](#-setup) · [Demo](#-demo) · [Disclaimer](#-disclaimer)

</div>

---

## 📖 Overview

The AI Travel Planner is a conversational agent that acts as a coordinated team of travel specialists. Describe your trip — origin, destination, budget, dates, number of travellers — and the system dispatches a **Flight Agent** and a **Hotel Agent** simultaneously to research real options via the Amadeus API.

Both agents return live pricing, ratings, and booking links. A central orchestrator consolidates their findings into a clean, budget-aware itinerary — all through a chat interface built with Streamlit.

---

## ✨ Features

- **Parallel multi-agent research** — flight and hotel agents run simultaneously, cutting wait time
- **Live data** — real prices, ratings, and Skyscanner booking links via the Amadeus API
- **Persistent memory** — `SqliteSaver` checkpointer maintains context across messages and session restarts
- **Conversational refinement** — say "too expensive" or "closer to the city centre" and the agent re-searches
- **Map-Reduce orchestration** — tasks are mapped to specialist sub-graphs and reduced into one coherent plan
- **Streamlit web UI** — clean chat interface served via `langgraph dev` + `langgraph_sdk`

---

## 🏗 Architecture

```
User (Streamlit Chat)
        │
        ▼
┌──────────────────────┐
│   Main Orchestrator  │  LangGraph StateGraph
│  Intake → Plan → Route│  TravelAgentState (TypedDict)
└───────┬──────────────┘
        │  Map — parallel dispatch
   ┌────┴────┐
   ▼         ▼
┌──────┐  ┌───────┐
│Flight│  │ Hotel │   Independent compiled sub-graphs
│Agent │  │ Agent │   Each with tool-calling + parser node
└──┬───┘  └───┬───┘
   │           │
   └─────┬─────┘
         │  Reduce — aggregate results
         ▼
  Final Itinerary Response
```

### Component Breakdown

| Component | Role |
|-----------|------|
| `TravelAgentState` | Central `TypedDict` — holds user inputs and results from both agents |
| **Travel Agent** sub-graph | Searches flights via Amadeus; generates Skyscanner deep links |
| **Hotel Agent** sub-graph | Searches hotels via Amadeus; returns real-time pricing and ratings |
| `SqliteSaver` | Persistent checkpointer — survives session restarts |
| `search_flight` / `search_hotel` | Custom LangGraph tools calling live Amadeus endpoints |
| Streamlit app | Chat UI connected to the graph API via `langgraph_sdk` |

---

## 🧠 Concepts Demonstrated

| Concept | Implementation |
|---------|---------------|
| **LangGraph** | `StateGraph` with custom reducers (`replace_value`, `operator.add`) for safe parallel state updates |
| **Tool Calling & RAG** | Agents retrieve live structured data from Amadeus rather than relying on static knowledge |
| **Persistent Memory** | `SqliteSaver` checkpointer preserves conversation context across turns and restarts |
| **Human-in-the-Loop** | Agent presents results then waits — users refine with natural follow-up messages |
| **Multi-Agent / Sub-Graphs** | Flight and hotel agents are independently compiled sub-graphs run by a parent orchestrator |
| **Map-Reduce** | Orchestrator maps research to both agents in parallel, then reduces findings into one plan |
| **Deployment** | Graph served as an API with `langgraph dev`; consumed by a Streamlit frontend |

---

## 🚀 Setup

### Prerequisites

- Python 3.9+
- [Amadeus API credentials](https://developers.amadeus.com) (free test tier)
- OpenAI API key (or compatible LLM provider)

### Install

```bash
git clone https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU.git
cd AryanRastogi72-LLM-Capstone-Project-MAT496-SNU

pip install -r requirements.txt
```

### Configure

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_key
AMADEUS_CLIENT_ID=your_amadeus_client_id
AMADEUS_CLIENT_SECRET=your_amadeus_client_secret
```

### Run

```bash
# Start the LangGraph API server
langgraph dev

# In a separate terminal, launch the Streamlit UI
streamlit run TravelPlannerWebAPP.py
```

---

## 📓 Build Log

The project was built incrementally — each step verified in isolation before adding the next layer of complexity.

| Step | Notebook | What was built |
|:----:|----------|----------------|
| 1 | [State & Graph + Persistent Memory](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/State_%26_Graph_With_PersistantMemory.ipynb) | `TravelAgentState`, custom reducers, `SqliteSaver` — verified with mock data |
| 2 | [Core Tools](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/Core_Tools.ipynb) | `search_flight` and `search_hotel` tools with hardcoded responses |
| 3 | [Travel Agent Sub-Graph](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/Travel_Sub_Graph.ipynb) | Flight-specialist sub-graph with output parser node |
| 4 | [Hotel Agent Sub-Graph](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/Accomadation_Sub_Graph.ipynb) | Accommodation sub-graph; parallel execution verified |
| 5 | [Map-Reduce Orchestrator](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/Map_Reduce.ipynb) | Main graph wiring parallel dispatch and result aggregation |
| 6 | [Amadeus API Integration](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/API_Implementations.ipynb) | Replaced all mock tools with live Amadeus endpoints |
| 7 | [Web App](https://github.com/MAT496-Monsoon2025-SNU/AryanRastogi72-LLM-Capstone-Project-MAT496-SNU/blob/main/TravelPlannerWebAPP.py) | Streamlit chat UI deployed against the `langgraph dev` API |
| 8 | [Video Demo](https://drive.google.com/file/d/1lClsOZuoNLCQWE7WR8_b6LKFLl0VfP4F/view?usp=drive_link) | Full walkthrough of the live application |

---

## 🎬 Demo

▶️ [**Watch the full video walkthrough**](https://drive.google.com/file/d/1lClsOZuoNLCQWE7WR8_b6LKFLl0VfP4F/view?usp=drive_link)

---

## ⚠️ Disclaimer

Flight and hotel data is sourced from the **Amadeus Test Environment**. Prices, availability, and booking details are for demonstration purposes only and may not reflect real-time market rates. Always verify with official booking platforms before making travel decisions.

---

<div align="center">

Built by [Aryan Rastogi](https://github.com/AryanRastogi72) · MAT496 LLM Capstone · SNU Monsoon 2025

</div>
