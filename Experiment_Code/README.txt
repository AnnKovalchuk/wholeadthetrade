Trading & Algorithmic Systems Study — Web Experiment Platform
Supplementary Documentation

This repository contains the web-based trading simulator used in the study 
“Who Leads the Trade? Responsibility, Algorithmic Influence, and Regret in Financial Human–Algorithm Collaboration.”
The application records behavioral data in a 15‑round trading task involving human–algorithm interaction.

A live demonstration version of the experiment is available at:
https://trading-decision-study-2026.netlify.app
This link is provided for demonstration purposes only. The replication package contains the full offline version of the experiment.

1. Project Structure

The repository contains the following files:

    /index.html          Main experiment interface
    /assets/             Folder with 30 chart images (stimuli)
    /backend_code.txt    Google Apps Script used for data collection
    /README.txt          Documentation file (this document)

2. Setup Instructions

2.1 Frontend

- Clone or download the repository.
- Ensure that the folder “assets” contains all chart images.
- Open the file “index.html” in any modern web browser, or deploy the folder to a static hosting service (e.g., Netlify or Vercel).

2.2 Backend (Google Sheets)

The experiment uses Google Apps Script as a serverless endpoint for data collection.

Steps:

1. Create a new Google Sheet.
2. Open “Extensions → Apps Script”.
3. Paste the script below into the editor.
4. Deploy as “Web App” with access set to “Anyone”.
5. Insert the deployment URL into the constant GOOGLE_SCRIPT_URL inside “index.html”.

Google Apps Script:

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

3. Experimental Design Summary

- Total of 15 rounds; starting balance: 1000 units of virtual currency.
- Algorithmic advice accuracy fixed at 50%.
- Round 12 includes an attention check.
- Recorded variables include: choices, wagers, switching behavior, PnL, and psychometric measures (Regret, Responsibility, Influence, etc.).
- Prolific participant ID is captured automatically via URL parameters.

4. Contact Information

Anna Kovalchuk  
Email: kovalch.anna@gmail.com
