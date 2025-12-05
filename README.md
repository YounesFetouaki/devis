# Devis Web App — README

**Purpose:**
This README describes the requirements and behaviour for a small personal web app that creates, previews and prints **devis** (quotes/estimates). The app is intended for **personal use** and must use **Moroccan dirham (MAD)** as the default currency. It must support **TVA (VAT)** handling and all common business rules tied to devis (numbering, expiration, versions, signatures, printing, PDF export, etc.). You told me you already have a visual template you want to use — this README explains how the app should behave and how to plug in your template (no code included here).

---

## 1 — Project overview

A lightweight single-user web application that lets you create, edit, preview, export and print professional devis in MAD. Key goals:

* Keep it simple and focused on personal use (no multi-tenant complexity).
* Use your provided visual template for HTML preview and PDF export.
* Correct and transparent handling of TVA (per-line and totals), discounts, rounding and legal fields commonly shown on a devis.

---

## 2 — Core features (what the app must do)

* Create / edit a devis with fields: client info, company info, devis number, date, valid-until date, items, quantities, unit prices, tax rate per line, discounts (line-level or global), notes.
* Preview the devis using your template (WYSIWYG preview that matches the exported PDF).
* Export to PDF preserving layout and fonts of your template.
* Print from browser or send to a local network printer.
* Track status: **Draft**, **Sent**, **Accepted**, **Rejected**, **Expired**.
* Versioning: keep a history of edits and increment a version number when modifying after sending.
* Acceptance flow: support attaching an electronic signature (image/png from a drawing canvas) when a client accepts.

---

## 3 — Currency and formatting

* Default currency: **MAD** (Moroccan dirham). Use local format for display: group thousands with non-breaking spaces and use comma or dot consistent with your locale preference. Example formats to consider:

  * Numeric currency display: `1 234.00 MAD` or `1,234.00 MAD` depending on your locale settings.
  * Use the locale `fr-MA` or `fr-FR` for date/number formatting depending on your preference for French-style formats.
* All amounts shown on the devis must clearly state the currency (MAD) near totals.

---

## 4 — TVA (VAT) handling and business rules

* Each line item can include an explicit `taxRate` (e.g., `0%`, `10%`, `20%` — whatever rates you use in practice). The app must calculate:

  1. Line net = unitPrice × qty − line discount
  2. Line tax = line net × taxRate
  3. Sum of line nets = subtotal
  4. Sum of line taxes = total TVA
  5. Apply global discount (if any) before/after VAT depending on the business rule you choose (document which option you select and keep consistent)
  6. Final total = subtotal − discounts + total TVA
* Rounding rules: round monetary values to **two decimal places (centimes)** at the line level and aggregate sums. Document the rounding behavior clearly so totals match printed numbers.
* Display a tax breakdown section that lists each tax rate used and the taxable base for that rate along with the tax amount.
* If a line is tax-exempt, show `TVA: Exonéré` or `0%` explicitly.
* Add a clear note on the devis about whether prices are `TTC` (toutes taxes comprises) or `HT` (hors taxes). Typical phrasing: `Prix en MAD, TVA non incluse (HT)` or `Prix en MAD, TVA incluse (TTC)` depending on how you present prices.

> **Important:** This README documents behavior for calculations and display only. Confirm with an accountant or local tax authority if you plan to use the devis for official invoicing or compliance-sensitive workflows.

---

## 5 — Numbering & identification

* Format example: `DEV-YYYY-XXXX` (e.g., `DEV-2025-0001`). Keep a sequential counter for your personal use.
* Include a `version` number that increases when a revisited devis is re-issued (for example `v1`, `v2`).
* Show the devis number and version prominently on the document and PDF.

---

## 6 — Template integration (how to use your template)

* Your template is the authoritative layout used for both browser preview and the PDF export. The README assumes the template accepts placeholder values for all fields (company info, client info, items, totals, notes, logos, etc.).
* Required placeholders (example keys you should have in the template):

  * `{{companyName}}`, `{{companyAddress}}`, `{{companyPhone}}`, `{{companyEmail}}`, `{{logoUrl}}`
  * `{{devisNumber}}`, `{{version}}`, `{{date}}`, `{{validUntil}}`, `{{status}}`
  * `{{client.name}}`, `{{client.company}}`, `{{client.address}}`, `{{client.email}}`, `{{client.phone}}`
  * An items loop to render description, qty, unit price, tax, line total
  * `{{subtotal}}`, `{{discount}}`, `{{taxTotal}}`, `{{total}}`
  * `{{footerText}}` (legal notes, bank details, payment terms)
* Ensure your template contains responsive/print-friendly CSS and that fonts are embedded or available when generating PDFs so the PDF output exactly matches the preview.

---

## 7 — Printing & PDF export behavior (user-facing)

* The preview screen should match the PDF exactly.
* Provide two printing/export options:

  1. **Download PDF** — generates a PDF file that matches the template and downloads to the user's machine (suitable for sending by email).
  2. **Print** — open the template in a print-friendly window and trigger the browser print dialog (for local printers).
* When exporting to PDF, ensure the generated file name includes the devis number (e.g., `DEV-2025-0001.pdf`).

---

## 8 — Acceptance, signatures & workflow

* Allow the user (or client) to accept the devis using a simple acceptance flow: attach a handwritten signature image, acceptance date and status update. Store the acceptance metadata with the devis record.
* After acceptance, optionally convert the devis to an invoice (if you plan that feature later); at minimum mark the status `Accepted` and keep a snapshot of the accepted document.

---

## 9 — Data to display on the devis (recommended fields)

* Company header: name, address, phone, email, trade ID (if any), logo
* Devis metadata: number, version, creation date, valid-until, status
* Client block: name, company, address, contact details
* Items table: description, qty, unit price (MAD), discount (per line), tax rate, line total
* Totals block: subtotal (HT), total discounts, TVA (per rate and total), total TTC
* Payment terms: e.g., `Paiement à réception`, `Net 30 jours`, or your preferred terms
* Footer/legal text: bank details, registration numbers, or a short legal clause

---

## 10 — Storage and backups (personal use suggestions)

* Keep a local backup export option (CSV or JSON) so you can save all devis locally.
* Optionally export all documents periodically to an external drive or a private cloud (Dropbox, Google Drive) for redundancy.

---

## 11 — Security & privacy (personal-use guidance)

* For a strictly personal app, avoid exposing the service publicly unless you secure it (password or local-only). If you host it online, protect access with a password and HTTPS.
* Do not store sensitive client data in public/unsecured locations. If you keep client personal data, consider encrypting backups.

---

## 12 — Localization and language

* Use French by default (`fr-MA` / `fr-FR` formats) since many Moroccan businesses work in French; switchable text labels are recommended if you later want English.
* Ensure date formats on the document are explicit (e.g., `05 décembre 2025`) to avoid confusion.

---

## 13 — Legal & compliance note

This README is a technical and UX specification for a personal quoting tool. It is **not** legal or tax advice. If you rely on these devis for tax reporting, invoicing, or other legal uses in Morocco, check local rules and consult an accountant.

---

## 14 — Next steps (suggested)

1. Insert your template placeholders as listed above.
2. Decide and document rounding and discount rules (before/after VAT).
3. Choose how the app will store and backup devis locally.
4. Decide whether the acceptance signature needs timestamping or IP/capture metadata.
5. Optionally prepare a small test dataset of 3 example devis to verify formatting and calculations.

---

## Contact / support

If you want this README adapted into a one-page printable PDF version of the spec or want me to refine the wording for a specific template you’ll paste here, tell me and I’ll update the document.

---

*End of README*
