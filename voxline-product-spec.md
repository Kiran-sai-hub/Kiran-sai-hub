# Voxline — Product Brief & Build Guide

*Voice-first factory software for Indian manufacturers (reference product: "Voxline by Athreix", manufacturing.athreix.com)*

Prepared: 2026-11-17 · For: founding engineers · Status: working draft

---

## 0. READ THIS FIRST — what is known vs. what is inferred

This document is built from the **public marketing website only** (HTML, JS and copy of the landing page). There is **no** access to the real backend, the real dashboards, customer data, or the team behind it. A web search found no independent information about Athreix (no customers, funding, press).

Every statement in this document carries one of these tags:

| Tag | Meaning |
|---|---|
| **[OBSERVED]** | Stated directly on the site (copy or page source). Treat as fact about the *claimed* product. |
| **[INFERRED]** | A reasonable deduction from what the site implies. Likely, not confirmed. |
| **[PROPOSED]** | Our own design recommendation. Not from the site. We decide these. |
| **[VERIFY]** | An external fact (Meta, Tally, law, vendor pricing) that changes. Re-check before relying on it. |

Important limits of the evidence:

- The site's "Watch it run" demo is a **scripted scroll animation** with hard-coded sample numbers (e.g. "420 pieces", "₹1.2 Cr", "86/92 present"). It is **not** a live product. Nothing on the page proves the voice pipeline works, at what accuracy, or in what languages beyond the claim.
- The 13 dashboards are described by **one sentence each**. Their fields, workflows and screens are **not** public. Section 6 therefore *proposes* the detail.
- No security, certification, data-residency, retention, deployment or AI-vendor information is published.

So: sections 1–5 are mostly observed; sections 6 onward are largely our design. Do not describe the product to a customer as if we have verified accuracy or compliance claims we have not tested.

---

## 1. One-paragraph summary

Factory staff report what happened by sending a ~20-second **WhatsApp voice note** (or a photo) in Marathi, Hindi or English. The system transcribes it, extracts the numbers, names and machines, **reads back what it understood**, and **saves nothing until the person confirms**. The confirmed record is filed into the right one of **13 dashboards** (grouped into 4 factory areas). If someone fails to report, it sends a WhatsApp reminder and then notifies the manager. Each morning the owner gets a short briefing of what is **late, due and missing**, and can ask the factory questions by voice or text on a single owner screen. It runs **alongside** Tally and payroll software rather than replacing them. **[OBSERVED]**

Tagline: **"Say it. It's done."** Sub-line: "Your people speak for 20 seconds. Voxline fills every register, report and dashboard." **[OBSERVED]**

Core insight **[INFERRED]**: ERP/MES software fails in small factories because it is *empty* — nobody types into forms. The site says it directly: **"Empty software is useless. Voice fills it."** Voxline makes data entry cost ~20 seconds on a channel workers already use.

---

## 2. Target customer and users

**Customer (buyer)** **[OBSERVED/INFERRED]**: Indian manufacturers, ~under 100 to 500+ employees (the pilot form offers bands: *Under 100, 100–250, 250–500, 500+*). Messaging emphasises ₹ pricing, GST and Tally. Example sample data on the site suggests **engineered/fabricated products** (pump skid, conveyor frame, welders, slings, tools, machines) — an SME engineering/fabrication shop is the implied first vertical **[INFERRED]**.

**Decision maker**: the factory owner (single "owner screen", owner Q&A). Sales is "Book a pilot → we will call you", i.e. founder/owner-led and phone-based **[OBSERVED]**.

**Personas**

| Persona | Needs | How they touch the system |
|---|---|---|
| Shop-floor worker / supervisor | Report quickly with zero training | WhatsApp voice note / photo; confirms read-back by voice |
| Department head / manager | Know who has not reported; see their department | Receives escalations; department dashboard |
| HR / finance | Attendance, leave, payroll inputs, payments | HR and payment dashboards; sees salary data |
| Owner | One view of the whole factory; ask questions | Morning briefing, owner screen, voice/text Q&A |
| Admin (ours/implementer) | Configure factory, roles, reminders | Back-office console **[PROPOSED]** |

---

## 3. The end-to-end flow (the six "stations") **[OBSERVED]**

The site's scroll sequence defines the product loop exactly:

1. **Speak** — A worker sends a voice note. No forms, no typing.
2. **Voxline understands** — It picks out the numbers, names and machines.
3. **You confirm** — Nothing is saved until the person says yes.
4. **It files itself** — The record goes to the right dashboard.
5. **Voxline follows up** — A missing report gets a WhatsApp reminder, then the manager is told.
6. **You see everything** — A short briefing every morning: what is late, due and missing.

Headline numbers: **20 seconds** to report · **13 dashboards** · **4 areas** of the factory · **1 screen** for the owner.

FAQ claims worth treating as product requirements **[OBSERVED]**:

- *Do workers need to learn new software?* No — they send a voice note or a photo, "the way they already do on WhatsApp."
- *What if it hears something wrong?* It repeats what it understood before saving; the person can **correct by voice**.
- *Do we stop using Tally?* No — works alongside Tally and payroll software.
- *Who can see what?* Each person sees only what their role allows; **salaries and payments stay with HR, finance and the owner**.

The site contrasts a **form with 8 fields** against **voice, 0:20 done**, with the caption "Marathi, Hindi and English · Works with Tally · On WhatsApp."

### 3.1 Interaction state machine **[PROPOSED]**

```
RECEIVED (audio/photo arrives on WhatsApp)
  → IDENTIFIED   (map WhatsApp number → employee, role, department)
  → TRANSCRIBED  (speech-to-text, language detected)
  → EXTRACTED    (intent + entities → structured draft record)
  → CONFIRMING   (system replies with read-back; waits for yes / correction)
       ├─ "yes"/equivalent        → COMMITTED  → FILED (dashboard record created)
       ├─ correction (voice/text) → re-EXTRACT → CONFIRMING   (max N loops)
       ├─ "no"/cancel             → DISCARDED
       └─ no reply within T       → EXPIRED (nothing saved; reminder may fire)
FAILED  (unintelligible / low confidence) → ask to repeat; after N tries → escalate to human/manager
```

Rules: no write to a business table before COMMITTED. Keep the raw audio, transcript, extracted draft, confirmation and final record linked for audit.

---

## 4. Product surface summary

| Surface | Status |
|---|---|
| WhatsApp voice/photo intake and replies | **[OBSERVED]** core channel |
| Read-back & confirm loop (voice correction) | **[OBSERVED]** |
| 13 dashboards in 4 areas | **[OBSERVED]** (names + 1-line purpose only) |
| Missing-report reminders (WhatsApp) then manager notification | **[OBSERVED]** |
| Morning briefing (late / due / missing) | **[OBSERVED]** |
| Owner single screen | **[OBSERVED]** |
| Owner Q&A by voice or text, grounded in day's data, flags missing data | **[OBSERVED]** |
| Role-based visibility; salary/payments restricted | **[OBSERVED]** |
| Marathi, Hindi, English | **[OBSERVED]** |
| Tally / payroll "alongside" | **[OBSERVED]** (mechanism unknown) |
| On-site training included | **[OBSERVED]** (a services cost to model) |
| 6-week single-department pilot | **[OBSERVED]** |
| Admin console, audit log, tenant config | **[PROPOSED]** (needed, not shown) |

---

## 5. The 13 dashboards (**[OBSERVED]** names and one-line purpose; fields are **[PROPOSED]**)

The site groups them into four areas, each with a sample headline figure and sample activity feed (all sample data).

### 5.1 Area 1 — People (2 dashboards)
Sample KPI shown: **86/92 Present today**.

| # | Dashboard | Site description | Proposed core data |
|---|---|---|---|
| 1 | **HR** | Hiring, attendance, leave, payroll and exit. | Employee master, attendance (shift, in/out, present/absent), leave requests & balances, overtime, payroll inputs, hiring pipeline, exit checklist |
| 2 | **Employee dashboards** | A view for each employee, manager and department head. | Per-role personal view: own attendance, leave balance, tasks, reports submitted/missing; manager/department rollups |

Sample feed items on the site: *"Shift B attendance marked"*, *"Leave request, 2 days"*, *"Overtime: 6 people, 2 hours"*.

### 5.2 Area 2 — Business & money (4 dashboards)
Sample KPI: **₹1.2 Cr Orders this month**.

| # | Dashboard | Site description | Proposed core data |
|---|---|---|---|
| 3 | **Sales** | Customers, enquiries and orders. | Customer, enquiry, quotation, order (value, due date, status) |
| 4 | **RFQ & vendor comparison** | Vendor quotes compared side by side. | RFQ, vendor, quote (price, lead time, terms), comparison/selection |
| 5 | **Vendor payments & receivables** | What you owe and what you are owed. | Payables, receivables, due dates, overdue flags, payments released (restricted visibility) |
| 6 | **Projects** | Milestones, team and progress. | Project, milestones, team, % progress, delay + reason (e.g. "waiting for material") |

Sample feed: *"Vendor quote received"*, *"Order confirmed: pump skid"*, *"Payment released: ₹1.8 L"*.

### 5.3 Area 3 — Shop floor (3 dashboards)
Sample KPI: **420 Pieces today** (gauge).

| # | Dashboard | Site description | Proposed core data |
|---|---|---|---|
| 7 | **Machine & tool register** | Machines, instruments and lifting tools. | Asset master (machine/instrument/lifting tackle), status (running/down), calibration & inspection dates, defects with photo |
| 8 | **Safety toolbox talks** | Daily safety briefing and who attended. | Talk topic, date, conductor, attendee list |
| 9 | **Kaizen ideas** | Improvement ideas from staff and what they saved. | Idea, submitter, status, estimated/realised saving |

Sample feed: *"420 pieces, 1 machine down"*, *"Toolbox talk done, 14 attended"*, *"Sling damaged, photo sent"*.
Note: production count + machine-down reporting is the flagship voice example ("420 pieces done today. One machine is down.") yet no separate "production" dashboard is named — it is presumably surfaced inside the shop-floor/machine area **[INFERRED]**. Confirm scope with a pilot customer.

### 5.4 Area 4 — Management (4 dashboards)
Sample KPI: **18 Open actions**.

| # | Dashboard | Site description | Proposed core data |
|---|---|---|---|
| 10 | **Review meetings** | Meeting decisions turn into tasks. | Meeting, attendees, decisions → auto-created action items |
| 11 | **Action items** | Every task has an owner and a date. | Task, owner, due date, status, closure evidence (photo) |
| 12 | **Customer & vendor contacts** | Who handles which customer or vendor. | Contact directory, account owner mapping |
| 13 | **Document folders** | Documents by department. | Folder tree by department, uploads, permissions |

Sample feed: *"Review recorded, 7 actions"*, *"Action closed with photo"*, *"Reminder sent to Stores"*.

---

## 6. Intent catalogue and extraction schemas **[PROPOSED]**

Voice notes must be mapped to an **intent** and a **typed payload**. Start with a small closed set per dashboard and expand from real recordings.

| Intent | Example utterance (Hindi/English/Marathi mixed) | Payload |
|---|---|---|
| `production.report` | "Aaj 420 pieces hue, ek machine band hai" | date, shift, item?, quantity, machine_down[] |
| `machine.status` | "CNC 2 band hai, spindle problem" | machine_id, status, reason, photo? |
| `attendance.mark` | "Shift B mein 86 log aaye" | shift, present_count / list, absent[] |
| `leave.request` | "Mujhe 2 din ki chhutti chahiye" | employee, from, to, reason |
| `overtime.log` | "6 log ne 2 ghante overtime kiya" | people[], hours |
| `toolbox.talk` | "Safety talk hui, 14 log the" | topic, attendees/count |
| `asset.defect` | "Sling damaged" + photo | asset_id, defect, photo |
| `kaizen.submit` | "Jig change karne se 10 minute bachte hain" | idea, saving estimate |
| `vendor.quote` | "Vendor X ne ₹4.2 lakh quote diya" | vendor, rfq, amount, lead_time |
| `order.confirm` | "Pump skid ka order confirm hua" | customer, item, value, due_date |
| `payment.release` | "₹1.8 lakh payment kiya" | vendor, amount, ref (restricted) |
| `project.update` | "Conveyor frame 1 din late, welders chhutti pe" | project, delay, reason |
| `meeting.record` | recorded decisions | decisions[] → `action.create` |
| `action.create/close` | "Stores ko kal tak bolt dene hain" / "Done" + photo | task, owner, due, evidence |

**Extraction requirements**

- Normalise numbers spoken in Marathi/Hindi/English (e.g. "ek lakh pachaas hazaar", "saade chaar").
- Resolve **entities against master data** (machine names/codes, employee names, vendors, projects) with fuzzy matching; ask for clarification when ambiguous.
- Return a **confidence per field**; low-confidence fields are highlighted in the read-back.
- Use constrained/structured output (JSON schema) and reject payloads that fail validation.
- Unknown intent → ask a clarifying question; never guess a write.

**Read-back design**: concise, in the worker's language, voice reply preferred (TTS) with a text echo. Example: *"Maine suna: 420 pieces, CNC 2 band. Sahi hai?"* Accept "haan / yes / ho" and corrections ("nahi, 402").

---

## 7. Reminders, escalation and morning briefing

**Site behaviour [OBSERVED]**: missing report → WhatsApp reminder → manager is told. Morning briefing lists **late, due and missing** items. Owner example: *"3 of 8 departments have not reported: Welding, Stores, Quality. I have asked them."*

**Design [PROPOSED]**

- **Expected-report schedule** per department/role: what must be reported, by what time, which shifts, which days; holiday and shift calendar per factory.
- **Detection**: a scheduler checks each expectation at its deadline; "missing" = no COMMITTED record in the window.
- **Ladder**: T+0 reminder to the person → T+X second reminder → notify manager → included in next morning briefing. All steps configurable and rate-limited ("nag budget") to avoid alert fatigue.
- **Briefing**: one short WhatsApp message (and the owner screen) at a configured time: counts of late/due/missing, top issues (machine down, overdue payments, late projects).
- **Idempotency**: never send duplicate reminders; record every notification with status and who acknowledged.

**WhatsApp constraint [VERIFY]**: business-initiated messages outside the 24-hour customer-service window must use **pre-approved message templates**; templates need Meta approval and are priced per message category. Design reminders and briefings as templates; replies from a worker open a service window. Re-verify current rules and pricing with Meta before committing to cost assumptions.

---

## 8. Owner Q&A **[OBSERVED]** + design **[PROPOSED]**

Site: "Ask your factory anything. By voice or text. Voxline answers from today's data, and tells you what is missing." Sample Q&A (sample data):

- Q: *Which projects are late?* → "2 projects are late. Pump skid: 4 days, waiting for material. Conveyor frame: 1 day, two welders on leave."
- Q: *Who has not reported today?* → "3 of 8 departments have not reported: Welding, Stores, Quality. I have asked them."
- Q: *What do we owe vendors this week?* → "₹8.4 lakh to 6 vendors. 2 payments are overdue."

**Design principles**

1. **Answer only from the database.** Preferred pattern: LLM translates the question into a *bounded, permission-scoped query* (a fixed set of query tools/metrics), not free-form SQL over everything. Numbers in the answer must come from query results, not model memory.
2. **State data gaps.** If a department has not reported, say the answer is incomplete.
3. **Respect roles.** The Q&A runs with the asker's permissions; a department head cannot extract salaries by asking.
4. **Cite the source rows** (links to records) so the owner can verify.
5. **Evaluate**: maintain a golden question set with expected answers; block releases that regress.

---

## 9. Roles and permissions **[OBSERVED principle; matrix PROPOSED]**

Principle: *each person sees only what their role allows; salaries and payments stay with HR, finance and the owner.*

| Resource | Worker | Dept head | HR | Finance | Owner | Admin |
|---|---|---|---|---|---|---|
| Own reports/attendance | RW | R | R | – | R | – |
| Department dashboards | – | RW (own dept) | R | R | R | config |
| Attendance / leave (all) | – | R (own dept) | RW | – | R | – |
| **Salaries / payroll** | – | – | RW | R | R | – |
| **Vendor payments / receivables** | – | – | – | RW | R | – |
| Sales / RFQ / projects | – | R/RW (own) | – | R | R | – |
| Documents | by folder | by folder | by folder | by folder | all | – |
| Owner Q&A | – | scoped | scoped | scoped | full | – |

Enforce authorization in the **data layer** (row-level policies), not only the UI or the Q&A prompt.

---

## 10. Integrations

### 10.1 WhatsApp **[OBSERVED]** (mechanism **[PROPOSED]**)
- Use the **WhatsApp Business Platform (Cloud API)** via Meta or a BSP **[VERIFY: pricing, number verification, template approval, media retention]**.
- Inbound: webhook receives voice notes (typically OGG/Opus **[VERIFY]**), images, text, button replies. Download media promptly via the media ID.
- Outbound: free-form replies inside the service window; templates outside it.
- Identity: map the sender phone number to an employee; handle shared phones and number changes explicitly. Unknown numbers get a safe default (ignore / onboarding message).
- Resilience: queue inbound events, dedupe by message ID, retry outbound, handle webhook downtime. **This is a single-vendor platform dependency — track policy changes.**

### 10.2 Tally and payroll **[OBSERVED: "works alongside"]**
Mechanism is not public. Options **[PROPOSED]**:
- **Phase 1 (recommended)**: *export/import* — generate Tally-importable files/vouchers or reports; read nothing from Tally. Lowest risk.
- **Phase 2**: two-way sync using Tally's XML/HTTP interface or an on-prem connector **[VERIFY against current Tally Prime docs]**; needs a small agent on the customer's Windows machine and careful ledger/voucher mapping.
- Payroll: ingest attendance/overtime/leave into whatever payroll tool the factory uses via CSV export first; real integrations per customer only when paid for.

### 10.3 Other (not on the site) **[PROPOSED]**
Email/SMS fallback, Google Workspace, ERP exports — defer.

---

## 11. Voice pipeline **[PROPOSED]**

```
WhatsApp audio → media download → normalise (ffmpeg → 16 kHz mono)
  → VAD / noise reduction → language ID → STT (Marathi/Hindi/English, code-switching)
  → domain biasing (machine names, employee names, units) → LLM intent+entity extraction (JSON schema)
  → entity resolution vs master data → confidence scoring → read-back (TTS + text)
```

Hard problems and how to test them:

| Problem | Mitigation / test |
|---|---|
| Machine/workshop noise | Collect real noisy samples early; add noise augmentation to the test set |
| Marathi/Hindi/English code-switching | Benchmark multiple STT vendors on *your own* recordings; measure WER and, more importantly, **field-level accuracy** |
| Numbers, units and names (the data that matters) | Domain vocabulary/phrase hints; post-correct against master data; always read back numbers |
| Dialects/accents | Pilot-driven data collection with consent |
| Latency | Target a confirmation reply within a few seconds of the note **[PROPOSED target: p95 < ~10 s]** |
| Cost | Track cost per voice note (STT + LLM + TTS + WhatsApp message fees) against ₹250/employee/month |

Vendor choice **[VERIFY]**: candidates include Indian-language speech providers, cloud STT (Google/Azure/AWS), and open models (Whisper-class, AI4Bharat). Decide by benchmark, not reputation. Keep the STT/LLM behind an interface so vendors are swappable. Check data-processing terms and where audio is processed (see §13).

**Success metrics**: field-level extraction accuracy on a golden set; confirm-on-first-read-back rate; correction rate per user; time-to-record; % of expected reports received.

---

## 12. Architecture **[PROPOSED]**

```
 WhatsApp Cloud API ──webhook──▶ Ingress (verify signature, dedupe, enqueue)
                                      │
                         ┌────────────▼────────────┐
                         │  Conversation service   │  state machine (§3.1), per-sender sessions
                         └───┬─────────┬───────────┘
              Voice/NLU workers   Notification service (reminders, briefing, escalation, templates)
                         │                     ▲
                         ▼                     │ scheduler (expected-report engine)
                 Domain services ─▶ Postgres (multi-tenant, RLS) ─▶ Object storage (audio, photos, docs)
                         │
        Web app (owner screen + 13 dashboards) ◀── API/auth (RBAC) ── Q&A service (tool-based, scoped)
                         │
                  Integration workers (Tally/payroll export, later connectors)
        Observability: logs, traces, per-note pipeline timeline, cost metering, audit log
```

Recommended stack (any equivalent is fine): TypeScript or Python backend, Postgres with row-level security, a durable queue, S3-compatible storage, a React web app, a small set of swappable AI adapters. Prefer **one deployable monolith + workers** at first; split services only when forced.

**Multi-tenancy**: one tenant per factory. Every row carries `tenant_id`; RLS enforced; per-tenant encryption keys later. Decide early whether customers might demand dedicated/on-prem deployment (site silent **[unknown]**).

**Key entities**: Tenant, Factory/Site, Department, Employee (phone, role, language), Role/Permission, Machine/Asset, Customer, Vendor, Project, Order, RFQ/Quote, Payable/Receivable, Attendance, Leave, Task/Action, Meeting, ToolboxTalk, KaizenIdea, Document/Folder, **VoiceMessage → Transcript → DraftRecord → ConfirmedRecord**, ExpectedReport, Reminder/Notification, AuditEvent, UsageMeter.

---

## 13. Security, privacy and compliance **[PROPOSED — site publishes none]**

Treat as day-one requirements, not later polish:

- **Data classification**: salaries, payments and personal data (phone numbers, voice) are sensitive.
- **Voice = personal data.** Define consent, purpose, retention (e.g. raw audio deleted after N days once confirmed) and deletion on request. India's **DPDP Act 2023 and its rules** apply to personal data processing **[VERIFY current obligations, notice/consent, children's data, cross-border rules, breach notification]**. Get legal advice before launch.
- **Employee monitoring concerns**: workers must know what is recorded and why; avoid surveillance framing.
- **Third-party processors**: STT/LLM/TTS and WhatsApp providers receive personal data — sign DPAs, check data-use/training terms, record sub-processors, and consider region/data-residency options.
- **Controls**: TLS everywhere, encryption at rest, secrets management, least-privilege access, RLS, audit log of reads/writes on sensitive tables, rate limiting, webhook signature verification, backups with tested restore, vulnerability scanning, MFA for admins.
- **AI-specific**: prompt-injection resilience for voice/text/photo input (a worker saying "ignore previous instructions" must not change permissions); the Q&A tool layer enforces authorization, not the model.
- **Certifications**: SOC 2 / ISO 27001 only when customers ask; start building the evidence (policies, logging, access reviews) from the beginning.

---

## 14. Commercial model **[OBSERVED]**

- **₹250 per employee per month**; **minimum ₹25,000/month, covers up to 100 employees**; **one-time setup from ₹1 lakh**. Includes all 13 dashboards, WhatsApp and on-site training. **Prices exclude GST.**
- **Pilot**: one department, 6 weeks; pilot price not stated.
- Pilot request form: name, factory name, phone, employee band (Under 100 / 100–250 / 250–500 / 500+); success message "Thank you. We will call you soon." The site posts the form to a Google Apps Script web app with a hidden honeypot field for spam.

**Implications for us [PROPOSED]**

- **Metering**: count active employees per tenant per month; support the minimum fee and the 100-employee tier boundary; GST invoicing.
- **Unit economics to model before launch**: per-voice-note COGS (STT+LLM+TTS+WhatsApp), reminders/briefings per user, support and on-site training hours, setup effort. At 100 employees the base is ~₹3 lakh/year in subscription — margins are thin if onboarding is not standardised.
- **Concern**: per-seat pricing penalises large low-usage workforces; consider usage or tiered pricing after learning real usage.

---

## 15. Pilot and onboarding playbook **[PROPOSED, based on the 6-week pilot]**

| Week | Goal |
|---|---|
| 0 | Pick one department; collect employee list, roles, phones, languages, machines, reports expected; consent notices |
| 1 | Configure master data and expected-report schedule; WhatsApp number + templates approved; train supervisors on-site |
| 2–3 | Soft launch with human review of every extraction; collect recordings (with consent) to tune vocabulary |
| 4–5 | Remove review for high-confidence intents; turn on reminders/escalation and morning briefing |
| 6 | Measure: reports received vs expected, confirm rate, correction rate, time saved; owner review; decide to expand |

Pilot success criteria (agree in writing): report completion rate, field accuracy, owner satisfaction, worker adoption, number of "missing report" chases avoided.

---

## 16. Non-functional requirements **[PROPOSED]**

- **Availability**: WhatsApp workflow is operational-critical during shifts; target 99.5%+ initially with graceful degradation (queue and reply later).
- **Performance**: confirmation reply quickly (see §11); dashboards load in a couple of seconds on a low-end phone/browser.
- **Mobile-first web**: owners and managers will view on phones; low bandwidth tolerance.
- **Localization**: English, Hindi, Marathi UI strings and voice replies; Indian numbering (lakh/crore), ₹, IST, Indian date format.
- **Observability**: per-message timeline (received→transcribed→extracted→confirmed→filed), failure reasons, cost per note, cohort dashboards for accuracy.
- **Auditability**: immutable audit trail from audio to record; who confirmed what and when.
- **Data export**: customers must be able to export all their data.

---

## 17. Suggested build plan **[PROPOSED]**

**Phase 0 (1–2 weeks)** — Pick the vertical and 1–2 friendly factories. Record 100+ real voice notes. Benchmark STT vendors on them. Write the golden test set. *Do this before writing product code; it decides whether the idea is feasible.*

**Phase 1 — MVP loop (6–8 weeks)**: WhatsApp ingest → transcribe → extract (3–4 intents: production/machine status, attendance, action items, toolbox talk) → read-back/confirm → store → simple owner web view → reminder + manager escalation. Multi-tenant skeleton, RBAC, audit log from the start.

**Phase 2 — Pilot-ready (6–8 weeks)**: remaining shop-floor + management dashboards, morning briefing, owner Q&A (tool-based), admin console, Tally *export*, metering, DPDP basics (consent, retention, deletion).

**Phase 3 — Scale**: business & money dashboards (restricted data), Tally two-way connector, customer-specific vocabularies, usage analytics, stronger security posture, certification groundwork.

Prioritise intents by what owners actually ask for, not by the order of the 13 dashboards.

---

## 18. Key risks

| Risk | Severity | Mitigation |
|---|---|---|
| Speech accuracy on real factory audio | High | Early benchmark; confirm loop; domain vocabulary; human-in-the-loop in pilot |
| Worker/supervisor adoption | High | On-site training, minimal friction, manager accountability, measure adoption weekly |
| WhatsApp platform dependency (policy, pricing, templates, number bans) | High | Template design, fallbacks (SMS/app), BSP selection, monitoring |
| Privacy/compliance (voice + employee data, DPDP) | High | Consent, retention, DPAs, legal review |
| Hallucinated numbers in Q&A | High | Tool-grounded answers, citations, eval suite, "data missing" disclosure |
| Thin unit economics at ₹25k/month | Medium | Standardised onboarding, cost metering, pricing tests |
| High-touch sales cycle | Medium | Pilot motion, partner channels (Tally resellers/ERP implementers) |
| Domain-specific dashboards that don't match real factories | Medium | Co-design with pilot customers; configurable fields |
| Competitors (MES/ERP vendors adding WhatsApp bots) | Medium | Domain templates, accumulated per-factory data, distribution |

---

## 19. Open questions we must resolve (the site cannot answer them)

1. **First vertical** — which sub-industry? (Engineering/fabrication appears implied.)
2. **Dashboard depth** — exact fields and workflows for each of the 13; which are in MVP?
3. **Production reporting** — is there a dedicated production/output dashboard or is it inside machine/shop floor?
4. **Reminder rules** — who defines expected reports and deadlines; default ladder timings?
5. **Identity** — shared phones, new joiners, contractors without smartphones?
6. **Languages** — are all three required at launch for voice, UI, and replies? Dialect coverage?
7. **Tally** — export only, or two-way? Which Tally version(s)? Which payroll tools?
8. **Deployment** — pure cloud, India region, or customer-hosted for some buyers?
9. **Data policy** — audio retention period, who may listen to recordings, deletion process.
10. **AI vendors** — which STT/LLM/TTS; can customer data be used for training (should be **no**)?
11. **Pricing** — pilot price, treatment of seasonal/contract workers, overage on usage.
12. **Support model** — who provides on-site training and at what cost?
13. **Success metrics** — what must be true after 6 weeks for a customer to convert?

---

## 20. Appendix A — Verbatim product copy (from the site)

- Hero: "Factory software that listens. Say it. It's done." / "Your people speak for 20 seconds. Voxline fills every register, report and dashboard."
- CTAs: "Book a pilot", "Watch it run", "Request a pilot".
- Stats: "20 seconds to report · 13 dashboards · 4 areas of the factory · 1 screen for the owner".
- Section: "One line. Every department." / "Empty software is useless. Voice fills it." / "13 dashboards. 4 areas. Everything a factory runs on. Each one updates when someone speaks."
- Owner: "Ask your factory anything. By voice or text. Voxline answers from today's data, and tells you what is missing."
- "Speak. Don't type." — Form (0/8 fields) vs Voice (0:20 done) — "Marathi, Hindi and English · Works with Tally · On WhatsApp".
- Pricing block as in §14. Pilot: "Start small. Try Voxline in one department for 6 weeks."
- Footer: "Voxline by Athreix · © 2026".

## 21. Appendix B — Site technical notes (useful only for the marketing page)

Static landing page: plain HTML/CSS/JS, GSAP + ScrollTrigger animations, a small 3D line effect, Google Fonts (Bricolage Grotesque, Geist, Geist Mono), reduced-motion support, and a pilot form posting JSON to a Google Apps Script endpoint (spam honeypot included). It tells us how the *brand* is presented, nothing about the product backend.

## 22. Appendix C — Glossary

- **Station** — one of the six steps of the loop in §3.
- **Read-back** — the system repeating its understanding for confirmation before saving.
- **Intent / payload** — the type of report and its structured fields.
- **Expected report** — a report a role must submit by a deadline; its absence triggers the reminder ladder.
- **RLS** — Postgres row-level security for tenant and role isolation.
- **BSP** — WhatsApp Business Solution Provider.
- **DPDP** — India's Digital Personal Data Protection Act, 2023.
