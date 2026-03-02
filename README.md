# 🛒 OpenClaw Retail Lead Automation
### `RETAIL_INDIA_OUTREACH_PIPELINE` — v2.0 (Production)

| Field | Value |
|---|---|
| **Pipeline ID** | `RETAIL_INDIA_OUTREACH_PIPELINE` |
| **Version** | 2.0.0 (Production) |
| **Submitted by** | [Your Full Name] |
| **Date** | [Submission Date] |
| **Tool** | OpenClaw (macOS) / n8n-compatible |

---

## 📌 What This Is

A production-grade, **9-node automated pipeline** that:

1. **Discovers** retail decision-makers in India (Director+) via the Apollo.io API with structured title/industry filtering and LinkedIn URL deduplication.
2. **Enriches** each lead with 3 parallel signal sources: LinkedIn post activity (Apify scraper), company news (NewsAPI with Bing fallback), and funding data (Crunchbase API).
3. **Synthesises** a grounded, cited personalisation hook using GPT-4o (temp=0.3, JSON mode). The LLM is prohibited from fabricating facts and must cite a verbatim excerpt from the raw signal.
4. **Generates** a sub-300-character LinkedIn connection message per lead using a second GPT-4o call (temp=0.5).
5. **Validates** every lead through a 4-check validation gate before it reaches the output file.
6. **Logs** all errors, retries, and resolutions to a structured error log with full auditability.

---

## 📁 Folder Structure

```
OpenClaw_RetailLeads_Automation_v2/
│
├── README.md                                ← You are here
│
├── 01_Lead_Data/
│   ├── retail_india_outreach_leads_v2.csv   ← Machine-readable output
│   └── retail_india_outreach_leads_v2.xlsx  ← 5-sheet enterprise workbook
│       ├── Sheet 1: Lead Output (enterprise schema, 24 columns)
│       ├── Sheet 2: Error Log (all retries + resolutions)
│       ├── Sheet 3: Run Metrics (full pipeline performance stats)
│       ├── Sheet 4: Scalability Design (Tier 0 → Tier 5)
│       └── Sheet 5: Retry & Error Handling Spec (14 error types)
│
├── 02_Workflow_Screenshots/
│   └── [10 × PNG screenshots — replace placeholders]
│
├── 03_Loom_Walkthrough/
│   └── loom_walkthrough_link.txt
│
├── 04_Workflow_Config/
│   ├── pipeline_config_v2.json              ← Full 9-node config
│   ├── llm_enrichment_prompt_v2.txt         ← N05 prompt (with rules + rubric)
│   └── llm_message_generation_prompt_v2.txt ← N06 prompt (with banned phrases)
│
├── 05_System_Docs/
│   └── system_documentation_v2.docx         ← Full system architecture doc
│       Sections: Architecture · Validation Layer · LLM Prompts ·
│       Error Handling · Scalability Plan · Risk Mitigation ·
│       Assumptions · Known Limitations · Interview Defence Notes
│
└── 06_Error_Logs/
    └── error_log_BATCH_001.csv              ← All errors with resolution status
        [+ run_metrics_BATCH_001.json at runtime]
```

---

## 🆕 V2.0 Improvements Over V1.0

### ✅ Validation Layer
- Added **4-check validation gate** (was: char count only)
- **Hallucination guard**: cross-checks hook vs raw signal
- **Generic phrase blocklist**: 14 banned phrases
- **Confidence threshold**: leads <0.70 → human review queue
- **Re-prompt budget**: N07 can re-send to N06 before routing

### 🔁 Error Handling
- All 9 nodes now have explicit error handling specs
- **Exponential backoff** on all external API calls
- **Fallback chains**: Apollo→Hunter, NewsAPI→Bing, session rotate
- **Dead-letter queue** for leads that exhaust all retries
- Error log captures: `error_id`, `node`, `type`, `message`, `retry_count`, `retry_strategy`, `resolved`, `timestamp`

### 🤖 LLM Prompts
- Enrichment prompt: added **7 explicit rules** + confidence rubric
- `signal_raw_excerpt` field added for audit trail / hallucination check
- `hallucination_risk` field added (`LOW` / `MEDIUM` / `HIGH`)
- Message prompt: **6 explicit rules**, 14 banned phrases listed
- Both prompts include **self-check checklist** for the model

### 📊 CSV Schema (24 columns, up from 17)
| Change | Field |
|---|---|
| New | `signal_raw_excerpt`, `message_word_count` |
| New | `char_limit_pass`, `generic_phrase_pass`, `hallucination_check` |
| New | `scrape_attempt_count` (audit trail for retries) |
| Renamed | `signal_confidence` → `hook_confidence_score` (float 0–1) |
| XLSX | 5 sheets including error log and run metrics |

### 📈 Scalability
- Defined **6 tiers** (Tier 0 → Tier 5, up to 500 leads/day)
- **Queue-based processing** design documented (Redis BRPOP)
- **Rate limiter spec**: Redis token bucket, 80/day/account
- **Monitoring spec**: DataDog dashboard at Tier 5

---

## 🔍 How to Review This Submission

### ⚡ Quickest Path

1. **Open** `system_documentation_v2.docx` (`05_System_Docs/`)
   → Full architecture, prompts, and interview defence notes

2. **Open** `retail_india_outreach_leads_v2.xlsx` (`01_Lead_Data/`)
   → Sheet 1: Lead data with hook confidence + validation flags
   → Sheet 2: Error log (realistic production errors + resolutions)
   → Sheet 3: Run metrics (full batch performance stats)

3. **Watch** the Loom video (`03_Loom_Walkthrough/loom_walkthrough_link.txt`)

### 🔬 Deep Dive

4. **Read** `llm_enrichment_prompt_v2.txt` and `llm_message_generation_prompt_v2.txt`
   → These are the most interview-relevant files in the submission

5. **Open** `pipeline_config_v2.json`
   → Full machine-readable workflow config, all 9 nodes, scalability tiers

---

## 📊 Key Metrics — BATCH_001

| Metric | Value |
|---|---|
| Leads qualified | **10 / 34** returned by Apollo (70.6% discard) |
| Leads approved | **10 / 10** (100% approval rate) |
| Avg hook confidence | **0.878** (range: 0.76 – 0.96) |
| Avg message length | **162 characters** (max: 300) |
| Generic violations | **0** |
| Hallucination flags | **0** |
| Errors encountered | **5** (all auto-resolved) |
| Est. LLM cost | **$0.31** for 10 leads |
| Total runtime | **52 min 14 sec** |

---

## 📬 Contact

[your.email@domain.com]
