# Sprint Plan: Medicare Communication Compliance App

## Sprint 0: Setup & Foundations (2 weeks)
**Goals**
- Establish development environment in Windsurf.
- Define core architecture and data model.
- Build scaffolding for backend, frontend, and integrations.

**Tasks**
- [ ] Provision project in Windsurf (monorepo setup).
- [ ] Implement Postgres schema (from data model).
- [ ] Configure auth (JWT/session-based, role-based access).
- [ ] Setup object storage for attachments (e.g., S3).
- [ ] Define CMS guideline ingestion pipeline (static JSON for MVP).
- [ ] CI/CD setup for deployments.

---

## Sprint 1: Communication Submission (2 weeks)
**Goals**
- Enable staff to create and submit communications.
- Capture metadata and store versions.

**Tasks**
- [ ] Build "Create Communication" UI (title, type, audience, channel).
- [ ] Support uploads (PDF, DOCX, text).
- [ ] Extract normalized text for screening.
- [ ] Store submission in `communications` + `communication_versions`.
- [ ] Display list of past submissions.

---

## Sprint 2: Automated Screening Engine (2 weeks)
**Goals**
- Implement compliance rule engine.
- Provide immediate feedback to users.

**Tasks**
- [ ] Implement ruleset runner (pattern + phrase_list).
- [ ] Integrate with CMS guideline JSON (Sprint 0 pipeline).
- [ ] Highlight flagged issues in UI with severity levels.
- [ ] Check for required disclaimers (e.g., missing fields).
- [ ] Save results in `screening_results` + `rule_matches`.

---

## Sprint 3: Audit Trail & Reporting (2 weeks)
**Goals**
- Ensure every action is logged.
- Provide compliance admins with search and export.

**Tasks**
- [ ] Implement `audit_events` on create, update, screen actions.
- [ ] Build searchable audit trail view (filters: type, date, user).
- [ ] Export results to CSV/PDF (screening summaries).
- [ ] Immutable versioning for communications.

---

## Sprint 4: Event & Outreach Support (2 weeks)
**Goals**
- Handle CMS event-specific requirements.
- Provide compliant templates for flyers.

**Tasks**
- [ ] Add `event_details` model (educational vs. marketing).
- [ ] Build event submission form.
- [ ] Validate fields (location, date, disclaimers).
- [ ] Provide CMS-compliant flyer templates (locked text + editable fields).
- [ ] Apply disclaimer auto-insertion logic.

---

## Sprint 5: User & Role Management (2 weeks)
**Goals**
- Add secure role-based access control.

**Tasks**
- [ ] Implement user invitations (admin adds staff).
- [ ] Assign roles (admin, author, viewer).
- [ ] Enforce role-based permissions at API and UI layers.
- [ ] Build basic org switcher (for multi-org deployments).
- [ ] Log user actions in audit trail.

---

## Sprint 6: Reporting & Dashboard (Phase 2, 2 weeks)
**Goals**
- Provide compliance insights to admins.

**Tasks**
- [ ] Build dashboard: risk level distribution (pie chart).
- [ ] Add trendline view of violations over time.
- [ ] List most common rule violations.
- [ ] Breakdown by user activity.
- [ ] Export analytics snapshots to PDF.

---

## Sprint 7: Future Enhancements (Phase 3, 2 weeks)
**Goals**
- Improve usability with AI and localization.

**Tasks**
- [ ] AI-powered rewrite suggestions for flagged text.
- [ ] Store and manage CMS-approved templates.
- [ ] Add Spanish disclaimer library.
- [ ] Extend screening rules for multilingual support.
- [ ] Build compliance trend heatmap view.

---

## Release Milestones

- **MVP (Sprints 0–3, ~6 weeks)**  
  - Communication submission  
  - Automated screening  
  - Audit trail & export  

- **Phase 2 (Sprints 4–6, ~6 weeks)**  
  - Event & outreach compliance  
  - User & role management  
  - Reporting dashboards  

- **Phase 3 (Sprint 7+, ongoing)**  
  - AI-powered rewriting  
  - Multi-language support  
  - Advanced analytics  

---
