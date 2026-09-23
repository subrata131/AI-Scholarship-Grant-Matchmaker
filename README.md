# 🎓 AI Scholarship & Grant Matchmaker

An AI-powered platform that matches students to relevant scholarships and grants based on their profile — instead of manually searching hundreds of listings, students get ranked, explained matches in seconds.

---

## 📌 Problem Statement

Finding the right scholarship is hard. Eligibility criteria are scattered across dozens of websites, written in dense legal language, and easy to miss. Students often:
- Miss scholarships they were actually eligible for
- Waste hours manually checking eligibility line-by-line
- Miss deadlines due to no centralized tracking

**Our Solution:** A tool where a student fills a short profile once, and an AI engine matches them against a curated database of scholarships — ranking results by fit and explaining *why* each one matches, in plain language.

---

## 🔄 Project Workflow

```
┌─────────────────┐     ┌──────────────────┐     ┌────────────────────┐
│  1. Data Setup   │ --> │ 2. Profile Input  │ --> │ 3. AI Matching      │
│  Curate & parse  │     │ Student fills a   │     │ Rank + explain      │
│  scholarship data│     │ short form        │     │ matches via LLM     │
└─────────────────┘     └──────────────────┘     └────────────────────┘
                                                            │
                                                            v
                                                  ┌────────────────────┐
                                                  │ 4. Results Display  │
                                                  │ Ranked cards +      │
                                                  │ deadlines           │
                                                  └────────────────────┘
```

### Step-by-step

1. **Data Setup (one-time, offline)**
   - Collect scholarships from public sources (National Scholarship Portal, Buddy4Study, DAAD, Fulbright, university sites, etc.)
   - Store in a structured JSON/CSV file: name, eligibility, category, deadline, link
   - Optionally use the LLM once to auto-extract structured fields (age limit, income cap, course, country) from raw eligibility text

2. **Profile Input (frontend)**
   - Student fills a simple form: education level, course/field, category, income range, location, etc.
   - Form data is sent to the backend as JSON

3. **AI Matching (backend)**
   - Backend filters scholarships by hard constraints (deadline not passed, category matches)
   - Remaining candidates + student profile are sent to the LLM (or an embeddings-based similarity search) for ranking
   - LLM returns a ranked list with a short, plain-language reason for each match

4. **Results Display (frontend)**
   - Show ranked scholarship cards: name, why it matches, deadline, apply link
   - Optional: "save for later" or deadline reminder toggle

---

## 🛠️ Tech Stack

| Layer          | Technology                              | Why                                                   |
|----------------|------------------------------------------|--------------------------------------------------------|
| Frontend       | **React** + Tailwind CSS                 | Fast to build, component-based, easy for team to split work |
| Backend        | **Python + FastAPI**                     | Simple, async-ready, auto-generates API docs (Swagger) |
| AI/LLM         | Claude API (or OpenAI API)               | Parses eligibility text, ranks + explains matches       |
| Data storage   | JSON/CSV file → SQLite (if time allows)  | No need for a heavy DB during a hackathon               |
| Deployment     | Vercel (frontend) + Render/Railway (backend) | Free tier, fast to spin up                         |
| Version control| Git + GitHub                             | Team collaboration                                      |

> **Note:** We are intentionally keeping the stack lightweight. No authentication system, no production database, no government API integrations — those are out of scope for this build.

---

## 📁 Project Structure

```
scholarship-matchmaker/
├── frontend/                # React app
│   ├── src/
│   │   ├── components/      # ProfileForm, ResultCard, Navbar, etc.
│   │   ├── pages/           # Home, Results
│   │   ├── api/             # functions calling backend endpoints
│   │   └── App.jsx
│   └── package.json
│
├── backend/                 # FastAPI app
│   ├── main.py               # entry point, defines routes
│   ├── models.py             # request/response schemas (Pydantic)
│   ├── matcher.py            # matching + LLM logic
│   ├── data/
│   │   └── scholarships.json # curated scholarship dataset
│   └── requirements.txt
│
├── docs/
│   └── data_schema.md         # field definitions for scholarships.json
│
└── README.md                  # you are here
```

---

## 🔌 API Endpoints (Backend)

| Method | Endpoint             | Description                                      |
|--------|-----------------------|---------------------------------------------------|
| GET    | `/scholarships`       | Returns all scholarships (for admin/debug view)    |
| POST   | `/match`              | Accepts student profile, returns ranked matches    |
| GET    | `/health`             | Simple health check                                |

**Example request to `/match`:**
```json
{
  "education_level": "undergraduate",
  "field": "computer science",
  "category": "general",
  "income_range": "below 8 lakh",
  "country": "India"
}
```

**Example response:**
```json
[
  {
    "name": "XYZ Merit Scholarship",
    "match_reason": "You meet the income and course criteria; strong fit for CS undergrads.",
    "deadline": "2026-11-15",
    "apply_link": "https://example.com/apply"
  }
]
```

---

## 📊 Scholarship Data Schema

Each entry in `scholarships.json` should follow this structure:

| Field           | Type    | Example                          |
|------------------|---------|-----------------------------------|
| `name`           | string  | "National Merit Scholarship"      |
| `category`       | string  | "general" / "ST" / "SC" / "minority" |
| `education_level`| string  | "undergraduate" / "postgraduate"  |
| `field`          | string  | "engineering" / "any"             |
| `income_limit`   | number  | 800000                            |
| `country`        | string  | "India" / "abroad"                |
| `deadline`       | date    | "2026-11-15"                      |
| `apply_link`     | string  | URL                                |
| `raw_eligibility`| string  | Original eligibility text (for LLM parsing reference) |

See `docs/data_schema.md` for the full field list and validation rules.

---

## 🚀 Getting Started

### Prerequisites
- Node.js (v18+) and npm
- Python 3.10+
- An API key for Claude or OpenAI (ask team lead for the shared dev key, or use your own)

### Backend Setup
```bash
cd backend
python -m venv venv
source venv/bin/activate      # on Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload
```
Backend runs at `http://localhost:8000` — visit `/docs` for interactive Swagger API docs.

### Frontend Setup
```bash
cd frontend
npm install
npm run dev
```
Frontend runs at `http://localhost:5173` (or whatever Vite/CRA assigns).

### Environment Variables
Create a `.env` file in `backend/`:
```
LLM_API_KEY=your_api_key_here
LLM_PROVIDER=anthropic   # or openai
```

---

## 🤝 How to Contribute

1. **Pick a task** from the team's task board (or ask in the group chat if unsure where to start)
2. **Create a branch** from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes** — keep commits small and focused
4. **Test locally** before pushing (run both frontend and backend, check nothing breaks)
5. **Push and open a Pull Request** with a short description of what you changed
6. **Tag a teammate** to review before merging into `main`

### Suggested task split (for a 6-person team)
| Role                     | Responsibility                                      |
|---------------------------|------------------------------------------------------|
| Frontend (2 people)       | Profile form, results page, styling                  |
| Backend (2 people)        | FastAPI routes, matching logic, data handling         |
| AI/Data (1 person)        | Curate scholarship data, write/tune LLM prompts       |
| Integration/Demo (1 person)| Connect frontend↔backend, prepare demo script, deployment |

### Code Style
- Frontend: keep components small, one component per file
- Backend: use Pydantic models for all request/response bodies
- Commit messages: short and descriptive (e.g., `add profile form validation`, not `fix stuff`)

---

## 🎯 Demo Checklist (before presenting)
- [ ] At least 50–100 scholarships loaded in the dataset
- [ ] Profile form works end-to-end and returns real matches
- [ ] Match explanations are readable and specific (not generic)
- [ ] Deployed link works (or a reliable local demo is ready as backup)
- [ ] 2-minute pitch: problem → solution → live demo → impact

---

## 📄 License
For hackathon/educational use. Add a license here if the project continues post-hackathon.
