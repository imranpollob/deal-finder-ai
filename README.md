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

### Prerequisites
- Python 3.8 or higher
- pip package manager
- Virtual environment (recommended)

### Step 1: Create Virtual Environment
```sh
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

### Step 2: Install Dependencies
```sh
pip install -r requirements.txt
```

**Note:** The installation includes several heavy packages (PyTorch, Transformers, ChromaDB). This may take several minutes.

### Step 3: Set Up Environment Variables
1. Copy the example environment file:
   ```sh
   cp .env.example .env
   ```

2. Edit `.env` and replace the placeholder values with your actual API keys.

   The `.env.example` file contains all available configuration options with helpful comments. Here's what you need:

   **Required (Minimum Setup):**
   - `OPENAI_API_KEY` - Get from [OpenAI Platform](https://platform.openai.com/api-keys)

   **Highly Recommended (Choose one for notifications):**
   - `PUSHOVER_USER` & `PUSHOVER_TOKEN` - Get from [Pushover](https://pushover.net/) (recommended)
   - `TWILIO_*` variables - For SMS notifications via [Twilio](https://www.twilio.com/)

   **Optional (Enhanced Features):**
   - `MODAL_TOKEN` - For the fine-tuned Specialist agent
   - `DEEPSEEK_API_KEY` - Alternative to OpenAI for cost savings

   See the `.env.example` file for the complete list with detailed comments and links.

### Step 4: Set Up Required Model Files
You need two pre-trained model files in your project root:
- `ensemble_model.pkl` - Linear regression ensemble model
- `random_forest_model.pkl` - Random forest price predictor

**Options:**
- Train these models yourself using your own product dataset
- Contact the project maintainer for pre-trained models
- Modify `agents/ensemble_agent.py` to handle missing models gracefully

### Step 5: Configure Vector Database (Automatic)
The ChromaDB vector database will be created automatically in `products_vectorstore/` when you first run the application. The database is used by the FrontierAgent for RAG-based price estimation.

### Step 6: (Optional) Deploy Specialist Agent to Modal
If you want to use the fine-tuned SpecialistAgent:
1. Train and deploy your fine-tuned LLM to Modal as "pricer-service"
2. Ensure the deployment name matches: `modal.Cls.from_name("pricer-service", "Pricer")`
3. Set your `MODAL_TOKEN` in `.env`

**Note:** The system will work without the Specialist agent - the ensemble will use the other two agents.

### Step 7: Run the Application
```sh
python price_is_right_final.py
```

The Gradio web interface will launch automatically. By default it runs on `http://localhost:7860`.

**What happens when you run:**
- 🔍 Scans for deals every 5 minutes (configurable)
- 📊 Displays found deals in an interactive table
- 📝 Shows live logs of agent activity
- 📈 Visualizes product embeddings in 3D space
- 🔔 Sends notifications for deals with $50+ discount

### Troubleshooting

**ImportError for transformers/torch:**
```sh
pip install --upgrade torch transformers
```

**ChromaDB issues:**
```sh
pip install --upgrade chromadb protobuf==3.20.2
```

**OpenAI API errors:**
- Verify your API key is correct in `.env`
- Check you have available credits at [OpenAI Platform](https://platform.openai.com/usage)

**No deals found:**
- Check RSS feeds are accessible
- Verify OpenAI API is working
- Review logs in the Gradio UI for errors

---

## Environment Variables

The `.env.example` file contains all available configuration options with detailed comments and setup instructions. 

**Quick Reference:**

### Required
- `OPENAI_API_KEY` - OpenAI API key for GPT-4o-mini

### Notifications (Choose at least one)
- `PUSHOVER_USER` & `PUSHOVER_TOKEN` - Pushover push notifications (recommended)
- `TWILIO_*` - SMS notifications via Twilio (alternative)

### Optional (Enhanced Features)
- `MODAL_TOKEN` - For fine-tuned Specialist agent deployment
- `DEEPSEEK_API_KEY` - Alternative LLM provider for cost savings

**Setup:** Copy `.env.example` to `.env` and fill in your actual API keys. The example file includes helpful links and instructions for obtaining each key.

---

## Notes
- The app will launch a Gradio UI in your browser.
- Some features (e.g., push/SMS notifications, Modal specialist agent) require valid API keys and/or cloud setup.
- For best results, ensure all dependencies and model files are available.

---

## License
MIT License
