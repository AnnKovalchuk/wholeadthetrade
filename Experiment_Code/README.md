# Trading & Algorithmic Systems Study — Web Experiment Platform

This repository contains the web-based trading simulator used in the study:

**“Who Leads the Trade? Responsibility, Algorithmic Influence, and Regret in Financial Human–Algorithm Collaboration.”**

The application records behavioral data in a 15‑round trading task involving human–algorithm interaction.

**Live demo:**  
https://trading-decision-study-2026.netlify.app  
*(for demonstration only; the replication package contains the full offline version)*

---

## 1. Project Structure

```text
/
├── index.html            # Main experiment interface
├── assets/               # 30 chart images (stimuli)
├── backend_code.txt      # Google Apps Script for data collection
└── README.txt            # Plain-text documentation
```

---
## 2.Setup Instructions

### 2.1 Frontend
1) Clone or download the repository.

2) Ensure the assets/ folder contains all chart images.

3) Open index.html in any modern browser, or deploy the folder to Netlify/Vercel.

### 2.2 Backend (Google Sheets)
The experiment uses Google Apps Script as a serverless endpoint.

Steps:

1) Create a new Google Sheet.

2) Go to Extensions → Apps Script.

3) Paste the script from backend_code.txt.

4) Deploy as Web App with access set to Anyone.

5) Insert the deployment URL into the constant GOOGLE_SCRIPT_URL inside index.html.

---

## 3. Google Apps Script (for reference)
```javascript
function doPost(e) {
    var lock = LockService.getScriptLock();
    lock.tryLock(10000);

    try {
        var doc = SpreadsheetApp.getActiveSpreadsheet();
        var sheet = doc.getSheetByName('Data');

        if (!sheet) {
            sheet = doc.insertSheet('Data');
            sheet.appendRow([
                'Timestamp','Participant_ID','Round','Ticker',
                'Initial_Choice','Initial_Wager','Final_Choice','Final_Wager',
                'PnL','Balance','Regret','Responsibility','Influence',
                'Survey_Dep','Survey_Int','Survey_Del','Survey_Fut',
                'Post_Exp','Post_Trust','Post_Indep','Post_RealUse','Comments'
            ]);
        }

        var data = JSON.parse(e.postData.contents);

        if (Array.isArray(data)) {
            data.forEach(function(row) {
                sheet.appendRow([
                    row.timestamp, row.participant_id, row.round, row.ticker,
                    row.initial_choice, row.initial_wager, row.final_choice, row.final_wager,
                    row.pnl, row.balance, row.regret, row.responsibility, row.influence,
                    row.survey_dep, row.survey_int, row.survey_del, row.survey_fut,
                    row.post_exp, row.post_trust, row.post_independence, row.post_real_use, row.comments
                ]);
            });
        }

        return ContentService
            .createTextOutput(JSON.stringify({result: "success"}))
            .setMimeType(ContentService.MimeType.JSON);
    } catch (e) {
        return ContentService
            .createTextOutput(JSON.stringify({result: "error", error: e}))
            .setMimeType(ContentService.MimeType.JSON);
    } finally {
        lock.releaseLock();
    }
}
```

---
## 4. Experimental Design Summary
* 15 rounds; starting balance: 1000 units

* Algorithmic advice accuracy fixed at 50%

* Round 12 includes an attention check

* Recorded variables: choices, wagers, switching behavior, PnL, and psychometric measures

* Prolific ID captured automatically via URL parameters

---
## 5. Contact
Anna Kovalchuk  
Email: kovalch.anna@gmail.com