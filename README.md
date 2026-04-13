# TALASH - Talent Acquisition & Learning Automation for Smart Hiring

## Overview

TALASH is an intelligent CV processing and candidate evaluation system designed for academic and research institutions. It automates the extraction of structured information from PDF CVs and prepares data for LLM-based candidate scoring.

## System Architecture

The system follows a 6-layer architecture:

| Layer | Component | Description |
|-------|-----------|-------------|
| 1 | **Input** | Folder-based CV ingestion (PDF files) |
| 2 | **Preprocess** | PDF text extraction, cleaning, segmentation |
| 3 | **Extraction** | Personal, Education, Experience, Publications, Awards, Patents, References (Regex/NLP) |
| 4 | **LLM Analysis** | Prompt building → LLM API → Scored output (Milestone 2) |
| 5 | **Storage** | Excel (multi-sheet) + JSON + CSV |
| 6 | **UI/Output** | Streamlit Web App (Milestone 2) / Jupyter Notebook (current) |

### Design Decisions
- **Milestone 1:** Regex-based extraction (offline, no API required)
- **Milestone 2:** LLM integration (GPT / LLaMA)
- Modular design with dedicated extraction functions per section
- Multi-format storage for flexibility

---

## Project Structure


##  Features

### Current (Milestone 1)
-  PDF text extraction using `pypdf`
-  Multi-candidate PDF segmentation
-  Regex-based information extraction:
  - Personal information (name, DOB, salary, employment)
  - Education history (degree, year, CGPA)
  - Professional qualifications
  - Work experience (position, organization, duration)
  - Research publications (title, journal, impact factor, date)
  - Awards & scholarships
  - Patents
  - References (name, contact, email, phone)
-  Multi-format export (Excel, JSON, CSV)
-  LLM prompt structure demonstration

### Planned (Milestone 2)
-  LLM API integration (GPT-4 / LLaMA / Gemini)
-  Automated candidate scoring (0-10 for multiple criteria)
-  Ranking and "Hire/Shortlist/Not Hire" recommendations
-  Streamlit web interface
-  Enhanced extraction accuracy using LLMs

## Installation

### Prerequisites
- Python 3.10 or higher
- pip package manager

### Setup

1. **Clone or download** this repository

2. **Install required libraries:**
```bash
pip install pypdf pandas openpyxl
