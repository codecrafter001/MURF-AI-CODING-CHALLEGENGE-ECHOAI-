# 🎤 AI Voice Agent

## ✨ Core Features
- One-tap voice chat (microphone → AI answer with auto-played voice)  
- Multi-stage pipeline: STT → LLM → TTS  
- Persistent in-memory session history (per browser session id)  
- Real-time web search via Tavily (Gemini Function Calling)  
- WebSocket live transcripts + streamed TTS playback  
- Public demo safety: features are gated until users provide their own API keys (no shared secrets)  
- Sidebar Tools: Text to Speech generator (choose text → Murf voice output), Echo Bot (record → transcribe → re-speak your words in another voice)  
- Keyboard shortcut: press **m** to toggle mic on/off  

## 🧠 Architecture Flow
- User presses **Start Speaking** → Browser records audio (MediaRecorder)  
- Audio uploaded to `/agent/chat/{session_id}`  
- AssemblyAI transcribes bytes → text  
- Chat history compiled into a Gemini prompt  
- Gemini generates assistant reply  
- Murf API converts reply text to speech (default voice: en-US-charles)  
- Frontend auto-plays the returned audio & renders chat bubbles  
- User Voice → FastAPI → AssemblyAI → Gemini → Murf → Browser Playback  
- Supports **real-time streaming** via WebSocket (`/ws`) with partial transcripts and chunked TTS audio  

## 🗂️ Project Structure
- **app/** → Main application folder  
  - `main.py` → FastAPI entrypoint (routes import service layer)  
  - **services/** → Domain/service logic  
    - `stt_service.py` → AssemblyAI transcription helpers  
    - `tts_service.py` → Murf.ai TTS client wrapper  
    - `llm_service.py` → Gemini client + prompt builder + function calling  
    - `weather_service.py`  
    - `murf_ws_service.py` → Murf WebSocket streaming (chunked TTS)  
    - `web_search_service.py` → Tavily search wrapper  
    - `streaming_transcriber.py` → AssemblyAI streaming transcription  
  - **schemas/** → Pydantic models (`tts.py`: TextToSpeechRequest, ChatResponse, etc.)  
  - **templates/** → UI shell (`index.html`)  
  - **static/** → Frontend assets (CSS, JS, images, sounds)  
  - **uploads/** → Temp upload storage (optional)  
- `requirements.txt` → Dependencies  
- `.env` → Local API keys (not committed)  
- `.gitignore` → Ignore rules  
- `README.md` → Documentation  

## 🔑 Environment Variables
Add a `.env` file in the project root (optional fallback for dev):  
Create a .env file in the project root (optional; for local fallback):

ASSEMBLYAI_API_KEY=your_assemblyai_key
GEMINI_API_KEY=your_gemini_key
MURF_API_KEY=your_murf_key
TAVILY_API_KEY=your_tavily_key
OPENWEATHER_API_KEY=your_openweather_key
Notes:

For public deployments, users must enter their own keys via the in‑app Settings modal. Server keys are optional fallback for private/dev.
Do not commit .env. Share .env.example with placeholders instead.
Where to get API keys
AssemblyAI: https://www.assemblyai.com/app/account
Gemini (Google AI Studio): https://aistudio.google.com/app/apikey
Murf AI: https://murf.ai/api (Account settings → API key)
Tavily: https://app.tavily.com/ (Dashboard → API Keys)
OpenWeather: https://home.openweathermap.org/api_keys
Tip: copy .env.example to .env and fill your values. Never commit .env.

##🚀 Quick Start
# 1. Create & activate a virtual environment
python -m venv .venv
.venv\Scripts\activate  # Windows

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your .env file (see above)

# 4. Run the server (simple dev mode)
cd app && python main.py

# 5. Open in browser
http://127.0.0.1:8000/

# (Alt) Use uvicorn directly for auto-reload (optional)
# cd app && uvicorn main:app --reload

## 📡 Key Endpoints

| Method | Endpoint                  | Purpose                                              |
|--------|---------------------------|------------------------------------------------------|
| POST   | `/agent/chat/{session_id}` | Voice chat: audio → transcription → LLM → TTS        |
| POST   | `/tts/echo`                | Echo tool (repeat what you said with Murf)           |
| POST   | `/generate_audio`          | Direct text → speech (Murf)                          |
| POST   | `/transcribe/file`         | Raw transcription (AssemblyAI)                       |
| WS     | `/ws`                      | Streaming: partial transcripts + chunked TTS         |
| GET    | `/debug/web_search`        | Tavily test: `?query=your+question`                  |
| GET    | `/debug/llm_chat`          | LLM (no audio): `?q=hello`                          |
| POST   | `/debug/llm_chat_text`     | LLM (no audio): `{ "text": "hello" }`               |

## 🧪 Tech Highlights
- FastAPI backend with **service + schema layering** (clean separation)  
- AssemblyAI transcription (resilient + fallback path)  
- Google Gemini (`gemini-1.5-flash`) via reusable client & retry logic  
- Gemini Function Calling with a **web_search tool** backed by Tavily  
- Murf AI TTS wrapped in a lightweight client (consistent error handling)  
- Murf WebSocket streaming with safe chunking to speak full answers  
- MediaRecorder + multipart upload for low-latency voice capture  
- Autoplay + replay logic with audio unlock and retry  
- Structured **Pydantic responses** for clearer API contracts  
- Per-session key overrides wired from UI → backend (no keys echoed back)  

## 🔄 Session Handling
- Browser **session id** appended to URL (query param)  
- History stored in **in-memory dict (`CHAT_HISTORY`)**  
- Suitable for prototyping; swap with **Redis/DB** for production scaling  

## 🛡️ Notes / Limits
- Public mode gates features until users provide keys (Settings auto-opens on first use)  
- Not production-hardened (**no auth, rate limiting, or persistence yet**)  
- API keys must remain secret (`.env` not committed)  
- In-memory history resets on server restart (swap with **Redis/DB** later)  
- Gemini key must be loaded before first request (**lazy reconfigure added**)  

## 🤝 Contributing
- Prototype phase — open issues with ideas (latency, UI/UX, voice packs, multilingual support)  
- PRs welcome after discussion  

## 📄 License
- Licensed under **MIT License** (see LICENSE.txt)  

## 🙌 Acknowledgements
- **AssemblyAI** → Speech-to-text  
- **Google Gemini** → Language understanding  
- **Murf AI** → High-quality synthetic voices  
- **FastAPI** → Rapid backend framework  
- Built as part of **30-Day AI Voice Agent Challenge by Murf.ai**  

