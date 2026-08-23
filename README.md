# Repo ER Doctor 🩺

An autonomous AI agent that fixes failing tests in GitHub repositories. Built for the **All Things Agentic Hackathon**.

**Track:** Taskmaster  
**Tech:** Gemini 3.5 Flash · Google Cloud Run · Firestore · GitHub API · Python

---

## 🚨 The Problem

When a test fails, a developer has to:

1. Read the traceback
2. Find the culprit function
3. Write a fix
4. Run the test suite
5. Open a pull request

This takes time, is repetitive, and often requires deep context about the codebase. Most AI coding tools only chat — they don’t close the loop by verifying the fix actually works.

---

## 💡 The Solution

**Repo ER Doctor** is an AI agent that automates the entire loop:

```
Failing Test → Traceback Parser → Structural Context (tree-sitter)
       → Gemini 3.5 Flash Fix Generation → Docker Sandbox Verification
       → Green Tests? → Open Pull Request on GitHub
```

It’s an *agent*, not a chatbot. It **acts**: it reads the repo, generates a patch, runs tests, and proposes a pull request — all with hard verification.

---

## ✨ Features

- 🔍 **Automatic culprit detection** from pytest tracebacks
- 🧠 **Gemini 3.5 Flash** for code fix generation
- 🌳 **Structural context** via tree-sitter (extracts the exact failing function)
- 🐳 **Sandboxed verification** using Docker + pytest
- 🟢 **Binary success gate** — tests must pass before a PR is opened
- 🔁 **Retry loop** — if the fix fails, the agent gets the new test output and tries again
- 📦 **PR automation** — creates a branch, commits the fix, and opens a pull request with a summary
- ☁️ **Serverless deployment** on Google Cloud Run
- 🔥 **Firestore** for persisting agent run history

---

## 🏗️ Architecture

```
┌───────────────┐
│   User / CLI   │
│  (Hackathon   │
│     demo)     │
└───────┬───────┘
        │ POST /fix
        ▼
┌───────────────────────────────┐
│        Cloud Run Agent        │
│                               │
│  1. Fetch repo files (GitHub) │
│  2. Parse traceback           │
│  3. Extract structural context│
│  4. Call Gemini 3.5 Flash     │
│  5. Apply generated patch     │
│  6. Run pytest in sandbox     │
│  7. Verify green              │
│  8. Open Pull Request         │
│  9. Store result in Firestore │
└───────────────────────────────┘
        │                │
        ▼                ▼
┌───────────────┐  ┌────────────┐
│  Gemini API   │  │  Firestore  │
└───────────────┘  └────────────┘
```


---

## 🧰 Tech Stack

| Component       | Tool                          |
|-----------------|-------------------------------|
| AI Model        | Gemini 3.5 Flash (Gemini API) |
| Agent Orchestration | Python + Google ADK / GenAI SDK |
| Cloud Infrastructure | Google Cloud Run          |
| Persistence     | Firestore                     |
| Code Parsing    | tree-sitter (Python)          |
| Verification    | pytest + Docker               |
| GitHub Integration | PyGithub / REST API         |
| Local Dev       | GitHub Codespaces            |

---

## 📂 Project Structure

```
repo-er-doc/
├── main.py                 # FastAPI app, entrypoint
├── agent.py                # Core agent loop
├── github_api.py           # Fetch files, create PR
├── gemini_client.py        # Gemini API wrapper
├── traceback_parser.py     # Extract failing file/line
├── structural.py           # tree-sitter context extraction
├── verifier.py             # Docker sandbox + pytest runner
├── prompt_builder.py       # Builds Gemini prompt
├── requirements.txt
├── Dockerfile
├── README.md
└── LICENSE
```

---

## 🚀 Getting Started (Local / Codespaces)

### Prerequisites

- Python 3.10+
- GitHub account
- Gemini API key ((https://aistudio.google.com/apikey))
- A GitHub repository with at least one failing test (or use our demo repo)

### Setup

```bash
# 1. Clone the repo
git clone https://github.com/<your-username>/repo-er-doc.git
cd repo-er-doc

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Set environment variables
export GEMINI_API_KEY="your-gemini-api-key"
export GITHUB_TOKEN="your-github-personal-access-token"
```

### Run the Agent Locally

```bash
uvicorn main:app --reload
```

Then send a test request:

```bash
curl -X POST http://localhost:8000/fix \
  -H "Content-Type: application/json" \
  -d '{
    "repo_url": "https://github.com/your-username/repo-er-doc-demo",
    "test_path": "test_math_ops.py",
    "bug_description": "test_add_positive_numbers is failing"
  }'
```

The agent will:
1. Fetch the repo files
2. Run the tests to capture the traceback
3. Generate a fix with Gemini
4. Verify the fix
5. Open a pull request if successful

---

## ☁️ Deploy to Google Cloud Run

```bash
# Build and push Docker image
gcloud builds submit --tag gcr.io/<your-project-id>/repo-er-doc

# Deploy
gcloud run deploy repo-er-doc \
  --image gcr.io/<your-project-id>/repo-er-doc \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --set-env-vars GEMINI_API_KEY=your-key,GITHUB_TOKEN=your-token
```

---

## 🎥 Demo Video

[Link to 4‑minute demo video]

The demo shows:
- A repo with a red failing test
- The agent finding the culprit function
- Gemini generating a fix
- Tests turning green
- A pull request opening automatically

---

## ⚠️ Limitations

- Currently scoped to **Python repositories** with pytest tests
- Works best on single‑file or small multi‑file bugs
- May require multiple retries for complex bugs
- Does not yet handle flaky tests or environment‑specific failures

---

## 🔮 Future Work

- Support for JavaScript/TypeScript
- Integration with CI systems (GitHub Actions)
- Multi‑bug detection and batch fixing
- Fine‑tuned local model to reduce API costs
- Automatic issue creation with root‑cause analysis

---

## 📝 Submission Checklist

- [x] Public GitHub repo
- [x] Demo video (4 min)
- [x] Architecture diagram
- [x] Spin‑up instructions (this README)
- [x] Built with Gemini 3.5 Flash
- [x] Uses Google Cloud Run
- [x] Uses Firestore
- [x] Agent takes real action (opens PR)

---

## 📄 License

MIT © [leshorohloni5]

---

## 🙏 Acknowledgements

Built for the **All Things Agentic Hackathon**. Thanks to Google and Devpost for the challenge.
