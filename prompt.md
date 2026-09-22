Here's a complete set of prompts, organized in the order you'd use them. Copy each one into Claude (I'd recommend **Claude Code** for the backend and app-building phases, since those involve many files). When a prompt says "attach the prototype," upload the relevant HTML file (`sahayakh-app.html`, `sahayakh-helper-app.html`, `sahayakh-admin-dashboard.html`) alongside it.

---

## PHASE 0 — Planning

**Prompt 1: Product requirements**
```
I'm building Sahayakh, a hyperlocal task/errand marketplace (like a personal
concierge service) for Warangal, India, with three apps: a customer app,
a helper (gig worker) app, and an admin dashboard. I've attached HTML
prototypes for all three. Study them and write a detailed Product
Requirements Document covering: user roles, core user flows, the task
lifecycle (unassigned -> assigned -> on the way -> reached -> in progress
-> completed), OTP-based task verification, pricing/fee model, payment
flow (pay now vs pay after), payouts to helpers, disputes, and admin
controls. Flag anything in the prototypes that is ambiguous and needs a
decision from me.
```

**Prompt 2: Tech stack decision**
```
Based on the PRD, recommend a tech stack for a small team (1-3 developers)
launching an MVP in one city first. I want: a mobile app usable by both
customers and helpers, a web admin dashboard, and a backend API. Compare
React Native vs Flutter for mobile, and recommend a backend
framework/database, real-time layer (for live tracking and requests),
push notifications, and payment gateway suited for India (UPI). Give me
one final recommendation, not just options, and justify it against cost
and my team size.
```

**Prompt 3: Database schema**
```
Design a complete relational database schema (PostgreSQL) for Sahayakh
based on the attached prototypes and PRD. Include tables for users,
helpers, helper_documents, tasks, task_status_history, payments, payouts,
disputes, reviews, and pricing_config. Define columns, types, foreign
keys, indexes, and enums for status fields. Output it as a SQL migration
file plus a short ER diagram description.
```

**Prompt 4: System architecture**
```
Write a system architecture document for Sahayakh: how the customer app,
helper app, admin dashboard, backend API, database, real-time service,
payment gateway, and push notification service connect. Include a
diagram description, the API design style (REST vs GraphQL) with
justification, how live location tracking will work, and how OTP-based
task-start verification will be implemented securely.
```

---

## PHASE 1 — Project setup

**Prompt 5: Repo scaffolding**
```
Set up a monorepo for Sahayakh with this structure: /backend (API server),
/customer-app, /helper-app, /admin-dashboard, /shared (shared types/utils).
Initialize each with the stack we chose, add a root README explaining the
structure and how to run each part locally, set up environment variable
templates (.env.example) for each service, and add a basic CI config that
runs lint and tests on push.
```

---

## PHASE 2 — Backend

**Prompt 6: Auth**
```
Implement phone-number + OTP authentication for the backend. Support two
roles (customer, helper) sharing the same phone-auth flow, plus a
separate email+password login for admin users. Use JWT for session
tokens with refresh tokens. Include rate limiting on OTP requests and
tests for the auth endpoints.
```

**Prompt 7: Core data models**
```
Implement the database models/ORM entities from our schema (users,
helpers, tasks, payments, payouts, disputes, reviews, pricing_config)
using [your chosen ORM]. Add seed data matching the sample data in the
attached prototypes (the same helper names, task types, and pricing) so
I can test against realistic data.
```

**Prompt 8: Task creation & matching**
```
Build the task creation API: a customer submits a task (type, description,
photos, location, date/time, budget). Then build a matching engine that
finds online, verified helpers within a radius who accept that task type,
ranked by distance, rating, or price as the customer chooses — matching
the "Available Helpers" sorting in the attached customer prototype.
```

**Prompt 9: Request/accept/decline flow**
```
Build the helper-side request flow: when a customer confirms a task, send
a request to a helper with a 45-second expiry (matching the helper
prototype). Support accept, decline with reason, and auto-expiry that
requeues the request to the next helper. Use WebSockets or a push
service so this updates in real time on both apps.
```

**Prompt 10: Task lifecycle & OTP verification**
```
Implement the full task lifecycle state machine on the backend:
assigned -> on_the_way -> reached -> otp_pending -> in_progress ->
completed, plus cancelled and disputed. Generate a 6-digit OTP when a
helper reaches the location; only advance to in_progress once the
customer's OTP is verified. Log every status change with a timestamp for
the timeline shown in the admin dashboard.
```

**Prompt 11: Live location tracking**
```
Implement real-time location sharing: the helper app streams GPS location
during on_the_way and in_progress, and the customer app subscribes to
it live, matching the map/tracking screens in the prototype. Use
WebSockets, store only recent location pings (not full history), and
handle reconnects gracefully.
```

**Prompt 12: Payments**
```
Integrate a UPI-based payment gateway (e.g., Razorpay) for India. Support
both "pay after completion" (charge based on final distance/time) and
"pay now" (pre-authorize an estimated amount, then capture the final
amount). Handle payment failures and retries, and expose webhooks to
update task/payment status.
```

**Prompt 13: Fees, payouts & wallet**
```
Implement the fee and payout system: apply the Sahayakh commission
percentage (configurable, matching the Pricing screen in the admin
dashboard prototype) to each completed task, credit the rest to the
helper's wallet, and build withdrawal endpoints (to UPI or bank account)
with a minimum withdrawal amount and pending/paid/failed states matching
the admin Payouts screen.
```

**Prompt 14: Disputes**
```
Build the dispute system: either party can raise a dispute on a
completed task with an issue and message thread. Implement the
admin resolution actions from the admin prototype: full refund, partial
refund, pay helper, warn, or dismiss, and update task/payment state
accordingly.
```

**Prompt 15: Admin APIs**
```
Build the remaining admin APIs: helper application review and document
verification (approve/reject with reason), helper suspension/
reactivation, task reassignment and cancellation, and pricing config
updates — all matching the admin dashboard prototype's actions.
```

**Prompt 16: Notifications**
```
Add push notifications (FCM/APNs) and SMS fallback for key events: new
task request to helper, helper on the way / reached to customer, OTP
sent, task completed, payment received, payout processed, and dispute
updates. Include notification templates for each.
```

---

## PHASE 3 — Customer app

**Prompt 17: Customer app scaffold**
```
Attached is my working HTML/CSS/JS prototype of the customer app
(sahayakh-app.html) with all 11 screens and interactions already
designed. Build this as a production [React Native / Flutter] app,
screen by screen, matching this exact visual design, copy, and
interaction pattern. Start with the Home screen and Personal/Business
mode selection, wired to real navigation (not just prototype state).
```

**Prompt 18: Customer app — task creation to tracking**
```
Continue the customer app: implement Create Task, Finding Helpers,
Available Helpers, Helper Profile, and Confirm Task screens exactly as
in the attached prototype, now connected to the real backend APIs for
task creation, the matching engine, and helper selection.
```

**Prompt 19: Customer app — live tracking to completion**
```
Continue the customer app: implement the live tracking screens (on the
way, OTP entry, in progress), Task Completed with rating, and Task
Details/history, connected to the real-time location API, OTP
verification API, and payment API.
```

---

## PHASE 4 — Helper app

**Prompt 20: Helper app scaffold**
```
Attached is my helper-app HTML prototype (sahayakh-helper-app.html).
Build this as a production [React Native / Flutter] app matching this
design: Home with online/offline toggle and incoming requests, My Tasks,
Earnings, and Profile tabs, wired to real navigation.
```

**Prompt 21: Helper app — task execution flow**
```
Continue the helper app: implement Request review/accept/decline,
navigation to the customer, OTP entry (number pad), the in-progress
checklist with proof photos, and the review/submit/earnings screens,
matching the attached prototype exactly and connected to the real
backend APIs.
```

**Prompt 22: Helper app — earnings & profile**
```
Continue the helper app: implement the Earnings screen (7-day chart,
withdraw flow to UPI/bank) and Profile screen (verification status,
task-type toggles, service radius), connected to the payout and helper
profile APIs.
```

---

## PHASE 5 — Admin dashboard

**Prompt 23: Admin dashboard scaffold**
```
Attached is my admin dashboard HTML prototype (sahayakh-admin-dashboard.html).
Build this as a production React web app matching this design: sidebar
navigation, Overview page with KPIs and live activity feed, connected to
real backend data instead of mock data.
```

**Prompt 24: Admin dashboard — operations screens**
```
Continue the admin dashboard: implement Approvals (document review),
Helpers (list, profile drawer, suspend/reactivate), and Tasks (list,
detail drawer, assign/reassign, cancel), matching the attached prototype
and wired to the admin APIs.
```

**Prompt 25: Admin dashboard — disputes, payouts, pricing**
```
Continue the admin dashboard: implement Disputes (resolution actions),
Payouts (pay now, pay all, retry failed), and Pricing (rate editor with
live fare calculator), matching the attached prototype and wired to the
real backend.
```

---

## PHASE 6 — Quality, deployment, launch

**Prompt 26: Testing**
```
Write an automated test suite for the backend: unit tests for the
matching engine, task state machine, and fee/payout calculations, plus
integration tests for the full task lifecycle from creation to payout.
Also write end-to-end tests for the critical customer and helper flows.
```

**Prompt 27: CI/CD & deployment**
```
Set up CI/CD: run tests and linting on every pull request, and deploy
the backend to [your chosen host], the admin dashboard as a static/SSR
web app, and produce release builds for the customer and helper mobile
apps. Include environment separation for staging and production.
```

**Prompt 28: Security review**
```
Review the backend for security issues: authentication, authorization
(customers can't see other customers' tasks, helpers can't see
unassigned tasks outside their radius, admin routes are protected),
input validation, rate limiting, and safe handling of payment webhooks.
List findings and fix them.
```

**Prompt 29: Launch checklist**
```
Give me a pre-launch checklist for a single-city MVP launch of Sahayakh:
app store submission requirements, minimum helper supply needed, legal/
privacy policy and terms of service, customer support setup, and
monitoring/alerting for backend errors and payment failures.
```

---

**How to use these:** work top to bottom, one prompt per Claude conversation (or per Claude Code session) so context stays focused. After each phase, review the output before moving to the next — especially the schema (Prompt 3) and architecture (Prompt 4), since everything else builds on them.
