# Smart Email Assistant
### Privacy-Preserving LLM-Powered Email Management System

> An AI email client that summarizes threads, categorizes messages, and suggests replies — entirely on-device, with no data ever sent to external servers.

---

## Overview

Modern AI email tools (Gmail, Outlook) require your data to be processed in the cloud. **Smart Email Assistant** challenges that assumption — delivering state-of-the-art summarization, categorization, and reply suggestions while keeping all user content strictly local.

Built as a capstone project at Central Michigan University (CPS 698, Fall 2025).

---

## Features

- **Thread Summarization** — Condenses long email threads into concise, coherent summaries using a fine-tuned T5 model
- **Email Categorization** — Semantically classifies emails to help users understand and organize their inbox
- **Reply Suggestions** — Generates context-aware reply drafts to reduce repetitive effort
- **On-Device Inference** — All AI processing happens locally; no email content leaves the user's environment
- **User Personalization** — Isolated LoRA fine-tuning adapts the model to individual communication styles without cross-user data leakage
- **Secure Authentication** — Google OAuth 2.0 + IMAP with ephemeral session handling

---

## Tech Stack

| Layer | Technologies |
|---|---|
| Frontend | React 19, TypeScript, Tailwind CSS, TanStack Router, Framer Motion |
| Backend | Python, FastAPI, Tortoise ORM, Uvicorn |
| AI / ML | T5 (fine-tuned), LoRA / PEFT, EmailSum-style thread modeling |
| Auth | Google OAuth 2.0, XOAUTH2, JWT |
| Infrastructure | Docker, SQLite, Redis, Celery |
| Dev Tools | Poetry, Aerich, Vite, ESLint |

---

## Architecture

```
┌─────────────────┐     OAuth2 / IMAP      ┌─────────────────┐
│   React Client  │ ──────────────────────▶ │  FastAPI Server │
│  (TypeScript)   │ ◀────────────────────── │  (Python)       │
└─────────────────┘     JSON responses      └────────┬────────┘
                                                     │
                                          ┌──────────▼──────────┐
                                          │  Model Inference     │
                                          │  T5 + LoRA (local)  │
                                          │  Summarize / Label  │
                                          │  / Suggest Reply    │
                                          └─────────────────────┘
```

- Email content is fetched via IMAP using OAuth access tokens (never stored persistently)
- Model inference runs in an isolated service — no external API calls
- LoRA adapters are user-specific and stored in isolated containers

---

## Evaluation

Summarization performance was evaluated against the **EmailSum benchmark**:

| Metric | EmailSum Baseline | Our Model |
|---|---|---|
| ROUGE-1 | 41–43 | Matches / Exceeds |
| ROUGE-2 | 34–36 | Matches / Exceeds |

Qualitative evaluation confirmed coherence, fidelity, and usefulness of generated summaries and reply suggestions.

---

## Getting Started

### Prerequisites
- Python 3.12+
- Node.js 18+
- Docker (optional but recommended)
- Google Cloud project with OAuth 2.0 credentials

### Backend Setup
```bash
cd server
cp .env.example .env   # add your Google OAuth credentials
poetry install
poetry run aerich upgrade
poetry run uvicorn app.main:app --reload
```

### Frontend Setup
```bash
cd client
npm install
npm run dev
```

### API Docs
Once the server is running, visit `http://localhost:8000/docs` for the interactive API documentation.

---

## Project Structure

```
smart-email-assistant/
├── client/               # React + TypeScript frontend
│   ├── src/
│   │   ├── components/   # UI components (Radix UI + shadcn)
│   │   └── routes/       # TanStack Router pages
├── server/               # FastAPI backend
│   ├── app/
│   │   ├── api/          # Route handlers
│   │   ├── services/     # IMAP, OAuth, auth logic
│   │   ├── models/       # Tortoise ORM models
│   │   └── repository/   # Data access layer
│   └── migrations/       # Aerich DB migrations
```

---

## Team

- Dhanalakshmi Kannur Munirathnam
- Kunal Maheshwari
- Anurag Jitendra Ambekar

Faculty Mentor: Prof. Liang Sisheng, Central Michigan University

---

## Privacy Guarantee

This system is designed around a core principle: **your email content never leaves your environment.** There is no telemetry, no cloud inference, and no persistent storage of email data beyond the active session. All model inference is local and all OAuth tokens are handled ephemerally.
