# CRBS-Group03 — Campus Room & Equipment Booking System

Group 03 term project, course **CSE703095 – Software Requirements** (Phenikaa University, 2026–2027).

| Member | Role |
|---|---|
| Mai Anh Tú | Team Leader / Lead BA |
| Nguyễn Đức Tính | System Analyst / QA |
| Nguyễn Tiến Việt | Business Analyst |
| Lê Văn Hoàn | Requirements Analyst |

## What this repository contains

CRBS lets Students and Lecturers search rooms and equipment, submit a room-plus-equipment booking as **one request**, obtain Lecturer endorsement for supervised practice, have Facility Staff approve or reject it, check equipment out and in, and record maintenance incidents. Role-based access and an audit trail apply throughout.

> **Scope note.** `src/crbs.py` is an **educational Python 3.10+ / SQLite prototype** built to demonstrate and test the specified business rules. External SSO, e-mail/SMS and inventory systems are simulated. It is **not** a deployed production system, and the automated tests below are evidence about this prototype only.

```
CRBS-Group03/
├── src/crbs.py                    # prototype implementation (standard library only)
├── tests/
│   ├── test_campus_booking.py     # 7 data-driven acceptance tests (TC-01, 02, 03, 05, 06, 11, 26)
│   ├── test_crbs.py               # 40 regression tests (PT-01 … PT-40)
│   └── test_catalog_contract.py   # 7 structural checks of the 42-case test catalogue
├── test_data/                     # test inputs: acceptance_test_data.json + 42-case catalogue (CSV/JSON)
├── scripts/generate_test_data.py  # regenerates acceptance_test_data.json from the workbook + catalogue
├── CRBS_Requirements_Master.xlsx  # requirements, use cases, rules, hazards, test cases, traceability
└── docs/
    ├── TEST_CODE_EXPLANATION_VI.md  # explanation of each test (Vietnamese)
    ├── models/                      # UML/state/hazard diagrams (PNG + .dot/.puml sources)
    └── test_record/                 # traceability map, business rules, state rules, change log
```

## Run it

```bash
python --version            # 3.10 or newer
pip install -r requirements.txt
python -m pytest -q         # expected: 54 passed
python src/crbs.py          # built-in demo
```

Last verified run (26 Oct 2026): `54 passed`.

## Traceability

Overall scenario → child scenario (SC-01…SC-04) → requirement / use case / state or business rule → hazard (where applicable) → test case → test-data ID → Python test → execution log. See `docs/test_record/CRBS_Overall_Scenario_Traceability.md`.

## Evidence limits

42 test cases are designed; 7 have executable acceptance code and 40 regression checks run against the prototype. Cases not executed against a target deployment (performance, live concurrency, production security) remain **Planned** and are not reported as Passed. Some business policies (booking lead times, cancellation boundary, supervisor timeout) are draft assumptions awaiting instructor confirmation.
