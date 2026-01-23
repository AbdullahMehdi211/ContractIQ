# ContractIQ

## 📌 Overview

ContractIQ is a **production‑grade, contract‑aware claim validation system** built on Azure. It ingests healthcare contracts (PDFs), extracts and normalizes rate tables, indexes them using vector search, and validates pharmacy claims using **deterministic rules** with an optional **GPT explanation layer**.

The system follows **regulator‑grade architecture principles**:

* Deterministic logic for all financial decisions
* LLMs used strictly for explanation (never decision‑making)
* Full traceability from claim → contract → rule → outcome

---

## 🧭 High‑Level Flow Diagram

```mermaid
flowchart LR
    A[Contract PDFs] --> B[Ingestion & Extraction]
    B --> C[Normalize Rates]
    C --> D[Vector Index (Azure AI Search)]
    D --> E[Claim Validation Engine]
    E --> F[Deterministic Justification]
    F --> G[GPT Explanation (Optional)]
    G --> H[Final Claim Decision]
```

---

## 🏗️ System Architecture (Layered)

### **1. Ingestion & Extraction**

* Contracts stored in **Azure Blob Storage**
* **Azure AI Document Intelligence** extracts tables and layout
* Output: structured JSON artifacts

### **2. Normalization & Classification**

* Identifies non‑specialty and specialty rate tables
* Produces normalized, machine‑readable rate records

### **3. Search Indexing (Vector + Hybrid)**

* Normalized rates indexed into **Azure AI Search**
* Uses **Azure OpenAI embeddings** for semantic retrieval
* Supports keyword, vector, and hybrid search

### **4. Claim Validation Engine**

* Retrieves best‑matching contract rate
* Calculates expected reimbursement
* Computes variance and decision (OK / MISMATCH)

### **5. Justification & Explanation**

* **Deterministic justification** for audit and compliance
* Optional **GPT explanation** for human‑readable summaries

### **6. Semantic Kernel Agent**

* Orchestrates end‑to‑end flow using tools/plugins
* Produces a single structured response for APIs or UI

---

## 🧮 Claim Validation (Deterministic Core)

* Input: Claim details
* Output:

  * Status (OK / MISMATCH)
  * Expected vs Paid amount
  * Applied contract rule

Example deterministic justification:

```json
{
  "status": "MISMATCH",
  "reason": "Underpayment detected",
  "expected_amount": 1180.0,
  "paid_amount": 820.0,
  "difference": 360.0,
  "rule_applied": "MedicareContract.pdf | IVIG Products | 18% Brand WAC"
}
```

This layer is the **source of truth** and is mandatory for compliance.

---

## 🤖 GPT Explanation Layer

* Consumes deterministic outputs only
* Generates natural‑language explanations
* Does **not** influence calculations or decisions

Used for:

* Support teams
* Business users
* Faster understanding of outcomes

---

## ☁️ Azure Services Used

* **Azure Blob Storage** – Stores contract PDFs and intermediate artifacts
* **Azure AI Document Intelligence** – Extracts structured data from contracts
* **Azure OpenAI (Embeddings)** – Generates vectors for semantic search
* **Azure AI Search** – Vector, keyword, and hybrid rate retrieval
* **Azure OpenAI (GPT‑4o)** – Human‑readable explanation layer
* **Azure Key Vault (Recommended)** – Secure secret management for production

---

## 📂 Repository Structure

```
ContractIQ/
│
├── ingestion/            # Contract ingestion & extraction
├── normalization/        # Table parsing & rate normalization
├── search/               # Index creation, embedding, retrieval
├── claims/               # Claim models & calculators
├── plugins/              # Search & validation plugins
├── agent/                # Semantic Kernel orchestration
├── data/                 # Intermediate JSON artifacts
├── .env                  # Azure configuration
└── requirements.txt
```

---

## ✅ Key Design Principles

* LLMs never make financial decisions
* All outcomes are reproducible and auditable
* Hybrid (keyword + vector) search for accuracy
* Clear separation of concerns across layers
