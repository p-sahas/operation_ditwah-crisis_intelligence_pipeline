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


