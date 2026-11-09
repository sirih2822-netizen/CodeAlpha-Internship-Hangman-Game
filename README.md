# CodeAlpha Hangman Game — Phase 2

Full-stack Hangman word-guessing application developed for the CodeAlpha Python Programming Internship (Task 1). Built with a React + Tailwind CSS frontend and a Python Flask REST API backend.

---

## 1. Prerequisites

Ensure you have the following installed on **Windows 11**:
- **Python 3.10+**: Download from [python.org](https://www.python.org/downloads/) (Make sure to check *"Add Python to PATH"* during installation).
- **Node.js (v18+ or v20+) & npm**: Download from [nodejs.org](https://nodejs.org/).

Verify in Command Prompt or PowerShell:
```bat
python --version
node --version
npm --version
```

---

## 2. Environment Architecture & Execution Note

- **Cloud Preview Limitation**: The Google AI Studio preview environment runs in a remote, isolated cloud container. It cannot reach a Flask server running locally on your computer's `127.0.0.1:5000`. In the preview, the frontend correctly displays a user-friendly connection notification rather than using fake frontend logic.
- **Local Testing**: To test the complete application, both the frontend and the Flask backend must be run together on the same accessible environment (your local Windows 11 machine).
- **No Secret Keys**: This application does not require any paid external services, API keys, databases, or authentication credentials.
- **Clean Distribution**: The downloaded ZIP file does not include virtual environments (`venv/`), `node_modules/`, or bytecode caches. These are quickly generated locally using the standard commands below.

---

## 3. Project Architecture

```
/
├── backend/
│   ├── app.py              # Flask REST API entry point & CORS configuration
│   ├── game_service.py     # Authoritative Hangman game state & business logic
│   └── requirements.txt    # Python dependencies (Flask, Flask-CORS)
├── src/
│   ├── components/         # Reusable React UI components
│   │   ├── Header.tsx      # Product title and internship badge
│   │   ├── Introduction.tsx# Guess the Word heading and subtitle
│   │   ├── HangmanVisual.tsx # 6-stage inline SVG gallows drawing
│   │   ├── WordDisplay.tsx # Letter slots reflecting revealed_word
│   │   ├── Keyboard.tsx    # 26-letter alphabetical on-screen keyboard
│   │   ├── GameResult.tsx  # Compact win / loss status display
│   │   └── Footer.tsx      # Internship attribution footer
│   ├── services/
│   │   └── api.ts          # Frontend API client communicating with Flask
│   ├── types.ts            # TypeScript interfaces
│   ├── constants.ts        # Predefined words and keyboard rows
│   ├── App.tsx             # Main game state orchestration & error handling
│   └── main.tsx            # React application entry point
├── tests/
│   ├── test_game_service.py# Deterministic automated unit tests for game logic
│   └── test_app.py         # Flask HTTP endpoint integration tests
├── .env.example            # Environment variables template
├── .gitignore              # Git ignore rules for Python & Node
├── package.json            # Frontend scripts and dependencies
└── README.md               # Documentation & setup guide
```

---

## 4. Windows 11 Setup & Run Instructions

Open two terminal windows (Command Prompt or PowerShell) — one for the backend and one for the frontend.

### Terminal 1: Backend Setup (Python Flask)

1. Open a terminal in the extracted project folder:
   ```bat
   cd C:\path\to\extracted-folder
   ```

2. Create and activate a Python virtual environment:
   - **Command Prompt (`cmd`)**:
     ```bat
     python -m venv venv
     venv\Scripts\activate.bat
     ```
   - **PowerShell**:
     ```powershell
     python -m venv venv
     .\venv\Scripts\Activate.ps1
     ```

3. Install backend dependencies:
   ```bat
   pip install -r backend/requirements.txt
   ```

4. Start the Flask server:
   - **Command Prompt (`cmd`)**:
     ```bat
     set PYTHONPATH=.
     python backend/app.py
     ```
   - **PowerShell**:
     ```powershell
     $env:PYTHONPATH="."
     python backend/app.py
     ```

   The backend will start at: **`http://127.0.0.1:5000`**

---

### Terminal 2: Frontend Setup (React + Vite)

1. Open a second terminal in the extracted project folder:
   ```bat
   cd C:\path\to\extracted-folder
   ```

2. Install frontend dependencies:
   ```bat
   npm install
   ```

3. Configure the Backend URL (Optional):
   Create a `.env` file in the root directory (copied from `.env.example`):
   ```env
   VITE_API_BASE_URL=http://127.0.0.1:5000
   ```
   *(If omitted, the frontend automatically defaults to `http://127.0.0.1:5000`).*

4. Start the frontend development server:
   ```bat
   npm run dev
   ```

   The frontend will start at: **`http://localhost:3000`** (or Vite assigned port). Open this URL in your web browser to play.

---

## 5. Running Automated Tests

To run the full automated Python backend test suite on Windows 11:

- **Command Prompt (`cmd`)**:
  ```bat
  venv\Scripts\activate.bat
  set PYTHONPATH=.
  python -m unittest discover tests -v
  ```

- **PowerShell**:
  ```powershell
  .\venv\Scripts\Activate.ps1
  $env:PYTHONPATH="."
  python -m unittest discover tests -v
  ```

### Test Coverage Details
The test suite deterministically verifies:
1. New game creation and valid UUID generation.
2. Correct letter reveals every occurrence in the word (e.g., double `Z` in `PUZZLE`).
3. Incorrect guess increments the mistake counter by exactly one.
4. Repeated correct guess does not alter game state.
5. Repeated incorrect guess does not increment mistake count.
6. Win detection when all letters in the word are revealed.
7. Loss detection immediately after six incorrect guesses.
8. Rejection of guesses submitted after game completion (HTTP 400).
9. Validation and rejection of invalid/malformed inputs (HTTP 400).
10. HTTP 404 responses for non-existent game IDs.
11. Secret word remains hidden during active gameplay responses.
12. Terminal game responses reveal the secret word.
13. Flask HTTP route integration tests (`/api/health`, `/api/game/new`, `/api/game/<id>`, `/api/game/<id>/guess`).

---

## 6. REST API Endpoints

| Method | Endpoint | Description | Status Codes |
|---|---|---|---|
| `GET` | `/api/health` | Health check endpoint | `200` |
| `POST` | `/api/game/new` | Creates a new game session with a randomly chosen word (`PYTHON`, `PLANET`, `ROCKET`, `GARDEN`, `PUZZLE`). | `201` |
| `GET` | `/api/game/<game_id>` | Returns current game state (`revealed_word`, attempts, status). | `200`, `404` |
| `POST` | `/api/game/<game_id>/guess` | Submits a single letter (`{"letter": "A"}`). Returns updated state with `last_guess_result`. | `200`, `400`, `404` |

---

## 7. In-Memory Session Limitations

This internship project utilizes an **in-memory session store** (`GameService.sessions`) to manage active games:
- All active game sessions reside in server RAM.
- **When the Flask server restarts or shuts down, active game sessions reset.**
- In production, this can be swapped with a persistent store (e.g., Redis, SQLite, PostgreSQL) without modifying the frontend or API contract.
