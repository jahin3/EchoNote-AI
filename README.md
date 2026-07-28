# EchoNote AI 🎙️

**Record once. Learn smarter.**

Record or upload a lecture and get an AI-generated transcript, key points, a to-do checklist, 10 MCQs, and a 60-second summary — with copy and PDF export.

---

## Folder Structure

```
echonote-ai/
├── frontend/                      # React + Vite + Tailwind
│   ├── public/
│   │   └── logo.svg
│   ├── src/
│   │   ├── components/
│   │   │   ├── tabs/
│   │   │   │   ├── TranscriptTab.jsx
│   │   │   │   ├── KeyPointsTab.jsx
│   │   │   │   ├── TodoTab.jsx
│   │   │   │   ├── MCQTab.jsx
│   │   │   │   └── SummaryTab.jsx
│   │   │   ├── ErrorBanner.jsx
│   │   │   ├── Footer.jsx
│   │   │   ├── Hero.jsx
│   │   │   ├── Logo.jsx
│   │   │   ├── Navbar.jsx
│   │   │   ├── ProcessingStatus.jsx
│   │   │   ├── RecordButton.jsx
│   │   │   ├── ResultsTabs.jsx
│   │   │   ├── ThemeToggle.jsx
│   │   │   └── UploadCard.jsx
│   │   ├── hooks/
│   │   │   ├── useRecorder.js
│   │   │   └── useTheme.js
│   │   ├── utils/
│   │   │   ├── api.js
│   │   │   └── pdfExport.js
│   │   ├── App.jsx
│   │   ├── index.css
│   │   └── main.jsx
│   ├── .env.example
│   ├── .gitignore
│   ├── index.html
│   ├── package.json
│   ├── postcss.config.js
│   ├── tailwind.config.js
│   └── vite.config.js
├── backend/                        # Node.js + Express
│   ├── controllers/
│   │   ├── analyzeController.js
│   │   └── transcribeController.js
│   ├── middleware/
│   │   ├── errorHandler.js
│   │   └── upload.js
│   ├── routes/
│   │   ├── analyze.js
│   │   └── transcribe.js
│   ├── services/
│   │   ├── aiService.js            # Gemini or OpenAI GPT
│   │   └── whisperService.js       # OpenAI Whisper
│   ├── uploads/                    # temp audio storage (gitignored)
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   └── server.js
├── samples/                        # sample transcripts for testing
│   ├── lecture-1-biology-photosynthesis.txt
│   ├── lecture-2-computer-science-data-structures.txt
│   └── lecture-3-history-industrial-revolution.txt
└── README.md
```

All frontend and backend source files referenced above are included in full in this project (see each file for complete, working code — no pseudocode).

---

## How It Works

1. **Record or upload** — the browser records audio via `MediaRecorder`, or the user uploads an mp3/wav/m4a/webm/ogg file.
2. **`POST /api/transcribe`** — the audio is sent as `multipart/form-data` to the backend, which forwards it to **OpenAI Whisper** and returns `{ transcript }`.
3. **`POST /api/analyze`** — the transcript is sent as JSON to the backend, which prompts **Gemini** (or GPT, via `AI_PROVIDER`) for strict JSON containing `keyPoints`, `todos`, `mcqs`, and `summary`.
4. **Results** render in tabs — Transcript, Key Points, To-Do, MCQs, Summary — with **Copy** and **Download PDF** actions (PDF built client-side with `html2canvas` + `jsPDF`).

---

## Environment Variables

### `backend/.env` (copy from `backend/.env.example`)
```
PORT=5000
CLIENT_ORIGIN=http://localhost:5173

OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

AI_PROVIDER=gemini
GEMINI_API_KEY=AIzaSyxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
GEMINI_MODEL=gemini-2.0-flash
OPENAI_TEXT_MODEL=gpt-4o-mini

MAX_UPLOAD_MB=25
```

### `frontend/.env` (copy from `frontend/.env.example`)
```
VITE_API_URL=
```
Leave empty for local dev (Vite proxies `/api` to the backend — see `vite.config.js`). Set to your deployed backend URL in production.

---

## Run Locally

```bash
# 1. Clone / unzip the project, then from the project root:

# --- Backend ---
cd backend
cp .env.example .env      # then fill in OPENAI_API_KEY and GEMINI_API_KEY
npm install
npm run dev                # runs on http://localhost:5000

# --- Frontend (in a new terminal) ---
cd frontend
cp .env.example .env       # leave VITE_API_URL empty for local dev
npm install
npm run dev                # runs on http://localhost:5173
```

Open **http://localhost:5173**, allow microphone access, and try recording — or upload one of the files in `/samples` (convert the `.txt` to speech with any TTS tool, or just paste its content directly into the `/api/analyze` request with a tool like Postman to test the analysis step independently).

---

## Deployment

### Frontend → Vercel

1. Push the project to a GitHub repo.
2. In Vercel: **New Project** → import the repo → set **Root Directory** to `frontend`.
3. Framework preset: **Vite**. Build command: `npm run build`. Output directory: `dist`.
4. Add environment variable: `VITE_API_URL` = your Render backend URL (e.g. `https://echonote-api.onrender.com`).
5. Deploy. Vercel will give you a URL like `https://echonote-ai.vercel.app`.

### Backend → Render

1. In Render: **New** → **Web Service** → connect the same repo → set **Root Directory** to `backend`.
2. Runtime: **Node**. Build command: `npm install`. Start command: `npm start`.
3. Add environment variables from `backend/.env.example`: `OPENAI_API_KEY`, `AI_PROVIDER`, `GEMINI_API_KEY`, `GEMINI_MODEL`, `OPENAI_TEXT_MODEL`, `MAX_UPLOAD_MB`, and set `CLIENT_ORIGIN` to your Vercel frontend URL.
4. Deploy. Render will give you a URL like `https://echonote-api.onrender.com`.
5. Go back to Vercel and confirm `VITE_API_URL` points to this Render URL, then redeploy the frontend if needed.

**Note:** Render's free tier spins down on inactivity, so the first request after idle time may take 30–60 seconds while the service wakes up — the frontend's loading states handle this gracefully.

---

## Screenshot Mockup Description

Since no live screenshot exists yet, here's what the finished UI looks like when running:

**Landing view (light mode):** A soft slate-white background with two large, blurred purple and blue gradient blobs floating behind the hero section. A sticky glass navbar at the top shows the microphone logo mark (a purple-to-blue gradient rounded square with a white mic icon and sound-wave lines) next to the wordmark "EchoNote AI", with a sun/moon toggle pill on the right. Centered below, a small pill badge reads "🎙️ AI-Powered Lecture Notes", followed by a large bold headline "EchoNote AI" (with "AI" in a purple-blue gradient), the tagline "Record once. Learn smarter." in medium gray, and a one-line description underneath.

Below the hero, two glassmorphic cards sit side by side (stacked on mobile): on the left, a frosted-glass panel with a large circular gradient record button (purple-to-blue) with a white mic icon, a mono-font timer "Tap to record" beneath it; on the right, a dashed-border upload card with a cloud-upload icon in a soft gradient tile, "Drop an audio file here", supported formats text, and a "Browse files" ghost button.

**Processing view:** The two cards are replaced by a centered glass card containing a 4-step horizontal progress tracker (Uploading → Transcribing → Analyzing → Done), each step a numbered circle connected by a gradient progress line, with a small spinning loader and "Hang tight..." caption below.

**Results view:** A wide glass card appears with a row of pill-shaped tabs (Transcript, Key Points, To-Do, MCQs, Summary) — the active tab filled with the purple-blue gradient, inactive tabs transparent. To the right, "Copy" (ghost button) and "Download PDF" (solid gradient button) sit together. Below, the active tab's content renders: Key Points as numbered rounded-square badges with text; To-Do as checkbox rows in soft glass cards that strike through when checked; MCQs as question blocks with a 2-column grid of answer buttons that turn green (correct) or red (incorrect) once picked; Summary as a highlighted "60-second summary" pill above a readable paragraph.

**Dark mode:** The same layout on a deep indigo-black background (`#0b0a1a`), glass panels rendered as translucent white-on-dark with soft purple glows instead of light blurs, text switching to light slate tones, maintaining the same purple-blue gradient accents throughout for brand consistency.

The whole layout is mobile-first: on narrow Android screens, the record/upload cards stack vertically, the tab pills wrap onto a second line and remain horizontally scrollable, and all buttons expand to comfortable tap targets (min 44px height).
