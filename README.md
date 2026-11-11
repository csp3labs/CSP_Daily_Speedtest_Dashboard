# CSP3 Ookla Daily Dashboard

One web page for the CSP3 Ookla speed test programme. It shows the daily test samples from the shared Google Sheet, and it lets testers import their own results from a `results.csv` file or from Ookla result screenshots. Access is by login, and each person only sees what their level allows.

Last updated: 11 Nov 2026 (Import tab added). See `CHANGELOG.md` for what changed.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole dashboard, including the Import tab. Published on GitHub Pages. |
| `Code.gs` | The login, data and import service. It lives in the **CSP3 Ookla Auth** Apps Script project, deployed as a web app. |
| `README.md` | This file. |
| `SPEC.md` | How the page and the service work, in detail. |
| `CHANGELOG.md` | History of changes. |
| `IMPORT_TAB_DEPLOY.md` | Step-by-step deployment and checks for the Import tab release. |
| `AUTH_SETUP.md`, `AUTH_SPEC.md` | First-time setup of the login service and its specification (from the login release). |

The old standalone Importer page and its Apps Script project still exist and still work. They are not part of these files. See "The old Importer" below.

## Updating

**The service (`Code.gs`)**
1. Open the **CSP3 Ookla Auth** Apps Script project and replace `Code.gs` with the new file.
2. **Deploy, Manage deployments**, edit the existing web app, set **Version** to **New version**, then **Deploy**. Keep the same deployment so the URL does not change.

Saving the code does not change the live service. Only a new version of the deployment does.

**The page (`index.html`)**
1. Replace `index.html` in the GitHub repository.
2. Wait about a minute, then reload the page.

**Testing an update without touching the live version**
1. In Apps Script choose **Deploy, New deployment** (web app, Execute as Me, access Anyone). This gives a second URL; the live deployment stays on the old code.
2. In a copy of `index.html`, set `AUTH_API_URL` to the second URL and save it as `test.html`.
3. Open `test.html` and log in with a normal account. Both deployments share the same users and the same sheet, so test imports write real rows.

**Rolling back:** in **Manage deployments**, edit the deployment and choose the previous version number.

## Login and access levels

Users register with their name (picked from the list), an email and a password, confirm the email with a code, and wait for an admin to approve. At login they enter email or name, password and the 6-digit code from an authenticator app, which is set up once by scanning a QR at the first login. A session lasts until 22:59H Manila time.

| | Admin | Team Lead | Engineer |
|---|---|---|---|
| Samples | All | Samples whose Team Assignment is their team | Their own |
| Rank tables, Tester viewer, tester counts | Yes | Their team | No |
| Daily target | Yes | Their team | No |
| WhatsApp broadcast | Yes | Yes | No |
| Export | Everything | Their team | Their own |
| Import results | For any name in the List tab | For themselves or a name on their team | For themselves only |
| Load CSV, Open Google Sheet, Admin Tools | Yes | No | No |

Every user can send a **Request level change**. Admins approve registrations and level changes on the **Admin Tools** page (batch approve with Select all), and can disable users, set level and team, unlock accounts, reset an authenticator, or sign someone out everywhere. The Log tab of the sheet lists every login, admin action and import.

The data is filtered on the server before it is sent, so each person's browser only ever receives what they may see. Phone numbers are never sent to the dashboard.

## Using the dashboard

**Header**
- **Import results** (purple button) opens the Import tab. It becomes **Back to dashboard** while the tab is open.
- **Auto-sync: ON/OFF** refreshes the data every 60 seconds.
- **Refresh** reloads the data now.
- **Export** saves the selected data (current filters) or all the data you may see, as CSV or Excel.
- **Open Google Sheet** and **Load CSV** are for admins. Load CSV shows a downloaded copy of the sheet instead of the live data.
- **Request level change** and **Log out**. Admins also see **Admin Tools**, with a badge for pending requests.

**Filters (left panel)**
- Testers, Technology, Date, Team and Status. Nothing selected means all.
- Dates are picked on a small calendar. Click days to toggle them, Shift-click for a range.
- **Settings** holds the pass limits (4G: 5 Mbps DL, 1 Mbps UL; 5G: 25 Mbps DL, 3 Mbps UL) and the daily target (3 tests a day, average score 0.5). Changes apply to your view only.

**Cards**
- Dashboard tiles: test count, average DL / UL, below or failed, listed testers, unique testers.
- Test location map, with Map, Satellite, Dark and Minimal layers, Find my location and Copy as image.
- Samples per day calendar.
- Technology summary and Failure count breakdown.
- Daily target list and WhatsApp broadcast.
- Top 5, Top 10 and Bottom 10 testers, and the Tester viewer.
- Throughput candles (per hour, day or week).
- Samples per day, Pass rate per day, Download speed distribution, Samples by hour.
- Test logs, with search. Clicking a row jumps to its pin on the map.

Each card has a **Hide / Show** button, and the list cards have **CSV** and **Copy** buttons.

**Status of a test**
- **PASS:** DL and UL both meet the limits for its technology.
- **BELOW THRESHOLD:** one of the two is under its limit.
- **FAILED:** both are under their limits.

## Importing results

The Google Form is still the recommended way to log a test. The Import tab is for people who have a `results.csv` export or result screenshots.

1. Press **Import results**.
2. Check **Engineer Name**. An engineer's name comes from the login and cannot be changed. Leads and admins search and pick a name.
3. Pick the **Team Assignment** (prefilled with your own team when it is in the list) and type your **Mobile Phone Number** (11 digits starting with 0).
4. Choose the method:
   - **Ookla Results CSV:** choose the file, or open "Can't select the file?" and paste the CSV text.
   - **Ookla Result Screenshot:** choose full, uncropped screenshots. They are read in your browser and never uploaded. Check the values and edit any cell that was read wrongly (yellow cells are empty or out of range).
5. Press **Process & Check Duplicates**. Each row is marked Valid or Invalid with a reason (Duplicate, WiFi, No Test ID, Bad Date and so on).
6. Press **Import N Valid Rows to Sheet**. The message reports how many rows were added, skipped (already in the sheet) or rejected, and the dashboard refreshes.

Importing the same file twice is safe: a Test ID that is already in the sheet is skipped.

## The Google Sheet

| Tab | Used for |
|---|---|
| `Form Responses 1` | The test results (columns A to L). Written by the Google Form, the Import tab and the old Importer. |
| `List` | Engineer names in column A and team names in column C. Feeds the register name search, the import name and team lists, and the admin tester list. |
| `Access List` | Name, Team and default access level for each person. |
| `Users`, `Requests`, `Log` | Accounts, pending requests and the activity log. Created by `setup()`. |

Passwords and authenticator secrets are kept in the Apps Script project's private storage, not in the sheet.

## The old Importer

The standalone Importer page and its Apps Script project are still running, by choice, until they are switched off.

- Both importers skip Test IDs already in the sheet, so a test cannot be saved twice.
- The old project's links are a way around the login: its data link returns all rows and its import link accepts rows without a login. To retire it, remove `doGet` and `doPost` from the old project (or make them return an error) and deploy a new version. This also stops the old Live Dashboard and the old Importer page.
- The two importers are separate Apps Script projects and cannot share a lock. The new service re-checks its own writes; the old one does not.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| "Could not reach the login service" | Wrong `AUTH_API_URL`, deployment not set to Anyone, or no internet. |
| Dashboard shows old behaviour after a code change | The Apps Script deployment was not updated to a new version. |
| Import tab: "Could not load the name and team lists (Unknown action)" | `index.html` was updated but the service is still on the old version. Deploy the new `Code.gs`, then press **Retry loading lists**. |
| Import tab: name or team list is empty | The List tab has no names in column A or no teams in column C. |
| Import result says rows were rejected | The reason is in the message: name or team not in the list, invalid mobile number, bad date, bad Test ID, bad DL/UL or bad Lat/Lon. |
| "The results tab layout has changed" | Column E of `Form Responses 1` is no longer the Test ID. Columns must stay in the A to L order. |
| "The CSV reader could not load" or "The OCR engine could not be loaded" | No internet, or the library CDN is blocked. These load the first time the Import tab is used. |
| Screenshot row shows "No ConnType" or "No Test ID" | The OCR could not read that value. Type the connection type (4G or 5G) into the cell. A missing Test ID cannot be typed in: use the results.csv or the Google Form for that test. |
| User sees no data | Lead: no team in the Access List, or the samples' Team Assignment does not match. Engineer: the name on the samples differs from the registered name. |
| Verification or setup email does not arrive | Check spam. The Gmail daily quota may be used up and Brevo not configured. |
