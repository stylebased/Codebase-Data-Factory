# Codebase Data Factory

An automated pipeline to generate high-quality SFT training datasets from local code repositories.  
This system focuses on two core scenarios:

1. **Business Logic Q&A Generation (Scene-1)**  
2. **Architectural Design Proposal Generation (Scene-2)**


## 📚 Documentation

Detailed documentation is available in the `docs/` directory:

*   **[Design Document](docs/design_doc.md)**: System architecture, dataset schema, and generation strategies.
*   **[Configuration Guide](docs/configuration.md)**: Detailed explanation of environment variables and settings.

## ✨ Key Features

### 1. Business Process & Rule Q&A (Scenario 1)
Extracts business logic from code chunks and generates "Question-Reasoning-Answer" triplets.
- **Goal**: Teach the model *why* the code works the way it does.
- **Format**: Includes strictly formatted `thinking_trace` to improve model reasoning capabilities.

### 2. Architectural Design Proposal (Scenario 2)
Simulates new feature requirements and asks the model to design a solution that fits the existing project architecture.
- **Context**: Injects a compressed "Project Skeleton" and dependency list.
- **Style Matching**: Uses few-shot prompting with real code snippets to ensure the generated design matches the project's coding style.

## 🚀 Usage

### Prerequisites
- Python 3.9+
- An OpenAI-compatible API Key (e.g., OpenRouter, OpenAI)

### Installation
```bash
# Clone the repository
git clone https://github.com/your-username/local-repo-trainer.git
cd local-repo-trainer

# Install dependencies
pip install requests flask
```

### Configuration
The project uses `backend/config.py` for configuration.


> **API Configuration is Required**: You **MUST** configure your LLM API settings in `backend/config.py` before running the actual generation.
> *   Set `OPENAI_API_KEY` (or export it as an env var).
> *   Set `OPENAI_BASE_URL` if you are using a non-OpenAI provider (e.g., OpenRouter, local vLLM).
> *   Set `MODEL_NAME` to your desired model (default: `google/gemini-2.5-flash`).

You can override settings using environment variables. See **[Configuration Guide](docs/configuration.md)** for a full list of options (e.g., `TARGET_REPO_PATH`, `MODEL_NAME`, `CHUNK_TOKEN_LIMIT`).

### Running the Generator

**1. Generate Business Q&A Data (Scenario 1)**
```bash
# Dry-run (no API cost, uses mock data)
python run.py scene1 --limit 5 --dry-run

# Actual Generation
export OPENAI_API_KEY="sk-..."
python run.py scene1 --limit 100 --qa-count 3
```

**2. Generate Architecture Design Data (Scenario 2)**
```bash
# Dry-run
python run.py scene2 --count 2 --dry-run

# Actual Generation
python run.py scene2 --count 50
```

**3. Start Web UI (Optional)**
```bash
python run.py serve
```

## 📂 Output

Generated datasets are saved in the `data/` directory:

| File | Description |
| :--- | :--- |
| **`data/combined_sft.jsonl`** | **The Final Output**. This contains the merged and SFT-formatted data from both scenarios, ready for model fine-tuning. |
| `data/scene1_sft.jsonl` | SFT-formatted data for Business Q&A only. |
| `data/scene2_sft.jsonl` | SFT-formatted data for Architecture Design only. |
| `data/project_skeleton.txt` | The extracted directory tree of the target repo. |

