# AECF — AUDIT_CODE: {{TOPIC}}

## METADATA

| Field | Value |
| --- | --- |
| Skill | {{skill}} |
| Phase | AUDIT_CODE |
| Topic | {{TOPIC}} |
| Date | {{fecha}} |
| Code audited | <DOCS_ROOT>/<user_id>/{{TOPIC}}/AECF_05_IMPLEMENT.md |

## 1. Scope Compliance

| Aspect | Result |
| --- | --- |
| Does code match the PLAN? | ✅ / ❌ |
| Scope expansion? | ✅ / ❌ |
| Unplanned features? | ✅ / ❌ |

## 2. Security Audit

| Control | Status | Observation |
| --- | --- | --- |
| Input validation | ✅ / ❌ | |
| Access control | ✅ / ❌ | |
| Data exposure | ✅ / ❌ | |
| Enumeration mitigation | ✅ / ❌ | |
| Security logging | ✅ / ❌ | |

## 3. Resource Management

| Aspect | Status |
| --- | --- |
| Resources closed correctly | ✅ / ❌ |
| Context managers used | ✅ / ❌ |
| Timeouts on external calls | ✅ / ❌ |

## 4. Code Quality

| Aspect | Status | Observation |
| --- | --- | --- |
| Error handling | ✅ / ❌ | |
| Logging (no print()) | ✅ / ❌ | |
| Edge cases | ✅ / ❌ | |
| Side effects | ✅ / ❌ | |

## 5. Testing Evidence

| Aspect | Status |
| --- | --- |
| Tests executed | ✅ / ❌ |
| Evidence (output) | ✅ / ❌ |
| Tests passing | ✅ / ❌ |
| Coverage measured | ✅ / ❌ / Blocker: ___ |

## 6. Findings

| ID | Severity | Category | Description | Recommendation |
| --- | --- | --- | --- | --- |
| F1 | CRITICAL / WARNING / INFO | | | |

## AECF_SCORE_REPORT

| Category | Weight | Score | Weighted |
| --- | --- | --- | --- |
| Scope Validation | 2 | /6 | |
| Security Controls | 3 | /8 | |
| Resource Management | 2 | /4 | |
| Dep. Outage Resilience | 3 | /10 | |
| Logging & Observability | 2 | /6 | |
| Compliance w/ Previous | 3 | /6 | |
| Production Readiness | 2 | /8 | |
| Decision Integrity | 3 | /4 | |
| Code Audit Integrity | 2 | /6 | |
| Testing Evidence | 3 | /6 | |
| **TOTAL** | | | **__ / 156** |

**Score**: ___% | **Level**: ___ | **Verdict**: GO / NO-GO

## AECF_COMPLIANCE_REPORT

- [ ] Checklist `checklists/AUDIT_CODE_CHECKLIST.md` applied
- [ ] Scoring calculated according to `scoring/SCORING_MODEL.md`
- [ ] Findings classified by severity
- [ ] Testing evidence verified
- [ ] Verdict justified with evidence
- [ ] Missing testing evidence -> automatic NO-GO applied

