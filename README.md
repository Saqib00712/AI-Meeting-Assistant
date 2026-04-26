# AI Meeting Assistant
> An end-to-end AI pipeline that transcribes meeting audio, corrects financial terminology, and generates structured meeting minutes with task lists — powered by OpenAI Whisper, IBM Granite, Meta Llama 3.2, LangChain, and Gradio.

---

## What This Project Does

Upload any meeting audio file and the pipeline handles everything automatically in 3 steps:

```
Audio File (MP3/WAV)
        ↓
[Whisper] — speech-to-text transcription
        ↓
[Llama 3.2] — financial terminology correction
        ↓
[IBM Granite + LangChain] — meeting minutes + task list generation
        ↓
Gradio UI — view output + download .txt file
```

---

## Demo

**Input:** Audio recording of a financial earnings call

**Step 1 — Raw Transcript (Whisper):**
```
"...our HSA contributions grew by twelve percent, and the four zero one k 
enrollment rate increased significantly in Q3..."
```

**Step 2 — Corrected Transcript (Llama 3.2):**
```
"...our Health Savings Account (HSA) contributions grew by twelve percent, 
and the 401(k) (Retirement Savings Plan) enrollment rate increased 
significantly in Q3..."
```

**Step 3 — Meeting Minutes (IBM Granite):**
```
Meeting Minutes:
- HSA contributions grew 12% in Q3
- 401(k) enrollment increased significantly
- ROI targets discussed for Q4

Task List:
- [Finance Team] Prepare HSA contribution report — Due: Friday
- [HR] Send 401(k) enrollment update to all staff — Due: Monday
```

---

## Pipeline Architecture

```
[1] Audio Input (Gradio)
        ↓
[2] OpenAI Whisper Medium
    → automatic-speech-recognition
    → chunk_length_s=30, batch_size=8
        ↓
[3] remove_non_ascii() — text cleaning
        ↓
[4] Meta Llama 3.2 Vision (11B) via IBM WatsonX
    → financial product terminology correction
    → e.g. "four zero one k" → "401(k) (Retirement Savings Plan)"
    → e.g. "HSA" → "Health Savings Account (HSA)"
        ↓
[5] IBM Granite 3.3 (8B) via LangChain + WatsonxLLM
    → generates meeting minutes and task list
    → uses ChatPromptTemplate + RunnablePassthrough + StrOutputParser
        ↓
[6] Output: Gradio Textbox + downloadable .txt file
```

---

## Financial Terminology Corrections

The Llama 3.2 agent automatically handles:

| Raw Input | Corrected Output |
|-----------|-----------------|
| `HSA` | Health Savings Account (HSA) |
| `four zero one k` | 401(k) (Retirement Savings Plan) |
| `five two nine` | 529 (Education Savings Plan) |
| `ROA` | Return on Assets (ROA) |
| `VaR` | Value at Risk (VaR) |
| `PB` | Price to Book (PB) ratio |
| `LTV` | Loan to Value (LTV) or Lifetime Value (LTV) — context-aware |

Context-aware disambiguation — LTV is resolved correctly based on surrounding text.

---

## Tech Stack

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square)
![Whisper](https://img.shields.io/badge/OpenAI-Whisper%20Medium-black?style=flat-square)
![IBM Granite](https://img.shields.io/badge/IBM-Granite%203.3-blue?style=flat-square)
![Llama](https://img.shields.io/badge/Meta-Llama%203.2%2011B-purple?style=flat-square)
![LangChain](https://img.shields.io/badge/LangChain-Chain-green?style=flat-square)
![Gradio](https://img.shields.io/badge/Gradio-UI-orange?style=flat-square)

- **OpenAI Whisper Medium** — speech-to-text transcription via HuggingFace pipeline
- **Meta Llama 3.2 Vision (11B)** — financial terminology correction via IBM WatsonX
- **IBM Granite 3.3 (8B)** — meeting minutes and task generation via WatsonxLLM
- **LangChain** — `ChatPromptTemplate`, `RunnablePassthrough`, `StrOutputParser` chain
- **Gradio** — web UI with audio upload, text output, and file download
- **PyTorch** — backend for Whisper model inference

---

## Project Structure

```
AI-Meeting-Assistant/
│
├── app.py                              # Full pipeline and Gradio UI
├── requirements.txt                    # All dependencies
├── .env.example                        # API key template (for local use)
├── meeting_minutes_and_tasks.txt       # Auto-generated output file
└── README.md
```

---

## Getting Started

### 1. Clone the repo
```bash
git clone https://github.com/Saqib00712/AI-Meeting-Assistant.git
cd AI-Meeting-Assistant
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Set up API key (only needed outside IBM Skills Network)
```bash
cp .env.example .env
```
Edit `.env`:
```
WATSONX_API_KEY=your_ibm_watsonx_api_key_here
WATSONX_PROJECT_ID=your_project_id_here
```

> If running inside IBM Skills Network JupyterLab, no API key is needed — the environment is pre-configured.

### 4. Run the app
```bash
python app.py
```

### 5. Open your browser
```
http://127.0.0.1:7860
```
Upload an audio file (MP3 or WAV) and get meeting minutes instantly.

---

## Key Concepts Covered

- **HuggingFace pipeline** — loading and running Whisper for automatic speech recognition
- **Chunked audio processing** — `chunk_length_s=30` and `batch_size=8` for long audio files
- **IBM WatsonX ModelInference** — direct chat API call for Llama 3.2 with custom `TextChatParameters`
- **WatsonxLLM + LangChain** — wrapping IBM Granite in LangChain for prompt chaining
- **ChatPromptTemplate** — structured prompt with context variable injection
- **RunnablePassthrough** — passing context directly into the chain without transformation
- **StrOutputParser** — extracting clean text from LLM response
- **Multi-model pipeline** — combining Whisper + Llama 3.2 + IBM Granite in a single workflow
- **Gradio file download** — returning both text output and a downloadable `.txt` file
- **ASCII cleaning** — removing non-ASCII characters from raw transcripts before LLM processing

---

## Use Cases

- Financial earnings call transcription and summarization
- Board meeting minutes generation
- Team standup notes with action items
- Legal or compliance meeting documentation
- Any domain — just change the system prompt in `product_assistant()`

---

## Related Certifications

Built as part of the IBM **Generative AI Engineering with Transformers & LLMs** and **Build Multimodal Generative AI Applications** Specializations on Coursera.

[![IBM Badge](https://img.shields.io/badge/IBM-Generative%20AI%20Engineering-blue?style=flat-square)](https://www.credly.com/users/muhammad-saqib.361f9b8c)
[![IBM Badge](https://img.shields.io/badge/IBM-Multimodal%20AI%20Applications-blue?style=flat-square)](https://www.credly.com/users/muhammad-saqib.361f9b8c)

---

## Author

**Muhammad Saqib**
- GitHub: [@Saqib00712](https://github.com/Saqib00712)
- LinkedIn: [muhammad-saqib](https://www.linkedin.com/in/muhammad-saqib-68b9b3374/)
- Email: saqibkhosa649@gmail.com
- Credly: [15x IBM Certified](https://www.credly.com/users/muhammad-saqib.361f9b8c)
