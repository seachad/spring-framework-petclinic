# AECF Prompts — IMPLEMENT CHECKLIST

LAST_REVIEW: 2026-03-09

---

> Checklist simplificado para la fase IMPLEMENT. Evaluar cada ítem con 0 / 1 / 2 según `scoring/SCORING_MODEL.md`.

---

## 1. Scope Validation (Peso: 2)

- [ ] El código implementado coincide con el PLAN aprobado
- [ ] No hay expansión de scope
- [ ] No hay rediseño implícito no autorizado

## 2. Security Controls (Peso: 3)

- [ ] No se exponen datos sensibles
- [ ] Control de acceso implementado
- [ ] Mitigación de enumeración presente
- [ ] Logging cubre eventos de seguridad

## 3. Resource Management (Peso: 2)

- [ ] No hay recursos abiertos sin cerrar
- [ ] Se usan context managers / try-finally

## 4. Dependency Outage Resilience (Peso: 3)

- [ ] Llamadas externas tienen timeouts explícitos
- [ ] Reintentos acotados con backoff (+ jitter si aplica)
- [ ] Protección fail-fast ante fallos persistentes
- [ ] Errores al usuario reflejan fallo de dependencia (no engañosos)

## 5. Logging & Observability (Peso: 2)

- [ ] No se usa print() para logging
- [ ] Logging estructurado implementado
- [ ] Errores loggeados correctamente

## 6. Compliance with Previous Phase (Peso: 3)

- [ ] PLAN aprobado (AUDIT_PLAN = GO)
- [ ] Veredicto de auditoría respetado
- [ ] No hay violación de secuencia de fases

## 7. Production Readiness (Peso: 2)

- [ ] Casos borde considerados
- [ ] Error handling completo
- [ ] No hay fallos silenciosos
- [ ] No hay efectos secundarios ocultos

## 8. Decision Integrity (Peso: 3)

- [ ] No hay decisiones no autorizadas
- [ ] Todas las decisiones son trazables al PLAN

## 9. Implementation Integrity (Peso: 2)

- [ ] El código coincide literalmente con el PLAN
- [ ] No hay features adicionales no planificadas
- [ ] Todos los pasos del PLAN están implementados

---

## SCORING TABLE

| Categoría | Peso | Ítems | Puntuación (0-2 cada uno) | Total |
|---|---|---|---|---|
| Scope Validation | 2 | 3 | _ + _ + _ = __ | __ × 2 = __ |
| Security Controls | 3 | 4 | _ + _ + _ + _ = __ | __ × 3 = __ |
| Resource Management | 2 | 2 | _ + _ = __ | __ × 2 = __ |
| Dep. Outage Resilience | 3 | 4 | _ + _ + _ + _ = __ | __ × 3 = __ |
| Logging & Observability | 2 | 3 | _ + _ + _ = __ | __ × 2 = __ |
| Compliance w/ Previous | 3 | 3 | _ + _ + _ = __ | __ × 3 = __ |
| Production Readiness | 2 | 4 | _ + _ + _ + _ = __ | __ × 2 = __ |
| Decision Integrity | 3 | 2 | _ + _ = __ | __ × 3 = __ |
| Implementation Integrity | 2 | 3 | _ + _ + _ = __ | __ × 2 = __ |
| **TOTAL** | | **28** | | **__ / 132** |

```
Score = (Total / 132) × 100 = ___%
```

## VEREDICTO

- [ ] Score ≥ 75 → Implementación aceptable, pasar a AUDIT_CODE
- [ ] Score < 75 → Revisar implementación
- [ ] Hallazgo CRITICAL → NO continuar

> ⚠️ IMPLEMENT no tiene gate GO/NO-GO formal. El gate real es AUDIT_CODE. Pero un score bajo aquí indica riesgo alto de fallo en la auditoría.
