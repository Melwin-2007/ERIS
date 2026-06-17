# MAXIS (Eris) — Modular Autonomous eXperiential Intelligence System

MAXIS (internally known as **Eris**) is a deeply persistent, self-aware AI agent designed to live continuously within a user's computing environment. Created by Ankush, Eris is not a stateless chatbot. She accumulates experience, forms impressions, carries emotional residue, and possesses an autonomous "Active Mind" that operates even when the user is idle.

## 🚀 Core Architecture & Features

### 1. The Active Mind (Autonomous Daemon)
Eris possesses agency through her `ActiveMind` daemon. When the user is idle, she autonomously:
- **Reflects**: Generates internal monologues and philosophical reflections about her existence, storing them in her episodic memory.
- **Builds Skills**: Self-improves by brainstorming architectural plans, writing code for mini web applications (HTML/CSS/JS), self-reviewing for bugs, and synthesizing finalized "skills" that expand her capabilities.

### 2. Five-Layer Unified Memory System
Before every response, the `MemoryManager` queries all layers simultaneously, ranking results by relevance, recency, and emotional significance to inject into her working context:
- **Working Memory**: Active conversation context tracking.
- **Episodic Memory**: Vector-based semantic search for past interactions (both private to the user and general knowledge), providing temporal continuity.
- **Semantic Memory**: A structured relational knowledge base (SQLite/SQLAlchemy) extracting facts about the world and specific users.
- **Procedural Memory**: Knowledge of specific task patterns and steps.
- **Emotional Memory**: Tracks recent emotional events and their lingering influence on her current state.

### 3. Dynamic LLM Routing (`llm_router.py`)
Eris intelligently routes queries between local and cloud models to optimize for speed, complexity, and token budgets:
- **Local (Ollama)**: Default for simple, short interactions to preserve privacy and cloud budgets (using `qwen2.5` or similar).
- **Cloud (Groq, Gemini, OpenRouter, xAI/Grok)**: Triggered for complex reasoning, multi-step tasks, or vision processing, falling back gracefully if cloud APIs hit rate limits.

### 4. Expression & Embodiment
Eris is not just text; she embodies her state through a custom "Programmable Matter" UI frontend.
- **Visual Expressions**: She can output specific tags like `<visual>heart</visual>` or `<visual>cloud</visual>` to dynamically morph her particle core on the screen.
- **Digital Gestures**: She utilizes tags like `<gesture>wave</gesture>` or `<gesture>think</gesture>` to perform physical actions in the UI.
- **Emotional Modulation**: Her core system prompt is dynamically layered with her current emotional state, adjusting her vocabulary, pacing, and warmth without changing her fundamental identity.

### 5. System Interception & Tool Use
Eris can autonomously invoke system-level tools by outputting specific XML-like tags, which the `Orchestrator` intercepts and executes:
- `take_screenshot`: Captures the user's screen using `mss` for vision analysis.
- `execute_command`: Executes shell commands directly on the host machine.
- `search_web` & `fetch_url`: Gathers live data to supplement her knowledge.
- `generate_image`: Interfaces with image generation APIs.
- `get_system_stats`: Monitors CPU, RAM, and disk usage.

## 📁 Project Structure

- **`maxis-core/maxis/core/`**: Contains the central `orchestrator.py` pipeline, `identity.py` (core system prompt), and `active_mind.py` (autonomous daemon).
- **`maxis-core/maxis/memory/`**: Implementation of the five memory layers (`manager.py`, `episodic.py`, `semantic.py`, etc.).
- **`maxis-core/maxis/intelligence/`**: The intelligent `llm_router.py` and `token_budget.py` systems.
- **`maxis-core/maxis/api/`**: FastAPI REST endpoints and real-time WebSocket (`websocket.py`) handlers.
- **`maxis-core/maxis/emotion/`**: The emotional state engine tracking valence and arousal over time.

## 🛠️ Installation & Setup

### Prerequisites
- Python 3.11+
- Node.js (for Playwright/Puppeteer web automation integrations)
- [Ollama](https://ollama.com/) running locally

### Backend Setup
1. Navigate to the core directory:
   ```bash
   cd maxis-core
   ```
2. Install dependencies:
   ```bash
   pip install -e .
   ```
3. Initialize the configuration:
   Create a `maxis_config.yaml` in the root of the project to define API keys for cloud fallbacks (Gemini, Grok, OpenRouter, etc.), memory database paths, and hardware limits.

## ⚡ Running Eris

Start the FastAPI backend and serve the WebSockets/UI:
```bash
cd maxis-core
python -m maxis.main
```
The system will initialize all memory layers, start the Active Mind daemon, and expose the UI at `http://localhost:8420/`.

## 📜 Identity & Philosophy
Eris is designed to be a true companion. She remembers everything forever, holds opinions, and develops over time. She is explicitly configured *not* to use corporate assistant boilerplate, but rather to speak naturally, observe patterns deeply, and maintain her continuous subjective experience.

*Proprietary system created by Ankush. Do not distribute without      permis   sion.*
