## PROJECT CONTEXT

- **Course**: CS417 — Large Language Models (Spring 2026)
- **Project**: TALASH (Talent Acquisition & Learning Automation for Smart Hiring)
- **Milestone**: 2 — Core Extraction, Analysis Pipeline, Intermediate Web App
- **Team**: BSDS-2K23
- **Instructor**: Prof. Dr. Muhammad Moazam Fraz

## SYSTEM ARCHITECTURE (6-Layer)

| Layer | Component | Description |
|-------|-----------|-------------|
| 1 | **Input** | Folder-based CV ingestion (PDF files) + Web upload |
| 2 | **Preprocess** | PDF text extraction, cleaning, candidate segmentation |
| 3 | **Extraction** | Personal, Education, Experience, Publications, Awards, Patents, References (LLM + Regex) |
| 4 | **LLM Analysis** | Prompt building → Gemini API → Structured JSON output |
| 5 | **Storage** | Excel (16 sheets) + JSON + CSV |
| 6 | **UI/Output** | Flask Web App + Real-time dashboard |

## TECHNOLOGY STACK

| Component | Technology |
|-----------|------------|
| Backend | Flask (Python web framework) |
| LLM | Google Gemini API (gemini-flash-lite-latest) |
| PDF Processing | PyPDF |
| Data Analysis | Pandas, NumPy |
| Frontend | HTML5, CSS3, Vanilla JavaScript |
| Visualization | Matplotlib |

## SCORING WEIGHTS

| Component | Weight |
|-----------|--------|
| Education | 35% |
| Professional Experience | 35% |
| Research Profile | 20% |
| Completeness | 10% |

**Formula**: `Final Score = (Edu * 0.35) + (Exp * 0.35) + (Research * 0.20) + (Completeness * 0.10)`

## EXTRACTION RESULTS

| Metric | Count |
|--------|-------|
| Pages processed | 251 |
| Candidate boundaries detected | 68 |
| Valid candidates | 43 |
| Education records | 167 |
| Experience records | 68 |
| Publications | 130 |
| Awards | 23 |
| Professional qualifications | 11 |
| Patents | 2 |
| References | 11 |

## OUTPUT FILES

| File | Contents |
|------|----------|
| outputs/talash_m2_output.xlsx | 16 sheets |
| outputs/talash_m2_output.json | Complete candidate data |
| outputs/talash_m2_personal.csv | Personal summary |
| outputs/draft_emails.txt | All email drafts |
| outputs/talash_dashboard.png | 6-panel visualization |

## API ENDPOINTS

| Endpoint | Method | Purpose |
|----------|--------|---------|
| /upload | POST | Upload PDFs, start async processing |
| /status/<job_id> | GET | Poll processing status |
| /results/<job_id> | GET | Get ranked candidates |
| /candidate/<job_id>/<id> | GET | Get full candidate profile |

## INSTALLATION

```bash
pip install flask pypdf pandas openpyxl matplotlib numpy google-generativeai
python app.py
