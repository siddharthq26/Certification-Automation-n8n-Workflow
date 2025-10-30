
# 🎓 Certification Automation using n8n

## 📘 Project Overview

The **Certification Automation System** is an automated workflow built using **n8n**, designed to streamline the process of generating and distributing digital certificates for events, competitions, or workshops.

This automation integrates with **Google Sheets**, **Google Drive**, and **Gmail** to:

* Read participant data from a Google Sheet,
* Generate personalized certificates in PDF format,
* Upload them to Google Drive, and
* Send them automatically to participants via email.

The workflow significantly reduces manual effort, ensures accuracy, and provides an efficient, scalable solution for institutions conducting frequent events.

---

## ⚙️ Objectives

* To automate the process of generating event participation and achievement certificates.
* To eliminate manual certificate creation and mailing tasks.
* To integrate Google Workspace tools with n8n for seamless workflow execution.
* To provide an easily scalable system that can handle multiple events and participants.

---

## 🧩 Workflow Description

### Trigger

**Google Sheets Trigger**
The workflow starts automatically when a new entry is added to a linked Google Sheet.
Each row represents a participant’s details such as Name, Email, Event Name, Position, and Date.

### Process Flow

1. **Data Extraction:** Participant information is retrieved from the Google Sheet.
2. **Conditional Logic:** Based on the “Position” field, the type of certificate (Gold, Silver, Bronze, or Participation) is selected.
3. **Certificate Generation:** HTML templates are used to generate certificates dynamically.
4. **PDF Conversion:** The HTML certificate is converted to PDF format using a custom HTML-to-PDF module.
5. **File Upload:** The generated PDF is uploaded automatically to a specified folder in Google Drive.
6. **Email Dispatch:** The certificate is attached and sent to the participant’s registered email via Gmail.

---

## 🧮 Workflow Components

| Node Name                 | Function        | Description                                                            |
| ------------------------- | --------------- | ---------------------------------------------------------------------- |
| **Google Sheets Trigger** | Trigger         | Detects new participant entries in the Google Sheet.                   |
| **Edit Fields**           | Set             | Maps participant data (Name, Email, Position, Event Name, etc.).       |
| **Switch**                | Logic           | Determines which certificate template to use based on Position.        |
| **HTML Nodes (1–4)**      | HTML Generation | Creates the certificate layout and content for each achievement level. |
| **Merge**                 | Merge           | Combines outputs from all certificate types into one stream.           |
| **HTML to PDF**           | Conversion      | Converts the generated HTML into a downloadable PDF file.              |
| **Google Drive Upload**   | Storage         | Uploads the generated PDF certificate to Google Drive.                 |
| **Gmail Node**            | Email           | Sends the certificate as an attachment to the participant.             |

---

## 🧠 Logic Overview

| Position   | Certificate Type  | Description                           |
| ---------- | ----------------- | ------------------------------------- |
| 1          | 🥇 Gold           | Certificate of Excellence – 1st Place |
| 2          | 🥈 Silver         | Certificate of Excellence – 2nd Place |
| 3          | 🥉 Bronze         | Certificate of Excellence – 3rd Place |
| 0 / Others | 🎖️ Participation | Certificate of Participation          |

---

## 🧰 Tools and Technologies

* **n8n (Docker Deployment)** – For workflow automation
* **Google Sheets API** – For real-time data input
* **Google Drive API** – For storing generated certificates
* **Gmail API** – For sending emails with attachments
* **CustomJS HTML-to-PDF Converter** – For generating certificates in PDF format

---

## 🪶 Prerequisites

* A running **n8n instance** (preferably via Docker)
* Configured **Google APIs credentials** for:

  * Google Sheets Trigger
  * Google Drive
  * Gmail
* **CustomJS API credentials** (for PDF generation)
* Access to the designated Google Sheet:
  [Google Sheet Link](https://docs.google.com/spreadsheets/d/1YYJQt2Ri4EidwOmGjFFts_iSw6F2mVr0TIhC4eemp-c/edit?usp=drivesdk)

---

## 🚀 Steps to Execute

1. **Import Workflow**

   * Open n8n → *Workflows → Import from File* → Select `Certification automation.json`.

2. **Configure Credentials**

   * Add credentials for Google Sheets, Gmail, Google Drive, and CustomJS.

3. **Set Google Sheet Data**

   * The sheet must contain columns:

     ```
     Name | Email | Position | Event Name | Date | Certificate Type | Certificate URL
     ```

4. **Run the Workflow**

   * Execute manually or let the Google Sheets Trigger start it automatically.

5. **Check Outputs**

   * Certificates are uploaded to Google Drive.
   * Emails are sent to all participants with their respective certificates.

---

## 📂 Folder Structure

```
/Certification-Automation/
│
├── Certification automation.json   # n8n workflow file
├── README.md                       # Documentation file
└── /sample-certificates/           # Optional folder for sample outputs
```

---

## 📬 Output

Each participant receives:

* A **personalized certificate (PDF)** with their name, event name, position, and date.
* An **email from the organizer** with the certificate attached.

---

## 📑 Conclusion

The Certification Automation workflow provides a practical implementation of **no-code automation** using n8n.
It demonstrates the integration of multiple APIs and logical workflow design to solve a real-world administrative problem efficiently.
This system enhances productivity, reduces human error, and ensures timely distribution of certificates.

---

## 👩‍💻 Developed By

**Name:** Siddhartha Brahmanapally
**Department:** Computer Science and Engineering
**Institution:** VNR Vignana Jyothi Institute of Engineering and Technology, Hyderabad
**Project Title:** *Certification Automation using n8n*
**Version:** 1.0


