# TALASH

**Talent Acquisition and Learning Automation for Smart Hiring**

TALASH is a CV processing pipeline that ingests raw PDF resumes exported from the NUST HR portal and extracts structured, relational data using an LLM backend. The output is a multi-sheet Excel workbook, per-candidate JSON cache files, and optional missing-information email drafts. A Flask web interface is included for upload and review.

---

## Features

- PDF text extraction using pdfplumber, with graceful handling of two-column NUST HR form layouts
- LLM-powered field extraction via Google Gemini (structured JSON output)
- Relational schema across nine tables, all linked by a candidate ID
- Multi-sheet Excel export via openpyxl
- Per-candidate JSON cache to avoid redundant API calls in downstream modules
- Auto-generated missing-info emails for incomplete applications
- Resume/checkpoint support for interrupted runs
- Flask web app with SQLite backend for candidate review and scoring
- Salary normalization for PKR values entered in thousands

---

## Project Structure

```
talash/
├── app.py                   Flask web application
├── pipeline.py              CLI entry point
├── config.py                Table schemas, prompts, API config
├── database.py              SQLite helpers
├── requirements.txt
├── split_pdf.py             Splits bulk PDF uploads into individual files
├── rescore_fast.py          Re-runs scoring without re-parsing PDFs
├── .env.example             Copy to .env and add your API key
│
├── core/
│   ├── pdf_parser.py        pdfplumber-based text extraction
│   ├── llm_extractor.py     Gemini API calls and JSON parsing
│   └── excel_writer.py      Multi-sheet openpyxl workbook writer
│
├── data/
│   ├── journal_database.py  Known journal metadata
│   ├── conference_rankings.py  Known conference tiers
│   └── university_rankings.py  Known university tiers
│
├── utils/
│   └── normalizer.py        Dates, salaries, IDs, degree levels
│
├── cvs/                     Input PDFs go here
├── output/                  Generated files appear here
│   ├── TALASH_Candidates.xlsx
│   ├── raw_json/            Per-candidate JSON cache
│   └── .checkpoint.json     Resume checkpoint
│
└── templates/
    └── index.html           Web UI
```

---

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env
# Open .env and add your GOOGLE_API_KEY or ANTHROPIC_API_KEY
```

---

## Running the CLI Pipeline

```bash
# Basic run
python pipeline.py --input ./cvs --output ./output

# Generate missing-info email drafts per candidate
python pipeline.py --input ./cvs --output ./output --emails

# Resume an interrupted run (skips already-processed PDFs)
python pipeline.py --input ./cvs --output ./output --resume

# Reduce request rate if hitting API limits
python pipeline.py --input ./cvs --output ./output --delay 3.0
```

## Running the Web App

```bash
python app.py
```

The app will be available at `http://localhost:5000`. It loads candidates from SQLite on startup, and falls back to migrating the JSON cache if the database is empty.

---

## Output

```
output/
├── TALASH_Candidates.xlsx     Main deliverable (one sheet per table)
├── raw_json/
│   ├── candidate_001.json     Full extracted JSON per candidate
│   └── ...
├── missing_email_cand_006.txt   Missing-info emails (if --emails flag used)
└── .checkpoint.json             Resume checkpoint
```

The Excel workbook contains one sheet per relational table:

- Data Dictionary
- Personal Info
- Education
- Experience
- Publications
- Skills
- Patents
- Books
- Awards
- References

---

## Relational Schema

All tables are linked via `candidate_id` (foreign key referencing `personal_info.candidate_id`).

| Table | Primary Key | Key Fields |
|---|---|---|
| personal_info | candidate_id | name, salary, employment_status, apply_date |
| education | record_id | degree_level, institution, GPA, year |
| experience | record_id | job_title, org, start/end, duration_months |
| publications | record_id | pub_type, venue, impact_factor_claimed, authorship_role |
| skills | record_id | skill_name, category, evidence_level |
| patents | record_id | patent_number, country, is_lead_inventor |
| books | record_id | publisher, isbn, authorship_role |
| awards | record_id | award_type, issuing_body |
| references | record_id | ref_name, designation, contact |

`evidence_level` in the skills table is populated by the M2 Skill Alignment module, not this pipeline.

---

## Loading the JSON Cache in Downstream Modules

```python
import json
from pathlib import Path

candidates = []
for f in Path("output/raw_json").glob("*.json"):
    candidates.append(json.loads(f.read_text()))

for c in candidates:
    name = c["personal_info"]["full_name"]
    for pub in c["publications"]:
        print(name, pub["title"], pub["impact_factor_claimed"])
```

---

## Design Notes

**Why LLM extraction instead of regex?**
The NUST HR form uses a two-column layout that breaks standard PDF text extraction order. An LLM understands semantic context regardless of the spatial arrangement of text on the page.

**Why cached JSON?**
Each LLM call has an API cost. The `raw_json/` directory lets M2 and M3 modules reload structured data without re-running the extraction step.

**Why `impact_factor_claimed` and not `impact_factor_verified`?**
Per the project specification, verification against external databases (WoS, Scopus) is the responsibility of Module 2 (Research Profile Analysis). This pipeline extracts only what the candidate declared on their CV.

**Salary normalization**
Some candidates enter salary in shorthand (for example, "200" to mean PKR 200,000). The normalizer detects values below 5,000 and multiplies by 1,000.

---

## Requirements

See `requirements.txt`. Key dependencies:

- Flask, Flask-CORS, Flask-SQLAlchemy
- google-generativeai
- pdfplumber
- openpyxl, pandas
- python-dotenv, tqdm, requests

Python 3.12 or later recommended.
