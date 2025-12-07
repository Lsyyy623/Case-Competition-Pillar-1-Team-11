# Case-Competition-Pillar-1-Team-11
### Team members: Chenxi Wu, Jiayi Zheng, Shiyun Lu
# Project Overview
This repository contains the code used to build a scalable data engine for cross-university sustainability research.  

Our workflow includes:
1. Faculty and publication data collection  
2. Multi-source abstract retrieval  
3. SDG and sustainability keyword matching  

For each business school, we provide:
- One script to collect **faculty / staff information**
- One script to collect **publication lists**
- Shared scripts to **retrieve abstracts** and **match them with sustainability-related keywords / SDGs**

The goal is to:

- Aggregate faculty and publication data across multiple schools
- Retrieve reliable abstracts from multiple sources
- Match publications to sustainability themes using keyword and embedding methods


# Repository Structure

A typical layout (your actual filenames may differ, but the roles are the same):

root/
├── schools/
│   ├── SchoolA_faculty.py
│   ├── SchoolA_publications.py
│   ├── SchoolB_faculty.py
│   ├── SchoolB_publications.py
│   └── ...
│
├── abstract_and_sdg/
│   ├── abstract_engine.py
│   ├── sdg_keyword_matching.py
│   └── sdg_keyword_dictionary.json
│
└── README.md


# Scripts and Their Purpose

## 📄 1. Faculty scraper (per school)
**Purpose**  
Scrape faculty or staff information from a specific business school website.

**Output**  
A file containing:
- Faculty name  
- Title / position  
- Department or area  
- Profile URL

---

## 📘 2. Publication scraper (per school)
**Purpose**  
Collect each faculty member’s publication list from a specific business school website. (e.g., 2015–present).

**Output**  
A file containing:
- Paper title/citation
- Author
- DOI (if available)  
- Year
- Venue (if available)  
- Source link  

---

### 🔍 3. Abstract Engine (`abstract_engine.py`)

A multi-source abstract retrieval pipeline designed for incomplete metadata and large-scale crawling.

**Key Features**
- **DOI-first strategy**: If a DOI is found, performs direct lookup on Semantic Scholar for the most accurate abstract.
- **Three-tier fallback**:
  1) Crossref (bibliographic search + JATS abstract)  
  2) Semantic Scholar (title/author search)  
  3) Google Scholar (SerpAPI snippet as weak abstract)
- **Fuzzy title matching** to select the best candidate across all sources.
- **Resumable runs** using SHA-1 row keys and periodic autosave.
- **Robust rate-limit handling** (Retry-After, backoff, network retries).

**Outputs**
- `*_with_abstracts.xlsx`  
- `*_need_manual.xlsx`  
- Autosave backups

---

### 🌱 4. SDG Processing Engine (`sdg_processing.py`)

Assigns each publication a **Primary SDG** and extracts SDG-related keywords using TF-IDF and semantic similarity.

**Key Features**
- **TF-IDF SDG matching**:  
  - Builds SDG keyword documents  
  - Computes cosine similarity between SDGs and abstracts  
  - Outputs `Primary_SDG` and `Primary_SDG_Score`
- **Relevance threshold** to flag meaningful SDG alignment.
- **Semantic keyword matching** using Sentence-BERT:  
  - Matches SDG keyword phrases to abstract sentences  
  - Includes fallback to ensure at least one relevant SDG keyword when appropriate
- **Clean, interpretable outputs** for dashboards or downstream analytics.

**Outputs**
- `Primary_SDG`  
- `Primary_SDG_Score`  
- `Primary_SDG_Relevant`  
- `Primary_SDG_Semantic_Matched_Keywords`  


