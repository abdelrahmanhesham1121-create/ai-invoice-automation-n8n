# 🧾 AI Invoice Processing & Deduplication Pipeline

An automated, intelligent end-to-end invoice extraction pipeline built with **n8n**, **Google Gemini Vision API**, and **Google Sheets**. The system parses unstructured invoice images, extracts structured key-value pairs via an AI Agent with a Structured Output Parser, verifies data uniqueness to prevent duplicate records, and persists clean data to Google Sheets.

---

## 🌟 Key Features
- **Automated Drive Ingestion:** Triggers automatically upon new invoice image upload in Google Drive.
- **Multimodal AI Extraction:** Leverages Google Gemini Chat Model + Structured Output Parser to extract clean JSON data without markdown formatting or raw schema noise.
- **Data Deduplication:** Queries Google Sheets using receipt/invoice numbers and dates before insertion to enforce data integrity.
- **Resilient Logic:** Utilizes `Always Output Data` on retrieval nodes and direct node execution references (`$('AI Agent').item...`) to ensure reliable data passing even across empty branch checks.

---

## 🏗 System Architecture & Workflow

1. **Trigger:** `Google Drive Trigger` listens for newly created files.
2. **Download:** `Download file` fetches the invoice binary image.
3. **AI Extraction:** `AI Agent` + `Structured Output Parser` + `Google Gemini Chat Model` processes the image and outputs clean structured parameters (`seller_name`, `invoice_date`, `invoice_number`, `tax_amount`, `total_amount`, `payment_method`).
4. **Duplication Lookup:** `Get row(s) in sheet` searches Google Sheets filtering by invoice number (`rec_number`) and invoice date.
5. **Logic Decision:** `If Node` evaluates whether a matching invoice record exists (`rec_number` is not empty).
6. **Persistence:** 
   - **False Branch (New Invoice):** Passes execution to `Append row in sheet`, inserting the structured fields cleanly into Google Sheets.
   - **True Branch (Duplicate Invoice):** Stops execution to prevent duplicate entries.

---

## 🛠 Tech Stack
- **Orchestration:** n8n
- **LLM / Vision Model:** Google Gemini Chat Model
- **Integrations:** Google Drive API, Google Sheets API
- **Data Exchange Format:** JSON / Structured Output Parser

---

## 📸 Workflow Architecture

![Workflow Canvas](Screenshot%202026-10-30%20084821.png)

### Sample Extracted JSON Output
```json
{
  "seller_name": "Berghotel Grosse Scheidegg",
  "invoice_date": "30.07.2007",
  "invoice_number": "4572",
  "tax_amount": "3.85",
  "total_amount": "54.50",
  "payment_method": "Bar"
}

---

## 🚀 How to Import & Run
1. Clone or download this repository.
2. Open your **n8n** instance.
3. Create a new workflow, click the top-right menu **(...)**, and select **Import from File**.
4. Upload `analyze image.json`.
5. Connect your credentials for **Google Drive**, **Google Gemini API**, and **Google Sheets**.
6. Set your Google Drive target folder and Google Sheets Spreadsheet ID, then activate the workflow!
