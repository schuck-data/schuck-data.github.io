# Forms Setup — Client Inquiry & Subcontractor Application

*Date: 2026-05-26 · The Contact and Careers pages now link out to Google Forms. This doc tells you what to build and where to paste the links.*

## What's already wired in the site

Two clearly-marked placeholder links are in place, each with an HTML comment next to it:

| Page | File | Placeholder to replace |
|---|---|---|
| Contact (client) | `CONTACT/index.html` | `https://forms.gle/REPLACE-WITH-CLIENT-INQUIRY-FORM` |
| Careers (subcontractor) | `CAREERS/index.html` | `https://forms.gle/REPLACE-WITH-SUBCONTRACTOR-FORM` |

Direct email/call/text remain on both pages as a fallback, with a line noting "if the form doesn't load on your network, email us" — because **Google Forms is blocked on some government/corporate networks**, which is exactly the audience sensitivity from the grayware review. Keep that fallback.

**To go live:** create each form (fields below), click **Send → link** (the `🔗` tab), optionally tick "Shorten URL," copy the `forms.gle/…` link, and paste it over the matching placeholder. Search the repo for `REPLACE-WITH` to find both spots.

## Form 1 — Client inquiry (Contact page)

Mirror the homepage funnel so the "What do you need?" options match the site.

1. **Name** — short answer — *required*
2. **Email** — short answer — *required* — turn on response validation → Text → Email
3. **Company / organization** — short answer — optional
4. **Phone** — short answer — optional
5. **What do you need?** — multiple choice — *required* — options (verbatim from the homepage):
   - Fix a broken data pipeline
   - Create a data dashboard
   - Data Health Report
   - Prepare for AI
   - Risk analysis and statistical modeling
   - Rent-an-Expert data consulting
   - Something else
6. **Timeline** — multiple choice — ASAP / 1–3 months / 3–6 months / Just exploring
7. **Budget range** — multiple choice — optional — Under $5k / $5k–$15k / $15k–$50k / $50k+ / Not sure yet
8. **Tell us about your project** — paragraph — *required*
9. **How did you hear about us?** — short answer — optional

**Form settings:** Responses → "Collect email addresses," turn on "Send respondents a copy of their response," and link responses to a Google Sheet. Set a friendly confirmation message ("Thanks — we'll be in touch within one business day.").

### Optional upgrade: pre-fill "What you need" from the homepage
Your homepage funnel options (and `docs/decisions.md`) anticipated pre-filling the selected service. Once the form exists: in Google Forms, **⋮ → Get pre-filled link**, choose one service, copy the generated URL, and note the `entry.XXXXX=` parameter. You can then point each homepage option at the form with that service pre-selected, e.g. `…/viewform?usp=pp_url&entry.123456=Create+a+data+dashboard`. (Today the homepage options point to `/CONTACT/`; this is a later enhancement, not required.)

## Form 2 — Subcontractor / partner application (Careers page)

1. **Name** — short answer — *required*
2. **Email** — short answer — *required* — email validation
3. **Phone** — short answer — optional
4. **Location / time zone** — short answer
5. **LinkedIn or portfolio URL** — short answer
6. **Areas of expertise** — checkboxes — Dashboards / BI · Data pipelines & ETL · Data quality · Statistical modeling · AI / ML · Cloud & data infrastructure · Other
7. **Tools you work in** — checkboxes (add an "Other") — SQL · Python · R · Power BI · Tableau · Looker · dbt · Excel · Snowflake / BigQuery / Redshift
8. **Years of professional experience** — multiple choice — 0–2 / 3–5 / 6–10 / 10+
9. **Availability** — multiple choice — Full-time · Part-time · Project-based · Occasional
10. **Resume** — *see note* — recommended: short answer, "Paste a link to your resume (Google Drive, Dropbox, etc.)"
11. **Tell us what you do** — paragraph — a few sentences

**Resume — file upload vs. link.** Google Forms' "File upload" question forces respondents to **sign into a Google account** and consumes *your* Drive storage. That sign-in requirement is a friction/privacy issue and is more likely to be blocked on locked-down networks. Recommendation: ask for a **resume link** (option 10 above). If you'd rather collect actual files, switch it to a File upload question and accept the Google-sign-in tradeoff.

**Form settings:** collect email addresses, send a copy to respondents, link to a Google Sheet, confirmation message ("Thanks for your interest — we'll reach out if there's a fit.").

## Two small consistency notes

- The Careers page `<meta description>` still says "Send your resume to dakota@schuckdata.com." Once the form is live you may want to update it to mention the application form.
- Keep both forms' confirmation/branding consistent with the site voice (calm, direct, no exclamation overload).
