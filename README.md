
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
## High-Level Architecture

1. **Trigger**
    - The workflow is initiated either:
        - Manually (via a manual trigger)
        - Automatically, by detecting a change or schedule in a Google Sheets document

2. **Data Extraction**
    - Reads participant data from a specified Google Sheet.
    - Captures fields such as Name, Email, Position, Event Name, Date, Certificate Type, and Certificate URL.

3. **Field Preparation**
    - Sets and formats the fields needed for the certificate.

4. **Decision: Certificate Type**
    - Utilizes a Switch node to determine the participant’s Position (e.g., 1st, 2nd, 3rd, or Participation).
    - Routes to the correct certificate template accordingly.

5. **Certificate Generation**
    - Populates the selected HTML certificate template with participant details.
    - Converts the filled HTML into a PDF certificate.

6. **Google Drive Upload**
    - Uploads the generated PDF certificate to a designated Google Drive folder for storage and access.

7. **Email Distribution Loop**
    - Iterates over all participants.
    - Sends the respective PDF certificate to each participant via Gmail.

---

## Architecture Diagram


<img width="1803" height="2190" alt="image" src="https://github.com/user-attachments/assets/66491527-0387-4e6a-9cb2-07f0f36279df" />

---

## 🧩 Building the Workflow in n8n (Low-Level)

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

<img width="485" height="557" alt="image" src="https://github.com/user-attachments/assets/988242be-1f32-4de2-a799-eac7cd347bb7" />


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

(just drag the fields that you want to extract)

<img width="491" height="557" alt="image" src="https://github.com/user-attachments/assets/5995d506-8cbe-4701-b031-7c0d6bf0d0d6" />

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

(just like this add 4 routing rules)

<img width="473" height="537" alt="image" src="https://github.com/user-attachments/assets/abb483a1-57e0-4f43-80a2-e3156ab6d5c2" />

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

(Here we are giveing the template of the certificate in html)

<img width="613" height="535" alt="image" src="https://github.com/user-attachments/assets/ce52de13-36a1-4791-8087-66261e20294d" />

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
<img width="202" height="407" alt="image" src="https://github.com/user-attachments/assets/6ef14b07-052f-41bf-9a90-31b67408c38b" />

---

### 🧩 Step 7: Convert HTML to PDF

* **Node Type:** `HTML to PDF`
(you need to download this node from community node )

<img width="1086" height="146" alt="image" src="https://github.com/user-attachments/assets/70ac2295-6790-49ab-8184-842e4a458362" />

* **Purpose:** Convert the generated HTML into a downloadable PDF certificate.
* **Credentials:** Connect your **CustomJS API** (used for PDF generation).
* **Input Field:**

  * `htmlInput = {{$json["html"]}}`

**Connection:**
`Merge → HTML to PDF`
<img width="1304" height="527" alt="image" src="https://github.com/user-attachments/assets/bb41f094-7ba7-4b70-b02a-3b921585eb95" />


---

### 🧩 Step 8: Loop Over Each Certificate(Optional)

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
(in the file name box drag the name from the edit fields which you can see in the left side, by this while saving in the drive it will we saved with the correct name)
<img width="1307" height="549" alt="image" src="https://github.com/user-attachments/assets/3d9987a4-750f-4c9e-bc52-7b1e7e75a303" />


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

(in the 'to' box drag the email you are seeing on the left side in the edit fields and place it in th box and in option -> add option-> attachment -> add attachment)
<img width="910" height="565" alt="image" src="https://github.com/user-attachments/assets/7f06f76e-0860-4dcb-9c45-10c3dc9a2092" />


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
