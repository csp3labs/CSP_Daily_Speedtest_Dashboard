# CSP3 Ookla Analytics Dashboard

A single-file HTML dashboard for the **Ookla Speedtest Results** Google Sheet (mobility test logger). It shows throughput, pass/fail status, daily participation, a test-location map, and builds a WhatsApp broadcast of who failed or has not submitted.

## Files

| File | Purpose |
|---|---|
| `CSP3_Ookla_Analytics_Dashboard.html` | The dashboard. One file, logo and background are embedded. |
| `auth/Code.gs`, `auth/appsscript.json` | The login and data service. **Replaces `ookla_doGet.gs`.** Setup is in `AUTH_SETUP.md`. |
| `AUTH_SETUP.md`, `AUTH_SPEC.md` | Login setup steps and the login specification. |
| `ookla_doGet.gs` | The first, open data service (no login). Kept for reference. Retire it, see `AUTH_SETUP.md`. |
| `SPEC.md` | Detailed specification and definitions. |

## Setup

Follow `AUTH_SETUP.md`. In short: create the Apps Script project from `auth/Code.gs`, run `setup()`, deploy as a web app, paste its URL into `AUTH_API_URL` in the HTML file, then register as the first admin.

## Login and access levels

Users register with their name (picked from the list), email and a password, confirm the email with a code, and wait for an admin to approve. At login they enter email or name, password and the 6-digit code from an authenticator app (scanned once from a QR at the first login). A session lasts until 22:59H Manila time.

| | Admin | Team Lead | Engineer |
|---|---|---|---|
| Samples | All | Samples whose Team Assignment is their team | Their own |
| Rank tables, Tester viewer, tester counts | Yes | Their team | No |
| Daily target | Yes | Their team | No |
| WhatsApp broadcast | Yes | Yes | No |
| Export | Everything | Their team | Their own |
| Load CSV, Open sheet, Admin page | Yes | No | No |

Every user can send a **Request level change**. Admins approve registrations and level changes (batch approve with Select all) on the **Admin** page, and can disable users, set level and team, unlock accounts, reset an authenticator, or sign someone out everywhere. The Log tab lists every login and admin action.

The data is filtered on the server before it is sent, so each person's browser only ever receives what they may see. Phone numbers are never sent.

## Using the dashboard

**Filters** (left side panel): Testers, Technology, Team and Status are multi-select lists, and Date is a small month calendar where you can pick several days (click to toggle, Shift-click for a range of days that have tests). Selected dates show as chips you can remove, with Today, Latest day and Clear dates shortcuts. Days with tests are bold, and you can also pick a day with no tests. Nothing selected means all. Click a list item to toggle it. Clear filters resets everything. The panel has its own **Hide** button, and the hamburger button in the header shows or hides it again (on phones it slides over the page). The Settings card below the filters holds the thresholds and the daily target. Filters apply to the numbers, charts, tables, the map and the exports.

**Header buttons**

- Under the title: your name and level, **Admin Tools** (admins only), **Request level change** and **Log out**.
- **Open Google Sheet** (admins only) opens the Google Sheet.
- **Auto-sync: ON/OFF** refreshes every 60 seconds. The choice is remembered.
- **Refresh** (with a refresh icon) reloads the data now.
- **Export** opens a menu: choose **Selected data only** (current filters) or **All data** (All team rows or All my rows for non-admins), then **CSV file** or **Excel file**. The phone column is not included. An **Ookla Result URL** column is added to the export (it is not added to the Google Sheet).
- **Load CSV** (admins) opens a downloaded copy of the sheet. Auto-sync pauses until you press Reload sheet.

**Dashboard card** (five equal tiles for the day in focus: the selected day, otherwise today, otherwise the latest day with data): test count, average DL/UL, below or failed, total listed testers, and unique testers (count, then "x of y", percent and samples underneath).

**Map**: Google Map, Satellite, Dark and a **Minimal (no labels)** layer, a find-me button, and pins coloured Pass (green), Below threshold (amber) or Failing (red). Click a pin for the test details and a link to the Ookla result. **Copy as image** copies the current map view (with pins and legend) to the clipboard, or saves a PNG if the browser blocks clipboard images. Click a row in the Test logs table to jump to its pin.

**Calendar**: each day shows the test count, then the testers who submitted out of the tester list ("124 of 193") and the percent. Click days to drill down. The month arrows change month.

**Technology summary**: one bar per technology band, always drawn to 100% so the gap to a full pass rate is visible (striped segments). Click a below-threshold or failed segment to filter the dashboard to that technology and status. Click it again to clear. An averages panel sits on the right.

**Daily target**: each test scores 1 if DL and UL meet the thresholds for its technology (4G 5/1 Mbps, 5G 25/3 Mbps by default), otherwise 0. A tester meets the target for the day with at least 3 tests and an average score of 0.5 or higher. The card lists testers on the list who do not meet it, with the reason (no tests, needs more tests, or low score). Both limits are editable in Settings.

**WhatsApp broadcast**: press **Generate list** to build the message for the selected day (or today). It has "Personnel Below Daily Target" (with passed tests, score and tests still needed) and "Personnel Without <date> Samples". The time is taken when you press Generate list, and you can edit the text before **Copy for WhatsApp** or **Open WhatsApp**.

**Tables and charts**: Failure breakdown, Top 5 testers by tests completed, Top 10 and Bottom 10 testers (rank by DL + UL, DL, or UL), Tester viewer (with score and team), DL and UL candlestick charts (per hour, day or week), daily trend charts, and the searchable Test logs with a link to each Ookla result.

**Ookla result links**: a Test ID with 11 digits links to `https://www.speedtest.net/my-result/a/<ID>` and one with 10 digits links to `https://www.speedtest.net/my-result/i/<ID>`. Other lengths get no link. The links appear in the Test logs, the map pop-ups and the exports.

**Copy and CSV on lists**: Top 5, Top 10, Bottom 10, Daily target, Tester viewer and Test logs each have **CSV** and **Copy** buttons.

**Hide/Show** on each card, plus Collapse all and Expand all in the filter panel. The layout choice is remembered in the browser.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| "Could not load the data" or login errors | See the troubleshooting table in `AUTH_SETUP.md`. |
| Tester list tiles show names only from the data, or the daily-minimum card says the list is unavailable | The updated `doGet` is not deployed, or the **List** tab name differs. |
| Team filter is empty | The sheet has no Team Assignment column yet, or `doGet` was not updated for column C. |
| Map shows no tiles or "Map library could not load" | No internet access, or the map host is blocked. |
| Copy as image says some tiles were blocked | The tile server does not allow image capture. Pins and legend are still included. Use the Minimal (no labels) layer for a full capture. |
| Excel export says the library could not load | No internet access. Use CSV. |
| Pins missing for some tests | Those rows have no coordinates. The map header shows "x of y tests plotted". |
| Clipboard copy does nothing | The browser blocked clipboard access. The text can still be selected in the WhatsApp card and copied by hand. |

## Notes

- Google tile links are unofficial. If tiles stop loading, that is the first place to check.
- Duplicate Test IDs are counted once in the dashboard. The "Entire sheet" export still includes every row.
- Browser storage keeps three settings: auto-sync on/off, collapsed cards, and whether the filter panel is open.
