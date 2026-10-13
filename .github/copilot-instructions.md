# Copilot Instructions

## Environment and commands

- This is a Python 3.12+ learning project managed with `uv`. Install the locked environment with `uv sync`; the only declared development dependency is `ipykernel`.
- Work is executed interactively in `lab2.ipynb`, `lab3.ipynb`, and `lab4.ipynb`; there are no configured build, test, lint, or single-test commands. Preserve notebook cell order when changing an exercise because later cells depend on variables and functions created earlier.
- Each notebook calls `load_dotenv(override=True)`. Keep credentials in the ignored `.env` file, never in a notebook or committed configuration. Required credentials depend on the notebook: `OPENAI_API_KEY` throughout; `ANTHROPIC_API_KEY` for `lab2`; `GOOGLE_API_KEY` for `lab3`; and `PUSHOVER_USER` plus `PUSHOVER_TOKEN` for `lab4`.
- `lab2` also invokes `ollama pull llama3.2` and then expects Ollama's OpenAI-compatible API at `http://localhost:11434/v1`.

## Architecture

- The notebooks are sequential AI-agent exercises rather than a packaged application. Keep each lesson self-contained instead of importing logic from a new shared module unless the project is deliberately reorganized.
- `lab2` generates a question with OpenAI, obtains answers from OpenAI, Anthropic, and local Ollama through their respective SDK interfaces, and presents the comparison in notebook output.
- `lab3` is a Gradio career-profile chatbot. It extracts text from `me/Linkedin.pdf` and reads `me/summary.txt`, embeds both in a system prompt, produces replies through OpenAI, and uses Gemini's OpenAI-compatible endpoint with a Pydantic `Evaluation` model to judge a response and retry once when rejected.
- `lab4` defines the next capability for that chatbot: OpenAI function-tool schemas map contact details and unanswerable questions to Pushover notifications via `requests.post`. Keep a Python function and its corresponding JSON schema aligned, including required parameters and `additionalProperties: False`.

## Repository conventions

- The personal-profile assets under `me/` are runtime context for the chatbot, not generic fixtures. Retain the relative paths (`me/Linkedin.pdf` and `me/summary.txt`) used by `lab3`.
- When adding a model provider, follow the existing pattern of constructing its client explicitly and using the provider's supported chat interface. Gemini is intentionally configured through `OpenAI(base_url="https://generativelanguage.googleapis.com/v1beta/openai/")` so structured parsing can return `Evaluation`.
- Gradio history is passed as `type="messages"` and is combined with the system prompt before the latest user message. Preserve that message ordering when modifying `chat`.
