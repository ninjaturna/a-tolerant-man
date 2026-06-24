# A Tolerant Man — Waiting List: Connect a Google Sheet

This sets up a clean Google Sheet **you own** to collect waiting-list signups, and
repoints the live site's form to it.

## What's already done
- New sheet created (owned by tam@goodideasonly.design), in your "Bill Evans" Drive folder:
  **A Tolerant Man — Waiting List (Master)**
  https://docs.google.com/spreadsheets/d/1VAkJ90pc2z96dCMIVamCSIrGmLsJcIXGioSb7Udwc-k/edit
  Columns: `Timestamp | Name | Email | Source | Raw`
- Seeded with **36 contacts** pulled and de-duplicated from your existing reader-comment
  sheets (Source = "imported - reader comments").
- (You can delete the earlier empty placeholder sheet titled "A Tolerant Man — Waiting List" —
  this Master sheet is the one to keep.)

## Why this is needed
The live site's "Add Me to the List" form currently POSTs to an Apps Script web app
(`AKfycbyEojhBboxKI2-3oHCwK_QQlZOWOVvaLsV-JRmJZ2DGEzn0q2wgDZUHYWSfNvj0AwUI`) that is
**not owned by your goodideasonly.design account** — which is why you can't see those
signups. The steps below give you a destination you fully control.

---

## Step 1 — Add the script to your new sheet
1. Open the **A Tolerant Man — Waiting List (Master)** sheet (link above).
2. Menu: **Extensions → Apps Script**.
3. Delete whatever is in `Code.gs`, paste the code below, and **Save** (disk icon).

```javascript
// Bound to: A Tolerant Man — Waiting List (Master)
const SHEET_ID = '1VAkJ90pc2z96dCMIVamCSIrGmLsJcIXGioSb7Udwc-k';

function doPost(e) {
  try {
    var data = {};
    if (e && e.postData && e.postData.contents) {
      try { data = JSON.parse(e.postData.contents); } catch (err) { data = {}; }
    }
    if (e && e.parameter) {
      for (var k in e.parameter) { if (data[k] === undefined) data[k] = e.parameter[k]; }
    }
    var name  = data.name  || data.Name  || data.fullName || '';
    var email = data.email || data.Email || data.emailAddress || '';

    var sheet = SpreadsheetApp.openById(SHEET_ID).getSheets()[0];
    sheet.appendRow([new Date(), name, email, 'website', JSON.stringify(data)]);

    return ContentService
      .createTextOutput(JSON.stringify({ ok: true }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ ok: false, error: String(err) }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet() {
  return ContentService.createTextOutput('A Tolerant Man waiting list endpoint is live.');
}
```

> The `Raw` column stores the full submission as a safety net, so no signup is ever lost
> even if the form sends slightly different field names.

## Step 2 — Deploy it as a web app
1. Top right: **Deploy → New deployment**.
2. Click the gear next to "Select type" → choose **Web app**.
3. Set:
   - **Description:** Waiting list
   - **Execute as:** Me
   - **Who has access:** Anyone
4. **Deploy** → it will ask you to **authorize** — approve it (this is your own account
   granting your own script access; safe to accept).
5. Copy the **Web app URL** (it ends in `/exec`). This is your NEW endpoint.

## Step 3 — Point the website's form at the new endpoint (Claude Code)

DEPLOYED ENDPOINT (verified working 6/24/2026):
`https://script.google.com/macros/s/AKfycbwCR6Py-ijSLVy5s-QMbLqpJcWfNA7I0V7_O94vxrK-KZAjao40hcA1hWAnYeEQdrYLWA/exec`

Run this in the site's repo with Claude Code:

> In this repo, the waiting-list form (`#waiting-list-form`) submits to the Google Apps
> Script URL
> `https://script.google.com/macros/s/AKfycbyEojhBboxKI2-3oHCwK_QQlZOWOVvaLsV-JRmJZ2DGEzn0q2wgDZUHYWSfNvj0AwUI/exec`.
> Replace that URL with:
> `https://script.google.com/macros/s/AKfycbwCR6Py-ijSLVy5s-QMbLqpJcWfNA7I0V7_O94vxrK-KZAjao40hcA1hWAnYeEQdrYLWA/exec`
> Keep the existing request format (POST, `mode: 'no-cors'`, same body). Do not change the
> reflection form. Then commit and redeploy to Vercel.

## Step 4 — Test
Submit a signup on the live site, then check the sheet — a new row should appear within a
few seconds.

---

## Note on the reflection form
The "Leave a reflection" form (`#reflection-form`) posts to the **same old endpoint** and
is still going to that other account. If you also want reflections in a sheet you own, that's
a second, similar setup (different columns) — say the word and it can be added.

## Note on existing/recent signups
Signups collected by the current site since launch are in the other account's sheet, not
yours. To recover them you'd need to sign into the Google account that deployed the original
script. The older comment/reflection data (2025) is already in your Drive:
- A Tolerant Man -Comments from readers and friends
- A Tolerant Man (Tally export, has an "email updates?" column)
- A Tolerant Man -Framer Comments
