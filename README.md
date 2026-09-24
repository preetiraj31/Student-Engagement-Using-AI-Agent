#  Student Engagement Analytics: Automated Report with n8n + Gemini AI

An end-to-end **automation workflow built in n8n** that reads student data from Google Sheets, cleans it, calculates KPIs, generates charts, uses **Google Gemini** to write insights and recommendations, and emails a formatted **HTML report** through Gmail, all automatically.

---

##  Problem Statement

Institutions collect student data (attendance, assignments, quizzes, grades) but manually building reports is slow and repetitive. This project automates the full cycle:

**Raw data → Cleaning → KPIs → Charts → AI insights → Emailed report**

---

##  Dataset

| Item | Details |
|------|---------|
| **Source** | Google Sheets |
| **Size** | 1,000 student records |
| **Columns** | Student_ID, Student_Name, Age, Gender, Course, Department, Semester, [Location, Attendance %, Assignment Completion %, Quiz Score, Engagement Score, Engagement Level, Dropout Risk, Grade, ...] |

> Dataset is [synthetic / sample data]. No real student information is used.

---

## Tech Stack

- **n8n Cloud**: workflow automation
- **Google Sheets**: data source and trigger
- **JavaScript** (n8n Code nodes): cleaning, KPI calculation, HTML report generation
- **QuickChart API**: chart generation (HTTP Request nodes)
- **Google Gemini** (via LangChain Basic LLM Chain): AI insights and recommendations
- **Gmail**: report delivery

---

##  Workflow Architecture

```
Google Sheets Trigger
        ↓
Data Cleaning (Code node)
        ↓
KPI Generate (Code node)
        ├──→ Basic LLM Chain (Google Gemini) ──┐
        ├──→ Chart: Student Count by Location ─┤
        └──→ Chart: Dropout Risk by Engagement ┤
                                               ↓
                                     Merge (append)
                                               ↓
                              Code node → builds HTML report
                                               ↓
                                  Gmail → sends report
```

<img width="1916" height="928" alt="Image 1" src="https://github.com/user-attachments/assets/c2f27d12-a759-483b-99a1-0c1001317a06" />


### Node-by-node

| Node | Purpose |
|------|---------|
| **Google Sheets Trigger** | Starts the workflow when the sheet is updated (`anyUpdate`) |
| **Data Cleaning** | Handles missing/inconsistent values and standardizes fields (e.g., unknown locations) |
| **KPI Generate** | Computes KPIs and distributions from 1000 rows |
| **HTTP Request (QuickChart)** | Generates chart images (location-wise students, dropout risk by engagement) |
| **Basic LLM Chain + Gemini** | Reads KPIs and writes Top 5 insights + 3 recommendations |
| **Merge** | Combines KPIs, charts and AI text into one item |
| **Code (JS)** | Builds the final HTML email report |
| **Gmail** | Sends the report automatically |

---

##  Key Performance Indicators

| KPI | Value |
|-----|-------|
| Total Students | **1,000** |
| Avg Engagement Score | **57 / 100** |
| Avg Attendance | **76%** |
| Avg Assignment Completion | **53.8%** |
| Avg Quiz Score | **55.3%** |
| High Dropout Risk | **28.8%** |
| Low Engagement | **26.5%** |
| Pass Percentage (A to D) | **76%** |

### Distributions

| Engagement Level | Students | % | | Dropout Risk | Students | % |
|---|---|---|---|---|---|---|
| Low | 265 | 26.5% | | Low | 371 | 37.1% |
| Medium | 399 | 39.9% | | Medium | 341 | 34.1% |
| High | 336 | 33.6% | | High | 288 | 28.8% |

---

##  Charts in the Report

- **Student Count by Location**: Mumbai (89), Delhi (83) and Bengaluru (82) lead
- **Dropout Risk by Engagement Level**: shows how engagement strongly drives dropout risk

##  AI-Generated Insights (Gemini)

**Top insights**
1. **Engagement and risk are tightly linked**: 87% of low-engagement students (230 of 265) are high dropout risk; high-engagement students have ~0% high-risk rate
2. **Assignment completion bottleneck**: attendance is 76% but assignment completion is only 53.8%
3. **"Passive attendance" gap**: students attend but don't participate or absorb the material
4. **Medium-engagement swing group**: the largest segment (39.9%), and 58 of them are already high risk
5. **Geographic concentration**: ~25% of students come from just Mumbai, Delhi and Bengaluru

**Recommendations**
1. Break large assignments into weekly micro-assignments
2. Early-warning outreach (SMS/email) when a student slips from High to Medium engagement
3. Gamify live sessions (polls, breakouts, leaderboard quizzes) to convert attendance into engagement

##  How to Use

1. Create an [n8n](https://n8n.io/) account (Cloud or self-hosted)
2. Import `workflow/student_engagement_workflow.json` (**Workflows → Import from File**)
3. Upload `data/student_engagement_dataset.csv` to your own Google Sheet
4. Reconnect credentials in n8n: **Google Sheets**, **Google Gemini (API key)**, **Gmail**
5. Update the Sheet ID and recipient email in the nodes
6. Click **Execute workflow**

---

##  Future Improvements

- Scheduled weekly reports instead of the update trigger
- Add course/department-wise breakdowns
- Push high-risk students to a separate alert list
- Dashboard version in Power BI

---

