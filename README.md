# MURF-AI-CODING-CHALLEGENGE-ECHOAI-
AI Voice Agent built with FastAPI, AssemblyAI, Gemini, and Murf. Enables real-time speech-to-speech chat, echo bot, TTS, and WebSocket streaming with session memory and clean modular design.
AI Voice Agent (FastAPI + AssemblyAI + Gemini + Murf)

A production-style prototype of a voice-first conversational AI agent built using FastAPI (Python) for the backend and HTML/JavaScript for the frontend.

The system enables end-to-end interaction:

User speaks into the browser.

Speech is recorded and uploaded.

AssemblyAI converts speech to text.

Gemini LLM (Google) processes the text, maintains conversational context, and generates a response.

Murf AI converts the response back into natural speech.

The browser automatically plays the reply.

This project was created as part of the 30-Day AI Voice Agent Challenge.

Core Features

One-tap voice chat: press record, speak, and receive an AI response in natural voice.

Multi-stage processing pipeline: STT (AssemblyAI) → LLM (Gemini) → TTS (Murf).

Persistent session memory: per-browser session_id maintains conversation history.

Real-time web search support via Tavily API (Gemini function calling).

WebSocket streaming for partial transcripts and low-latency playback.

Public demo safety: features are gated until users provide their own API keys (no shared secrets exposed).

Sidebar tools:

Text-to-Speech generator (enter text → Murf audio).

Echo Bot (record → transcribe → re-speak with Murf).

Keyboard shortcut: press m to toggle microphone on/off.

Architecture Flow

User presses "Start Speaking" in the browser.

The MediaRecorder API captures audio.

Audio is uploaded to the backend endpoint /agent/chat/{session_id}.

FastAPI receives and routes audio to the AssemblyAI API, which produces text.

The transcription is appended to session chat history and sent to Gemini API for reasoning.

Gemini generates an assistant reply.

The reply text is sent to Murf TTS API to synthesize realistic speech.

The backend returns an audio URL.

The browser plays back the generated audio and renders chat text.

Optionally, WebSocket /ws enables real-time streaming with partial transcripts and chunked TTS responses.

End-to-End Flow:
User Voice → FastAPI → AssemblyAI → Gemini → Murf → Browser Playback

Project Structure
app/
├── main.py                   # FastAPI entrypoint, defines routes
├── services/                 # Modular service layer
│   ├── stt_service.py        # AssemblyAI transcription helpers
│   ├── tts_service.py        # Murf TTS client wrapper
│   ├── llm_service.py        # Gemini client and prompt builder
│   ├── weather_service.py    # Example extension: OpenWeather API
│   ├── murf_ws_service.py    # Murf WebSocket streaming (chunked TTS)
│   ├── web_search_service.py # Tavily API wrapper for Gemini tool calls
│   └── streaming_transcriber.py # AssemblyAI real-time transcription
├── schemas/                  # Pydantic models for request/response validation
│   └── tts.py                # Data models (e.g., TextToSpeechRequest, ChatResponse)
├── templates/
│   └── index.html            # Main HTML frontend (chat + tools sidebar)
├── static/
│   ├── css/style.css         # Layout and styling
│   ├── JS/script.js          # Frontend logic: record, upload, playback
│   ├── images/               # Branding, screenshots, demo GIFs
│   │   ├── logo.png
│   │   ├── ui-screenshot.png
│   │   └── demo.gif
│   └── sounds/               # Microphone UI sound effects
│       ├── mic_start.mp3
│       └── mic_stop.mp3
├── uploads/                  # Temporary storage for uploaded audio
requirements.txt              # Python dependencies
.env                          # Environment file for API keys (excluded from git)
.gitignore                    # Ignore rules
README.md                     # Documentation

Environment Variables

Create a .env file in the project root with your API keys:

ASSEMBLYAI_API_KEY=your_assemblyai_key
GEMINI_API_KEY=your_gemini_key
MURF_API_KEY=your_murf_key
TAVILY_API_KEY=your_tavily_key
OPENWEATHER_API_KEY=your_openweather_key


Notes:

For public deployments, users must provide their own keys via the frontend Settings modal.

Server .env keys are optional fallback for development or private use.

Never commit .env to version control. Instead, commit .env.example with placeholders.

Where to get API keys:

AssemblyAI

Gemini (Google AI Studio)

Murf AI

Tavily

OpenWeather

Quick Start

Create and activate a virtual environment:

python -m venv .venv
.venv\Scripts\activate  # Windows
source .venv/bin/activate  # Mac/Linux


Install dependencies:

pip install -r requirements.txt


Copy .env.example → .env and fill in your API keys.

Run the server in development mode:

cd app
python main.py


Alternatively, run with uvicorn auto-reload:

cd app
uvicorn main:app --reload


Open in browser:

http://127.0.0.1:8000/

Key Endpoints
Method	Endpoint	Purpose
POST	/agent/chat/{session_id}	Complete voice chat pipeline: audio → STT → LLM → TTS
POST	/tts/echo	Echo Bot: record → Murf voice playback
POST	/tts/generate	Direct text to speech (Murf)
POST	/transcribe/file	Raw audio transcription (AssemblyAI)
WS	/ws	Streaming: partial transcripts + chunked TTS
GET	/debug/web_search	Tavily test endpoint (query param)
GET	/debug/llm_chat	LLM-only response (text query)
POST	/debug/llm_chat_text	LLM-only response (JSON body)
Technical Highlights

FastAPI backend with clear separation of routes, services, and schemas.

Resilient transcription pipeline with AssemblyAI.

Gemini LLM integration with retry logic and function calling (Tavily web search).

Murf AI TTS with error handling and WebSocket streaming support.

MediaRecorder API in frontend for efficient audio capture and upload.

Structured Pydantic responses for consistent API contracts.

In-memory session handling with CHAT_HISTORY dictionary.

Session Handling

Each browser session has a unique session_id (URL parameter).

Chat history is stored in a Python dictionary (CHAT_HISTORY) mapped to session IDs.

Suitable for prototyping; replace with Redis or database for production scalability.

History resets when server restarts.

Notes and Limitations

Demo mode requires users to provide their own API keys.

Not production-ready: no authentication, rate-limiting, or database persistence.

Latency depends on response times from AssemblyAI, Gemini, and Murf APIs.

In-memory session history is cleared on server restart.

API keys must remain private.

Contributing

Contributions are welcome. Suggestions, bug fixes, and feature ideas can be submitted as issues.

Steps to contribute:

Fork the repository.

Create a new feature branch (feature/my-feature).

Commit your changes with clear messages.

Open a pull request for review.

License

This project is licensed under the MIT License.
See LICENSE.txt
 for details.

Acknowledgements

AssemblyAI
 for speech-to-text.

Google Gemini
 for language reasoning.

Murf AI
 for natural TTS voices.

FastAPI
 for backend development.

Built as part of the 30-Day AI Voice Agent Challenge by Murf.ai.
