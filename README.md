```

```

Operation Ditwah – Crisis Intelligence Pipeline

## Overview

Operation Ditwah is a specialized Crisis Intelligence Pipeline designed to support disaster response efforts through advanced Large Language Model (LLM) integration. This system processes incoming disaster-related messages, classifies them by intent and priority, and employs advanced reasoning techniques (Chain of Thought, Tree of Thoughts) to optimize logistics and resource allocation (e.g., rescue boat routing).

The project is built as a modular Python framework with Jupyter notebooks serving as the primary interface for experimentation and workflow execution. It supports multiple LLM providers (OpenAI, Google Gemini, Groq) and includes robust systems for token management, cost estimation, and logging.

## Key Features

* **Multi-Provider LLM Support**: Seamlessly integrate and switch between OpenAI (GPT-4o), Google (Gemini 2.0 Flash), and Groq (Llama 3.1) based on task requirements.
* **Advanced Reasoning Strategies**:
  * **Chain of Thought (CoT)**: Step-by-step reasoning for complex priority scoring.
  * **Tree of Thoughts (ToT)**: Exploration of multiple logical branches to solve logistical routing problems.
* **Automated Classification**: Few-shot learning implementations to categorize messages into "Rescue", "Supply", "Info", or "Other" with associated priority levels.
* **Robust Architecture**:
  * **Smart Routing**: Automatically directs tasks to the most appropriate model (e.g., fast models for classification, reasoning models for logic).
  * **Resilience**: Built-in retry mechanisms, rate limit handling, and error backoff.
  * **Observability**: Detailed logging of token usage, latency, and cost estimates for every API call.

## Project Structure

```
├── config/                 # Configuration files
│   ├── config.yaml         # Main system config (providers, token limits, logging)
│   └── models.yaml         # Model definitions (tiers: general, strong, reason)
├── data/                   # Input data and datasets
│   ├── incidents.txt       # Sample disaster incident reports
│   └── sample_messages.txt # Raw messages for classification testing
├── notebooks/              # Interactive workflows
│   ├── 00_setup_and_verification.ipynb    # Setup, API key validation, and smoke tests
│   ├── 01_few_shot_classification.ipynb   # Message classification implementation
│   ├── 02_temperature_stability_test.ipynb# Model stability analysis
│   ├── 03_logistics_commander_cot_tot.ipynb # Advanced reasoning (CoT & ToT)
│   ├── 04_token_economics.ipynb           # Cost and token usage analysis
│   └── 05_news_feed_extraction.ipynb      # Intelligence extraction from news sources
├── outputs/                # Generated artifacts (CSVs, reports)
├── utils/                  # Core utility modules
│   ├── llm_client.py       # Unified client for all LLM providers
│   ├── router.py           # Logic for model selection
│   ├── prompts.py          # Prompt template management
│   ├── locking_utils.py    # Logging system
│   └── token_utils.py      # Token counting and estimation
└── requirements.txt        # Python dependencies
```

## Setup & Installation

### Prerequisites

* Python 3.10+
* API Keys for at least one supported provider (OpenAI, Google, or Groq)

### Installation

1. **Clone the repository** or navigate to the project directory.
2. **Install dependencies**:

   ```bash
   pip install -r requirements.txt
   ```

   *Alternatively, if using uv:*

   ```bash
   uv sync
   ```
3. **Environment Configuration**:
   Create a `.env` file in the root directory and add your API keys:

   ```env
   OPENAI_API_KEY=your_key_here
   GEMINI_API_KEY=your_key_here
   GROQ_API_KEY=your_key_here
   ```

## Configuration

The system is highly configurable via `config/config.yaml`:

* **Providers**: Enable/disable specific providers.
* **Models**: Define default models for different tasks (extraction, creation, reasoning).
* **Safety**: Configure PII detection and input sanitization.
* **Retries**: Adjust max retries and backoff strategies for API stability.

Model specific mappings can be found and updated in `config/models.yaml`, organizing models into "general", "strong", and "reason" tiers.

## Notebook Workflows & Usage Guide

The project is organized into a series of educational and functional notebooks. It is recommended to run them in the following order:

### 1. 00_setup_and_verification.ipynb

**Purpose**: System Health Check

* **What it does**: Verifies that your environment is correctly configured. It checks for the presence of API keys (OpenAI, Gemini, Groq), initializes the custom `LLMClient`, and runs a simple "Hello World" smoke test against all enabled providers.
* **Key Output**: A `runs.csv` log file created in the `logs/` directory, establishing that the logging system is active.

### 2. 01_few_shot_classification.ipynb

**Purpose**: Message Classification (The "Dispatcher")

* **Concept**: Uses **Few-Shot Prompting** to teach the model how to classify incoming disaster messages without fine-tuning.
* **Process**:
  1. Loads raw text messages from `data/sample_messages.txt`.
  2. Provides the LLM with 3-4 examples of correct classifications (e.g., mapping "water rising" to "Rescue" intent).
  3. Classifies new messages into structured fields: `District`, `Intent` (Rescue/Supply/Info/Other), and `Priority`.
* **Output**: A clean CSV `outputs/classified_messages_few_shot.csv` ready for downstream processing.

### 3. 02_temperature_stability_test.ipynb

**Purpose**: Reliability Testing (The "Stress Test")

* **Concept**: Demonstrates the impact of the `temperature` parameter on model determinism.
* **Experiment**:
  * **Chaos Mode (Temp = 1.0)**: Runs scenarios with high randomness to show how models can hallucinate or produce inconsistent safety-critical outputs.
  * **Safe Mode (Temp = 0.0)**: Runs the same scenarios to demonstrate consistent, reliable decision-making required for crisis response.
* **Takeaway**: In disaster systems, always use low temperature (0.0 - 0.2) for predictable logic.

### 4. 03_logistics_commander_cot_tot.ipynb

**Purpose**: Advanced Reasoning (The "Brain")

* **Concept**: Implements **Chain of Thought (CoT)** and **Tree of Thoughts (ToT)** reasoning strategies.
* **CoT Workflow**: The model is forced to "think silently" step-by-step to calculate priority scores for incidents based on complex rules (e.g., age vulnerability, medical urgency).
* **ToT Workflow**: The model explores multiple routing strategies (Greedy vs. Closest-First vs. Logistics-Optimized) to solve a "Traveling Salesperson" style rescue boat problem. It evaluates each branch and selects the optimal path to save the most lives in the shortest time.

### 5. 04_token_economics.ipynb

**Purpose**: Cost & Context Management (The "Budget Keeper")

* **Concept**: Manages the trade-off between context context window limits and costs.
* **Mechanism**:
  * Calculates the token count for every incoming message before sending it to the LLM.
  * If a message exceeds the `TOKEN_LIMIT`, it triggers a separate "Summarizer" agent to compress the text while retaining critical information.
  * Processes the safe/summarized text for final output.
* **Outcome**: Prevents context window overflows and reduces API costs by avoiding processing of unnecessarily long verbose inputs.

### 6. 05_news_feed_extraction.ipynb

**Purpose**: Intelligence Gathering (The "Analyst")

* **Concept**: Extracts structured data from unstructured real-time news feeds.
* **Tech Stack**: Uses **Pydantic** models and **Structured Outputs (JSON Mode)** to enforce strict schema validation.
* **Process**:
  1. Scrapes/Reads raw news headlines.
  2. Extracts entities: `District`, `Flood Level`, `Victim Count`, `Main Need`, and `Status`.
  3. Validates the data against a pre-defined schema (e.g., ensuring `District` is a valid Sri Lankan district).
  4. Filters out invalid or irrelevant items.
* **Output**: A validated Excel report `outputs/flood_report.xlsx` containing only high-confidence intelligence.

## Logging and Monitoring

All LLM interactions are logged to `logs/runs.csv`. This log file includes:

* Timestamp and Provider/Model used
* Input/Output token counts (estimated vs actual)
* Latency (ms)
* Cost estimates

Use the logging utilities in `utils/logging_utils.py` to analyze this data programmatically.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.