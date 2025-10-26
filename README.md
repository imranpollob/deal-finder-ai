# Deal Finder Project

Deal Finder is an autonomous, multi-agent framework that discovers, evaluates, and notifies you about the best online deals using LLMs, vector search, and ensemble modeling. It features a modular architecture, interactive UI, and extensible agent design.

---

## Table of Contents
- [Deal Finder Project](#deal-finder-project)
  - [Table of Contents](#table-of-contents)
  - [Features](#features)
  - [Project Architecture](#project-architecture)
  - [How It Works](#how-it-works)
  - [Folder Structure](#folder-structure)
  - [Setup Instructions](#setup-instructions)
  - [Environment Variables](#environment-variables)
  - [Notes](#notes)
  - [License](#license)

---

## Features
- Scrapes and summarizes deals from multiple online sources
- Uses LLMs (OpenAI, Modal, etc.) for product description and price estimation
- Ensemble modeling with specialist, frontier, and random forest agents
- Vector search with ChromaDB for product similarity
- Push notifications via Pushover (or SMS via Twilio)
- Interactive Gradio UI for monitoring and visualization

---

## Project Architecture

**Agents:**
- `ScannerAgent`: Scrapes and summarizes deals from RSS feeds using LLMs.
- `PlanningAgent`: Orchestrates the workflow, coordinating the scanner, ensemble, and messaging agents.
- `EnsembleAgent`: Combines predictions from multiple models (Specialist, Frontier, Random Forest) to estimate product prices.
- `SpecialistAgent`: Calls a fine-tuned LLM (deployed on Modal) for price estimation.
- `FrontierAgent`: Uses vector search (ChromaDB) and LLMs (OpenAI/DeepSeek) to estimate prices based on similar products.
- `RandomForestAgent`: Uses a traditional ML model for price estimation.
- `MessagingAgent`: Sends notifications (push or SMS) for the best deals found.

**Other Components:**
- **Vector Database (ChromaDB):** Stores product embeddings for similarity search, used by the FrontierAgent.
- **Gradio UI:** Provides a web interface to monitor, visualize, and interact with the agent framework.
- **Model Files:** Pre-trained models (e.g., `ensemble_model.pkl`, `random_forest_model.pkl`) are used for price estimation.

---

## How It Works
1. **Deal Scraping:** The `ScannerAgent` fetches deals from RSS feeds and summarizes them using LLM prompts.
2. **Deal Selection:** The agent selects the most promising deals based on description quality and price clarity.
3. **Price Estimation:** For each deal, the `PlanningAgent` uses the `EnsembleAgent` to estimate the true price. The ensemble combines:
    - SpecialistAgent (fine-tuned LLM)
    - FrontierAgent (vector search + LLM)
    - RandomForestAgent (ML model)
4. **Opportunity Evaluation:** The estimated price is compared to the deal price to calculate the discount.
5. **Notification:** If a deal exceeds the discount threshold, the `MessagingAgent` sends a notification (push or SMS).
6. **Visualization:** The Gradio UI displays found deals, logs, and a 3D plot of product embeddings.

---

## Folder Structure
```
deals_finder/
  ├── agents/                  # All agent classes (scanner, planner, ensemble, etc.)
  ├── items.py                 # Item data structure and utilities
  ├── testing.py               # Testing utilities
  ├── deal_agent_framework.py  # Main agent framework
  ├── log_utils.py             # Logging and formatting utilities
  ├── price_is_right_final.py  # Main app (Gradio UI)
  ├── requirements.txt         # Python dependencies
  ├── .env.example             # Example environment variables
  └── README.md                # Project documentation
```

---

## Setup Instructions
1. **Create and activate a virtual environment:**
   ```sh
   python3 -m venv .venv
   source .venv/bin/activate
   ```
2. **Install dependencies:**
   ```sh
   pip install -r requirements.txt
   ```
3. **Set up environment variables:**
   - Copy `.env.example` to `.env` and fill in your API keys (OpenAI, Pushover, Twilio, etc.)
4. **Download or train required model files:**
   - Place `ensemble_model.pkl` and `random_forest_model.pkl` in the project root if not already present.
5. **Run the app:**
   ```sh
   python price_is_right_final.py
   ```

---

## Environment Variables
See `.env.example` for all required variables. Example:
```
OPENAI_API_KEY=your-openai-key
PUSHOVER_USER=your-pushover-user
PUSHOVER_TOKEN=your-pushover-token
# ...other keys as needed
```

---

## Notes
- The app will launch a Gradio UI in your browser.
- Some features (e.g., push/SMS notifications, Modal specialist agent) require valid API keys and/or cloud setup.
- For best results, ensure all dependencies and model files are available.

---

## License
MIT License
