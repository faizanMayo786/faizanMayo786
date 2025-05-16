Here’s a **flow diagram**

---

**🔄 End-to-End Automation Flow**

1. **Content & Scheduling**
   • Draft “Espen-style” LinkedIn post
   • Schedule & publish via Meet Alfred (or Dripify)

2. **Engagement Trigger**
   • Prospect comments the magic word (“o4”)
   • Alfred detects the comment

3. **Auto-DM Delivery**
   • Alfred (or your custom script) sends a personalized DM with the Notion link
   • Prospect record created in Google Sheet/CRM with status “Contacted”

4. **Lead Enrichment**
   • Zapier/Make picks up new sheet rows → calls Snov.io/SalesQL API
   • Populates email, company, role back into the sheet/CRM; marks “Enriched”

5. **Email Warm-Up & Outreach**
   • Enriched emails enter Instantly domain-warmup
   • After 3–5 days → pushed to Smartlead/Lemlist sequence:
   – *Email 1:* Prompt stack
   – *Email 2:* Case study
   – *Email 3:* Call-to-action + Calendly link

6. **Intent-Based Handoff**
   • CRM flags “Hot” when:
   – ≥2 email link clicks **OR**
   – Reply to DM/email
   • Zapier notifies you (Slack/email) to book a 1:1 call

7. **Reporting & Optimization**
   • Google Data Studio dashboard (connected to sheet/CRM)
   • Weekly review of: post reach, comment volume, DM/open rates, email metrics, booked calls
   • Iterate post copy, sequence timing, and tool settings

---

**Example Week 1 Timeline**
Mon – Write + schedule LinkedIn post
Tue – Post goes live; auto-DMs sent; new leads logged
Wed – Leads enriched automatically
Thu – Domain warms; first email goes out
Fri – Review dashboard; flag hot leads; book discovery calls


