Perfect — so you don’t just want setup instructions; you want a **step-by-step “Workflow Building Guide”** explaining **how you actually created and connected each node in n8n**, so that someone reading the README can **recreate it themselves from scratch**, not just import it.

Below is your **final README.md** version for project submission — now expanded with a detailed **“Building the Workflow in n8n (Step-by-Step)”** section that explains every node connection, data flow, and logic setup clearly.

---

# 🎓 Certification Automation using n8n

## 🧾 Abstract

The **Certification Automation System** is an automated workflow built using **n8n**, designed to simplify the process of generating and sending digital certificates. It integrates **Google Sheets**, **Google Drive**, and **Gmail** to automatically create personalized certificates in PDF format and send them to participants via email.
This system eliminates repetitive manual work, reduces human error, and provides an efficient, scalable solution for institutions conducting frequent events.

---

## 📘 Project Overview

The project automates the certificate creation and distribution process for events or competitions. When a new record is added to a Google Sheet (with participant details), the workflow automatically generates a PDF certificate, uploads it to Google Drive, and emails it to the participant.

This automation is built in **n8n**, a visual, no-code/low-code workflow automation tool that supports API-based integration between services.

---

## ⚙️ Objectives

* To automate certificate creation and emailing using n8n.
* To integrate Google Workspace APIs (Sheets, Drive, Gmail) efficiently.
* To reduce administrative time and human errors.
* To develop a scalable workflow reusable for multiple events.

---

## 🧱 System Architecture

**Input:** Participant details from Google Sheets
**Process Engine:** n8n (logic, decision-making, automation)
**Output:** PDF certificate stored in Google Drive and sent via Gmail

---

## 🧩 Building the Workflow in n8n (Step-by-Step)

This section explains **how the workflow was built manually in n8n**, including the purpose of each node and how they connect.

---

### 🪄 Step 1: Create a New Workflow

1. Open **n8n** and click **“New Workflow”**.
2. Name it **Certification Automation**.

---

### 🧩 Step 2: Add a Google Sheets Trigger

* **Node Type:** `Google Sheets Trigger`
* **Purpose:** Automatically runs when a new row is added to a Google Sheet.
* **Configuration:**

  * Connect your **Google Sheets account**.
  * Select the Sheet that contains participant data.
  * Sheet Columns Example:

    ```
    Name | Email | Position | Event Name | Date | Certificate Type | Certificate URL
    ```
* **Output:** Participant details are passed to the next node.

---

### 🧩 Step 3: Set the Extracted Data

* **Node Type:** `Set`
* **Name:** `Edit Fields`
* **Purpose:** Map all relevant fields to variables for use later.
* **Fields to Add:**

  * `Name = {{$json["Name"]}}`
  * `Email = {{$json["Email"]}}`
  * `Position = {{$json["Position"]}}`
  * `Event Name = {{$json["Event Name"]}}`
  * `Date = {{$json["Date"]}}`
  * `Certificate Type = {{$json["Certificate Type"]}}`
  * `Certificate URL = {{$json["Certificate URL"]}}`

**Connection:**
Connect `Google Sheets Trigger → Edit Fields`.

---

### 🧩 Step 4: Add a Switch Node

* **Node Type:** `Switch`
* **Purpose:** Decide which certificate design to use depending on the participant’s position.
* **Configuration:**

  * Condition 1 → `Position = 1` (First Place)
  * Condition 2 → `Position = 2` (Second Place)
  * Condition 3 → `Position = 3` (Third Place)
  * Condition 4 → `Position = 0 or any other` (Participation)

**Connection:**
Connect `Edit Fields → Switch`.

---

### 🧩 Step 5: Add HTML Template Nodes

You’ll need **four HTML nodes**, one for each certificate type.

| Node Name | Certificate Type | Description                               |
| --------- | ---------------- | ----------------------------------------- |
| **HTML**  | 1st Place        | Gold-themed “Certificate of Excellence”   |
| **HTML1** | 2nd Place        | Silver-themed “Certificate of Excellence” |
| **HTML3** | 3rd Place        | Bronze-themed “Certificate of Excellence” |
| **HTML2** | Participation    | Standard “Certificate of Participation”   |

Each node contains custom HTML and CSS with placeholders such as:

```html
<div class="name">{{$json["Name"]}}</div>
<h2>{{$json["Event Name"]}}</h2>
<p>Date: {{$json["Date"]}}</p>
```

**Connection:**
Connect each output of the `Switch` node to its corresponding HTML node.

---

### 🧩 Step 6: Merge All HTML Outputs

* **Node Type:** `Merge`
* **Purpose:** Combine all HTML outputs into a single data stream before conversion.
* **Connection:**

  ```
  HTML → Merge
  HTML1 → Merge
  HTML2 → Merge
  HTML3 → Merge
  ```

---

### 🧩 Step 7: Convert HTML to PDF

* **Node Type:** `HTML to PDF`
* **Purpose:** Convert the generated HTML into a downloadable PDF certificate.
* **Credentials:** Connect your **CustomJS API** (used for PDF generation).
* **Input Field:**

  * `htmlInput = {{$json["html"]}}`

**Connection:**
`Merge → HTML to PDF`

---

### 🧩 Step 8: Loop Over Each Certificate

* **Node Type:** `Split In Batches`
* **Purpose:** Process each participant’s certificate individually.
* **Configuration:**

  * Batch Size: `1`

**Connection:**
`HTML to PDF → Split In Batches`

---

### 🧩 Step 9: Upload Certificate to Google Drive

* **Node Type:** `Google Drive`
* **Purpose:** Save the generated PDF to a specific Google Drive folder.
* **Configuration:**

  * Connect **Google Drive OAuth2** credentials.
  * Choose the folder where certificates should be stored.
  * File Name: `{{$json["Name"]}}`
  * Data Field: `data`

**Connection:**
`Split In Batches → Google Drive`

---

### 🧩 Step 10: Send Certificate via Gmail

* **Node Type:** `Gmail`
* **Purpose:** Email the certificate to the participant.
* **Configuration:**

  * Connect **Gmail OAuth2** credentials.
  * **To:** `{{$json["Email"]}}`
  * **Subject:** `VNR VJIET Certificate of Participation`
  * **Message:**

    ```
    Hey {{$json["Name"]}},
    Thank you for your presence in the event. Here is your certificate.
    ```
  * Attach the generated PDF from previous node.

**Connection:**
`Split In Batches → Gmail`

---

### 🧩 Step 11: Optional - Add “NoOp” Node (Loop)

* **Node Type:** `NoOp`
* **Purpose:** Completes the loop iteration by reconnecting to “Split In Batches”.
* **Connection:**
  `Gmail → NoOp → Split In Batches`

---

### ✅ Final Flow Summary

```
Google Sheets Trigger
   ↓
Edit Fields (Set)
   ↓
Switch (Condition based on Position)
   ↓
HTML Templates (1–4)
   ↓
Merge
   ↓
HTML to PDF
   ↓
Split In Batches
   ↓ ↓
 Gmail Node    Google Drive Upload
```

---

## 🧰 Required Integrations

To replicate the workflow, ensure you have connected:

* ✅ Google Sheets API
* ✅ Google Drive API
* ✅ Gmail API
* ✅ CustomJS API (for PDF conversion)

All credentials must be created under **Credentials → New → OAuth2** in n8n.

---

## 📤 Output

Each participant receives:

* A **custom PDF certificate** (generated automatically).
* An **email** containing the certificate as an attachment.
* A **copy** stored in the organizer’s Google Drive folder.

---

## 📂 Folder Structure

```
/Certification-Automation/
│
├── Certification automation.json   # Exported workflow
├── README.md                       # Documentation file
└── /sample-certificates/           # Optional: sample outputs
```

---

## 📑 Conclusion

This project demonstrates the power of **workflow automation** using n8n.
It integrates multiple Google APIs to build a smart system capable of performing repetitive tasks without supervision.
By automating certificate generation, institutions can save time, minimize errors, and improve operational efficiency.

---

## 🔮 Future Scope

* Integration with event registration forms (Google Forms / Typeform).
* Adding QR code-based certificate verification.
* Auto-generating event-specific reports.
* Providing an admin dashboard for tracking sent certificates.

---

## 👩‍💻 Developed By

**Name:** Siddhartha Brahmanapally
**Department:** Computer Science and Engineering
**Institution:** VNR Vignana Jyothi Institute of Engineering and Technology, Hyderabad
**Project Title:** *Certification Automation using n8n*
**Version:** 1.0

---

Would you like me to include **workflow diagram illustrations (like flowchart images or Mermaid diagrams)** to visually represent node connections? That would make your README even more professional for submission and GitHub.
