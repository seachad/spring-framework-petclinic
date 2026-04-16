# 10 Escenarios AECF para Curso con PetClinic

> Base: Spring Framework PetClinic — Spring 7, Java 17, Jakarta EE, 3 capas (Controller → Service → Repository), 3 implementaciones de persistencia intercambiables.

---

## Escenario 1 — `explain_behaviour`: Las 3 estrategias de persistencia y el cambio de perfil

**¿Por qué es interesante?**
Este proyecto incluye tres implementaciones completas del mismo contrato de repositorio: JPA pura con `EntityManager`, JDBC con `JdbcClient`, y Spring Data JPA. Cambiar de una a otra es solo pasar `-Dspring.profiles.active=jdbc`. Un desarrollador que llegue al código frío no entiende por qué existen tres versiones ni cómo funcionan los perfiles.

**¿Qué demuestra?**
- La capacidad de Claude para razonar sobre arquitectura multi-implementación
- Cómo leer XML de configuración y trazar el wiring de beans con perfil condicional
- Que Claude puede comparar implementaciones equivalentes y señalar diferencias de comportamiento

**¿Qué deberíamos ver?**
Claude debe identificar los tres paquetes (`jpa/`, `jdbc/`, `springdatajpa/`), leer `business-config.xml`, explicar cómo Spring activa los beans según perfil, y producir una tabla comparativa de los tres enfoques con sus trade-offs de rendimiento.

**Prompt de partida sugerido:**
> "Explícame cómo funciona el sistema de repositorios de este proyecto. ¿Por qué hay tres implementaciones y cómo se activa cada una?"

---

## Escenario 2 — `new_feature`: Paginación en el listado de propietarios

**¿Por qué es interesante?**
`OwnerController.processFindForm()` devuelve TODOS los owners que coincidan con el apellido buscado. Si hay miles de registros, la consulta devuelve todo. Es un bug de rendimiento realista y la feature de paginación es clásica en aplicaciones Spring MVC.

**¿Qué demuestra?**
- Cómo Claude navega del controlador al repositorio para entender el flujo completo antes de proponer cambios
- Que debe modificar la capa de servicio, el repositorio y la vista JSP de forma coherente
- Gestión del estado de paginación en una petición GET stateless

**¿Qué deberíamos ver?**
Claude debe proponer cambios en `OwnerRepository` (añadir `Pageable`), `ClinicService`, `OwnerController`, y la vista `ownersList.jsp` para renderizar los controles de página. Debería también proponer un test de controlador.

**Prompt de partida sugerido:**
> "Añade paginación al listado de búsqueda de owners. Quiero mostrar 5 resultados por página con controles de siguiente/anterior."

---

## Escenario 3 — `refactor`: Eliminar el acoplamiento de EAGER loading

**¿Por qué es interesante?**
`Pet.visits` está mapeado como `FetchType.EAGER`. El propio código fuente incluye un comentario: `"There is a bug in this implementation. It would be more correct to use LAZY loading here, but EAGER is used to avoid [LazyInitializationException]"`. Es un caso clásico de deuda técnica documentada.

**¿Qué demuestra?**
- Refactor guiado por evidencia: Claude debe leer el comentario existente, entender las implicaciones y proponer la solución correcta (sesión transaccional abierta o DTOs)
- Razonamiento sobre side effects: cambiar EAGER a LAZY puede romper otros puntos de carga
- Búsqueda de todos los puntos de uso antes de modificar

**¿Qué deberíamos ver?**
Claude debe encontrar el `@OneToMany(fetch = FetchType.EAGER)` en `Pet.java`, rastrear todos los puntos donde se accede a `pet.getVisits()` fuera de contexto transaccional, y proponer la solución (inicializar dentro de una transacción activa o usar `@Transactional` correctamente en el servicio).

**Prompt de partida sugerido:**
> "Hay un comentario en el código que dice que se usa EAGER loading como workaround. Analiza el problema y propón un refactor correcto."

---

## Escenario 4 — `document_legacy`: Documentar la configuración XML

**¿Por qué es interesante?**
`business-config.xml`, `mvc-core-config.xml`, `datasource-config.xml` y `tools-config.xml` son configuración Spring estilo pre-Boot. Para un desarrollador moderno que solo conoce `@SpringBootApplication` y `application.yml`, este XML es opaco.

**¿Qué demuestra?**
- La capacidad de Claude para leer XML de configuración Spring (namespaces, beans, imports, perfiles)
- Generación de documentación técnica precisa desde fuentes primarias
- Traducción entre paradigmas (XML vs. Java Config vs. Boot autoconfiguration)

**¿Qué deberíamos ver?**
Claude debe producir un documento que explique qué hace cada archivo XML, qué beans registra, cómo se relacionan entre sí, y —opcionalmente— su equivalente en Spring Boot si se quisiera migrar.

**Prompt de partida sugerido:**
> "Documenta los archivos de configuración XML de `src/main/resources/spring/`. Explica qué hace cada uno y cómo se relacionan."

---

## Escenario 5 — `test_coverage`: Cubrir el repositorio JDBC

**¿Por qué es interesante?**
`ClinicServiceJdbcTests` hereda de `AbstractClinicServiceTests` y se ejecuta con perfil `jdbc`, pero los tests son genéricos. Los detalles específicos de JDBC —como el `SimpleJdbcInsert`, el `RowMapper` personalizado o el manejo de tipos— no están probados de forma aislada.

**¿Qué demuestra?**
- Que Claude puede analizar cobertura de tests existentes y detectar gaps
- Generación de tests orientados a la implementación, no solo al contrato
- Uso correcto de bases de datos embebidas (H2) para tests de integración

**¿Qué deberíamos ver?**
Claude debe leer `JdbcOwnerRepositoryImpl`, identificar lógica específica no cubierta (ej: el extractor de ResultSet para owners con múltiples pets), y generar tests que verifiquen el comportamiento del mapeo de datos.

**Prompt de partida sugerido:**
> "Analiza la cobertura de tests del repositorio JDBC. ¿Qué casos importantes no están cubiertos? Escribe los tests que faltan."

---

## Escenario 6 — `explain_behaviour`: El aspecto de monitorización y su punto ciego

**¿Por qué es interesante?**
`CallMonitoringAspect` usa AOP para contar llamadas y medir tiempos en repositorios. Pero hay un comentario en el código: los repositorios de Spring Data JPA son proxies de interfaz y el aspecto no los intercepta correctamente. Es un bug arquitectural con causa técnica profunda.

**¿Qué demuestra?**
- Razonamiento sobre AOP, proxies dinámicos y la jerarquía de proxies de Spring
- Capacidad de Claude para leer el comentario de advertencia, reproducir el problema conceptualmente y explicar por qué ocurre
- Proponer una solución (ej: AspectJ weaving, cambiar el pointcut, usar Spring Data listeners)

**¿Qué deberíamos ver?**
Claude debe leer `CallMonitoringAspect.java`, explicar el mecanismo de proxy de Spring Data JPA, y describir por qué el pointcut `within(org.springframework.samples.petclinic.repository..*)` no captura los beans generados dinámicamente.

**Prompt de partida sugerido:**
> "El aspecto de monitorización no funciona con Spring Data JPA. Explica por qué ocurre esto y cómo se podría solucionar."

---

## Escenario 7 — `new_feature`: Endpoint REST para vets con OpenAPI

**¿Por qué es interesante?**
`VetController` ya devuelve JSON y XML via content negotiation sobre `GET /vets`. Es un punto de partida para añadir una API REST completa documentada con OpenAPI/Swagger. La feature es concreta, acotada y demuestra bien el razonamiento sobre capas.

**¿Qué demuestra?**
- Cómo Claude amplía un endpoint existente sin romper el comportamiento HTML
- Añadir dependencias Maven, configurar SpringDoc/OpenAPI, y anotar el controlador
- Que Claude distingue entre content negotiation para HTML y una API REST dedicada

**¿Qué deberíamos ver?**
Claude debe añadir la dependencia de SpringDoc OpenAPI a `pom.xml`, crear un `VetRestController` (o enriquecer el existente), añadir anotaciones `@Operation`/`@ApiResponse`, y verificar que el endpoint `/v3/api-docs` funciona.

**Prompt de partida sugerido:**
> "Añade documentación OpenAPI al endpoint de vets. Quiero poder ver el swagger-ui con los endpoints disponibles."

---

## Escenario 8 — `security_review`: Revisar la seguridad del controlador de propietarios

**¿Por qué es interesante?**
`OwnerController` recibe parámetros de formulario directamente en objetos de dominio. Aunque usa `@InitBinder` para bloquear el campo `id`, hay otras superficies de ataque: mass assignment de otros campos, ausencia de CSRF explícito, y una ruta `/oups` que lanza una excepción intencionada.

**¿Qué demuestra?**
- Análisis de seguridad sobre código real, no ejemplos artificiales
- Claude identificando mecanismos de protección existentes y sus gaps
- Propuestas concretas de mejora con su justificación técnica

**¿Qué deberíamos ver?**
Claude debe revisar `OwnerController`, `PetController`, `PetclinicInitializer`, identificar la ausencia de Spring Security, el `@InitBinder`, la validación con `@NotEmpty`/`@Digits`, y generar un informe con vulnerabilidades potenciales y recomendaciones priorizadas.

**Prompt de partida sugerido:**
> "Haz una revisión de seguridad de los controladores web. ¿Qué riesgos existen y cómo se mitigarían?"

---

## Escenario 9 — `refactor`: Migrar configuración XML a Java Config

**¿Por qué es interesante?**
Todo el wiring de beans está en XML (`business-config.xml`, etc.). Es un refactor de modernización puro: convertir a `@Configuration` classes sin cambiar el comportamiento observable. Es exactamente el tipo de tarea que una IA puede hacer de forma sistemática si entiende bien la semántica del XML.

**¿Qué demuestra?**
- Que Claude puede transformar XML Spring a Java Config de forma mecánica pero correcta
- Manejo de casos no triviales: `<context:component-scan>`, `<aop:aspectj-autoproxy>`, `<jdbc:initialize-database>`, y los tres perfiles de persistencia
- Verificación de que los tests siguen pasando tras el refactor

**¿Qué deberíamos ver?**
Claude debe producir clases `@Configuration` equivalentes para cada XML, mantener los perfiles `@Profile("jpa")`, `@Profile("jdbc")` etc., y proponer cómo eliminar los XML del classpath de forma gradual y segura.

**Prompt de partida sugerido:**
> "Quiero migrar la configuración de XML a Java Config. Empieza por `business-config.xml` y asegúrate de no romper los tests."

---

## Escenario 10 — `new_feature` + `explain_behaviour`: Internacionalización y detección de locale

**¿Por qué es interesante?**
El proyecto tiene ficheros `messages_es.properties`, `messages_de.properties`, `messages_fr.properties` pero no hay ningún mecanismo en la UI para que el usuario cambie de idioma. Los mensajes de validación están parcialmente internacionalizados. Es un feature incompleto y real.

**¿Qué demuestra?**
- Análisis de una feature a medio implementar: Claude debe descubrir el estado actual antes de proponer qué falta
- Añadir un `LocaleChangeInterceptor` y un selector de idioma en la cabecera de la aplicación
- Entender cómo Spring MVC resuelve mensajes y cómo los mensajes de validación JSR-303 se localizan

**¿Qué deberíamos ver?**
Claude debe leer `mvc-core-config.xml` para ver si hay `LocaleResolver` configurado, revisar los ficheros de mensajes para detectar gaps entre idiomas, y proponer añadir el interceptor de cambio de locale + un selector en el layout JSP.

**Prompt de partida sugerido:**
> "El proyecto tiene ficheros de mensajes en varios idiomas pero no hay forma de cambiar el idioma desde la UI. Analiza el estado actual y añade el soporte completo."

---

## Resumen de cobertura de skills AECF

| # | Escenario | Tipo | Complejidad | Skills AECF cubiertos |
|---|-----------|------|-------------|----------------------|
| 1 | 3 estrategias de persistencia | `explain_behaviour` | Media | Exploration, Architecture reasoning |
| 2 | Paginación en owners | `new_feature` | Media | Multi-layer change, View modification |
| 3 | Refactor EAGER loading | `refactor` | Alta | Bug analysis, Impact tracing |
| 4 | Documentar XML config | `document_legacy` | Baja | Legacy reading, Doc generation |
| 5 | Cobertura tests JDBC | `test_coverage` | Media | Gap analysis, Test generation |
| 6 | AOP y punto ciego | `explain_behaviour` | Alta | Proxy internals, AOP reasoning |
| 7 | REST + OpenAPI | `new_feature` | Media | Dependency mgmt, API design |
| 8 | Security review | `security_review` | Media | Threat modeling, Code review |
| 9 | XML → Java Config | `refactor` | Alta | Mechanical transformation, Verification |
| 10 | i18n completa | `new_feature` | Media | Feature gap analysis, UI + backend |

---

## Notas pedagógicas

- **Escenarios 1, 4, 6** son ideales para mostrar la capacidad de Claude de **leer y explicar** código que nadie documenta.
- **Escenarios 2, 7, 10** muestran **new_feature con Claude guiando el análisis previo** antes de escribir código.
- **Escenarios 3, 9** son los más exigentes: refactors donde **el riesgo de romper cosas es real** y Claude debe trazar impactos.
- **Escenario 5** es ideal para discutir **qué tests tienen valor real** vs. tests que solo suben el coverage.
- **Escenario 8** introduce el **rol de Claude como revisor de seguridad**, útil para mostrar sus límites y aciertos.
