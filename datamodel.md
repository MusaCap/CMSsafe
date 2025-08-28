# Data Model: Medicare Communication Compliance App

## Entities

### Organizations
| Field       | Type       | Constraints                     | Notes |
|-------------|------------|---------------------------------|-------|
| id          | UUID       | PK                              | Unique identifier |
| name        | Text       | Unique                          | Organization name |
| created_at  | Timestamptz| Not null                        | Record creation timestamp |
| updated_at  | Timestamptz| Not null                        | Last update timestamp |

---

### Users
| Field        | Type        | Constraints                     | Notes |
|--------------|-------------|---------------------------------|-------|
| id           | UUID        | PK                              | Unique identifier |
| org_id       | UUID        | FK → organizations.id           | User belongs to one org |
| email        | Citext      | Unique within org               | Case-insensitive |
| display_name | Text        |                                 | User full name |
| role         | Enum        | admin, author, viewer           | Role in system |
| status       | Enum        | active, disabled                | Account state |
| created_at   | Timestamptz | Not null                        | Created timestamp |
| updated_at   | Timestamptz | Not null                        | Updated timestamp |

---

### Communications
| Field              | Type        | Constraints                     | Notes |
|--------------------|-------------|---------------------------------|-------|
| id                 | UUID        | PK                              | Communication container |
| org_id             | UUID        | FK → organizations.id           | Belongs to org |
| created_by         | UUID        | FK → users.id                   | Creator |
| title              | Text        |                                 | Human-readable title |
| type               | Enum        | event, written, verbal_script, email, sms, other | Communication type |
| target_audience    | Text        |                                 | Who it’s for |
| distribution_channel| Enum       | print, email, sms, web, social, phone, in_person | Delivery method |
| status             | Enum        | draft, ready                    | State of communication |
| latest_version_id  | UUID        | FK → communication_versions.id  | Points to latest version |
| tags               | Text[]      |                                 | Search tags |
| created_at         | Timestamptz | Not null                        | Created timestamp |
| updated_at         | Timestamptz | Not null                        | Updated timestamp |

---

### Communication Versions
| Field          | Type        | Constraints                     | Notes |
|----------------|-------------|---------------------------------|-------|
| id             | UUID        | PK                              | Unique identifier |
| communication_id| UUID       | FK → communications.id          | Parent communication |
| version_number | Int         |                                 | Incremental version |
| content_type   | Enum        | text, html, markdown, docx, pdf | Format |
| content_text   | Text        |                                 | Extracted text |
| content_blob_url| Text       |                                 | Pointer to file storage |
| submitted_by   | UUID        | FK → users.id                   | Who submitted |
| submitted_at   | Timestamptz | Not null                        | Submission time |
| hash           | Text        |                                 | SHA-256 checksum |

---

### Screening Results
| Field                 | Type        | Constraints                     | Notes |
|-----------------------|-------------|---------------------------------|-------|
| id                    | UUID        | PK                              | Unique identifier |
| version_id            | UUID        | FK → communication_versions.id  | Which version was screened |
| engine_version        | Text        |                                 | Internal screening engine |
| cms_guideline_version | Text        |                                 | CMS guideline version |
| overall_risk          | Enum        | none, low, medium, high, critical | Screening risk level |
| missing_required_disclaimer | Bool  |                                 | True if disclaimer missing |
| blocked_terms_found   | Bool        |                                 | True if bad terms found |
| required_actions      | JSONB[]     |                                 | Machine-readable actions |
| summary               | Text        |                                 | Human notes |
| created_at            | Timestamptz | Not null                        | Created timestamp |

---

### Rule Matches
| Field          | Type        | Constraints                     | Notes |
|----------------|-------------|---------------------------------|-------|
| id             | UUID        | PK                              | Unique identifier |
| screening_id   | UUID        | FK → screening_results.id       | Parent screening |
| rule_version_id| UUID        | FK → rule_versions.id           | Rule applied |
| severity       | Enum        | info, minor, major, critical    | Level of issue |
| start_offset   | Int         |                                 | Start in text |
| end_offset     | Int         |                                 | End in text |
| excerpt        | Text        |                                 | Snippet of content |
| explanation    | Text        |                                 | Why it was flagged |

---

### Rules
| Field       | Type        | Constraints                     | Notes |
|-------------|-------------|---------------------------------|-------|
| id          | UUID        | PK                              | Unique identifier |
| code        | Text        | Unique                          | Stable CMS code |
| category    | Enum        | disclaimer, prohibited_terms, formatting, event_policy, benefit_comparison, contact_policy, other | Rule category |
| title       | Text        |                                 | Rule title |
| created_at  | Timestamptz | Not null                        | Created timestamp |
| updated_at  | Timestamptz | Not null                        | Updated timestamp |

---

### Rule Versions
| Field        | Type        | Constraints                     | Notes |
|--------------|-------------|---------------------------------|-------|
| id           | UUID        | PK                              | Unique identifier |
| rule_id      | UUID        | FK → rules.id                   | Parent rule |
| effective_from| Date       |                                 | Start date |
| effective_to | Date        | Nullable                        | End date |
| cms_reference| Text        |                                 | CMS section reference |
| logic_kind   | Enum        | pattern, phrase_list, dsl, classifier | Rule logic type |
| logic_payload| JSONB       |                                 | Logic details |
| default_severity | Enum    | info, minor, major, critical    | Default severity |
| required_disclaimer_code | Text | Nullable                   | Ties to disclaimers |
| created_at   | Timestamptz | Not null                        | Created timestamp |

---

### Disclaimers
| Field       | Type        | Constraints                     | Notes |
|-------------|-------------|---------------------------------|-------|
| id          | UUID        | PK                              | Unique identifier |
| code        | Text        | Unique                          | Stable disclaimer code |
| locale      | Text        |                                 | Language/locale |
| text        | Text        |                                 | Disclaimer text |
| applies_to_types | Text[] |                                 | Communication types |
| created_at  | Timestamptz | Not null                        | Created timestamp |
| updated_at  | Timestamptz | Not null                        | Updated timestamp |

---

### Version Required Disclaimers
| Field        | Type        | Constraints                     | Notes |
|--------------|-------------|---------------------------------|-------|
| version_id   | UUID        | FK → communication_versions.id  | Communication version |
| disclaimer_id| UUID        | FK → disclaimers.id             | Required disclaimer |
| source       | Enum        | rule, template, manual          | Why required |
| reason       | Text        |                                 | Notes |

**PK**: (version_id, disclaimer_id)

---

### Event Details
| Field        | Type        | Constraints                     | Notes |
|--------------|-------------|---------------------------------|-------|
| id           | UUID        | PK                              | Unique identifier |
| communication_id| UUID     | FK → communications.id          | Parent |
| event_kind   | Enum        | educational, marketing          | Event classification |
| location_name| Text        |                                 | Venue |
| address      | Text        |                                 | Address |
| start_at     | Timestamptz |                                 | Start time |
| end_at       | Timestamptz |                                 | End time |
| rsvp_required| Bool        |                                 | RSVP required |
| contact_phone| Text        |                                 | Contact number |
| virtual_join_url| Text     |                                 | Online link |

---

### Attachments
| Field       | Type        | Constraints                     | Notes |
|-------------|-------------|---------------------------------|-------|
| id          | UUID        | PK                              | Unique identifier |
| version_id  | UUID        | FK → communication_versions.id  | Attached to version |
| filename    | Text        |                                 | File name |
| mime_type   | Text        |                                 | File type |
| blob_url    | Text        |                                 | Storage link |
| size_bytes  | Bigint      |                                 | Size in bytes |
| uploaded_at | Timestamptz | Not null                        | Upload time |

---

### Audit Events
| Field        | Type        | Constraints                     | Notes |
|--------------|-------------|---------------------------------|-------|
| id           | UUID        | PK                              | Unique identifier |
| org_id       | UUID        | FK → organizations.id           | Belongs to org |
| actor_user_id| UUID        | FK → users.id, nullable         | Who triggered |
| entity_type  | Enum        | communication, communication_version, screening_result, rule, rule_version, disclaimer, user | Entity type |
| entity_id    | UUID        |                                 | Entity affected |
| action       | Enum        | create, update, screen, auto_fix, export, login, permission_change | What happened |
| metadata     | JSONB       |                                 | Extra info |
| created_at   | Timestamptz | Not null                        | Timestamp |

---

## Enumerations

### User Roles
- admin  
- author  
- viewer  

### Communication Types
- event  
- written  
- verbal_script  
- email  
- sms  
- other  

### Distribution Channels
- print  
- email  
- sms  
- web  
- social  
- phone  
- in_person  

### Communication Status
- draft  
- ready  

### Risk Levels
- none  
- low  
- medium  
- high  
- critical  

### Severity
- info  
- minor  
- major  
- critical  

### Rule Categories
- disclaimer  
- prohibited_terms  
- formatting  
- event_policy  
- benefit_comparison  
- contact_policy  
- other  

### Logic Kind
- pattern  
- phrase_list  
- dsl  
- classifier  

### Disclaimer Source
- rule  
- template  
- manual  

### Event Kind
- educational  
- marketing  

### Entity Types (for audit)
- communication  
- communication_version  
- screening_result  
- rule  
- rule_version  
- disclaimer  
- user  

### Audit Actions
- create  
- update  
- screen  
- auto_fix  
- export  
- login  
- permission_change  
