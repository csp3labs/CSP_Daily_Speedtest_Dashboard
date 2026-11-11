# CSP3 Ookla Daily Dashboard: Change log

## Phone layout and pending badge (latest)

- **Phone layout reworked** (screens under 700 px wide): no more sideways scrolling, tighter margins, a compact header with the user buttons in one row, smaller buttons and text, two-column tiles, card headers that wrap cleanly with the description under the title, calendar and tables sized for small screens, and the Export menu opens as a bottom sheet.
- **Charts size to their card** instead of being scaled down, so axis text stays readable on phones. Candle charts no longer draw giant candles when there are only a few days, and charts redraw when the screen width changes (for example when rotating the phone).
- **Pending badge on Admin Tools:** a red count shows how many registrations and level requests are waiting. It updates at login, every 60 seconds, and after each approval or rejection.

## Login and access levels

- **Login added.** Register (name picked from the list, email, password), confirm the email with a code, admin approves. Login uses email or name, password and a 6-digit Google Authenticator code. The QR is scanned once at the first login. Sessions end at 22:59H Manila time.
- **Access levels.** Admin sees everything. Team Lead sees samples whose Team Assignment matches their team. Engineer sees only their own samples. Data is filtered on the server before it is sent. Phone numbers are never sent.
- **Role views.** Engineers have no rank tables, tester viewer, daily target, WhatsApp card, tester counts, or Tester and Team filters. Team leads have no Team filter. Load CSV and Open Google Sheet are admin only.
- **Admin Tools.** Pending registrations and level requests with Select all and batch Approve or Reject, a Users tab (level, team, disable or enable, unlock, reset authenticator, sign out everywhere) and a Log tab.
- **Level change requests** by any user, approved by admins only.
- **Backend** (`auth/Code.gs`): separate Apps Script project, salted PBKDF2 password hashes, authenticator secrets kept in private Script properties, lockout after 5 failed attempts for 15 minutes, email through Gmail with Brevo as fallback.
- **Auto-sync** now every 60 seconds (was 30).
- **Setup screen** recommends only Google Authenticator, with Google Play and App Store links.
- **Titles:** "CSP3 Ookla Daily Dashboard" for the page and header, "CSP Daily OOKLA Dashboard" on the login page.
- **Header** reorganised: name, level, Admin Tools, Request level change and Log out sit under the title. Reload sheet is now **Refresh** with an icon. The export buttons are replaced by one **Export** menu (Selected data only or All data, then CSV or Excel).

## Earlier versions

1. Dashboard from the Google Sheet with KPIs, charts and tables, CSP3 branding.
2. Apps Script data source, auto-sync toggle, CSV and Excel export.
3. Leaflet map with Google Map, Satellite and Dark layers, find-me, status pins, legend.
4. Multi-select filters, calendar drill-down, technology bars, tester viewer, Top and Bottom 10.
5. Candlestick throughput charts.
6. Tester list from the List tab: Top 5, daily minimum, tester count views, WhatsApp report, list exports.
7. Team Assignment filter.
8. Layout revamp: side filter panel, tiles card, calendar with tester share, minimal map layer and copy as image, Ookla result links, 100% technology bars with click-to-filter, consistency scoring for the daily target, WhatsApp list generated on demand.
9. Date filter changed to a multi-date picker.
