# CodificandoAlonso/ReqExec

Títulos candidatos: 1) ReqExec

Área: Ingeniería de Requisitos

Tema: Verificación formal de requisitos mediante agentes de IA.

Síntoma: Los requisitos de un sistema ciberfísico están en texto. Sus incumplimientos y los conflictos entre ellos se descubren tarde, en revisión manual o en integración, y esa revisión no es reproducible.

Causa: El texto de un requisito es ambiguo y no tiene un vínculo ejecutable con un modelo del sistema; además, el recurso más a mano, pedir a un modelo de lenguaje que lo juzgue, no es determinista ni deja una evidencia que otra persona pueda comprobar.

Interés: Detectar incumplimientos y conflictos al escribir los requisitos, y no en integración, evita rehacer diseño y pruebas, y da al ingeniero un veredicto reproducible con evidencia (un plan o un contraejemplo) que se puede auditar.

Hipótesis: Si los requisitos se formalizan con un LLM a una representación tipada aprobada por una persona y se verifican con un planificador PDDL sobre un dominio curado, entonces la exactitud de los veredictos es mayor y reproducible (idéntica en 10 de 10 ejecuciones) (respecto a pedir a un LLM que juzgue directamente esos mismos requisitos, sin verificador simbólico), medida sobre un conjunto dorado de al menos 30 requisitos con incumplimientos y conflictos introducidos.

## Resumen

ReqExec es un sistema que convierte requisitos en lenguaje natural de un sistema ciberfísico en veredictos verificables. Un modelo de lenguaje traduce cada requisito a una representación intermedia tipada, que usa solo el vocabulario de un dominio curado, y una persona la aprueba. A partir de ahí todo es determinista: la representación se compila a PDDL y un planificador decide si el requisito se cumple, se viola, es inalcanzable o no es formalizable. Cada veredicto lleva un plan o un contraejemplo validado, y el sistema señala el subconjunto mínimo de requisitos en conflicto. El caso demostrador es un robot móvil autónomo en una célula de fabricación. La hipótesis se evaluará comparando la exactitud, la reproducibilidad y la detección de conflictos con las de un modelo de lenguaje que juzga sin verificador.

## Introducción

En el ámbito de la ingeniería de requisitos (ÁREA), los requisitos de un sistema ciberfísico se redactan en lenguaje natural y se verifican a mano: sus incumplimientos y los conflictos entre ellos se descubren tarde, en revisión manual o en integración, y esa revisión no es reproducible (SÍNTOMA). La raíz está en que el texto es ambiguo y no tiene un vínculo ejecutable con un modelo del sistema, y en que el recurso más a mano, pedir a un modelo de lenguaje que lo juzgue, no es determinista ni deja una evidencia que otra persona pueda comprobar (CAUSA). Por eso este trabajo se propone desarrollar un sistema que traduzca requisitos en lenguaje natural a una representación tipada aprobada por una persona y los verifique de forma determinista con un planificador PDDL, para detectar con evidencia ejecutable y trazable los incumplimientos y conflictos (OBJETIVO). La hipótesis es que esta combinación da veredictos más exactos y reproducibles que un modelo de lenguaje que juzga directamente los mismos requisitos (HIPÓTESIS).


Resolverlo importa porque un defecto de requisitos corregido al escribirlo suele costar menos que uno que llega a integración y obliga a rehacer diseño y pruebas [ref. pendiente], y porque en dominios con certificación se exige evidencia trazable de que cada requisito se ha comprobado (IMPORTANCIA DE RESOLVER EL PROBLEMA). Si nadie lo resuelve, los conflictos siguen apareciendo en fases tardías, con retrabajo, retrasos y sin evidencia reutilizable para auditar (CONSECUENCIAS DE NO RESOLVER EL PROBLEMA). Resolverlo permite detectar incumplimientos y conflictos al escribir los requisitos, con un veredicto reproducible y un plan o contraejemplo como evidencia, y medir esa mejora con métricas objetivas (BENEFICIOS DE RESOLVER EL PROBLEMA).


Los enfoques existentes se agrupan en tres familias: las plantillas y lenguajes controlados, que formalizan requisitos pero obligan a escribirlos ya en forma restringida; la traducción automática de texto a especificaciones formales o a PDDL con modelos de lenguaje, cuyo resultado suele ser sintácticamente válido pero poco fiable semánticamente [ref. pendiente]; y los asistentes de IA que revisan requisitos sin una verificación formal que respalde su respuesta (TRABAJOS EXISTENTES Y SUS LIMITACIONES). ReqExec propone que el modelo de lenguaje solo rellene una representación tipada sobre un dominio PDDL curado por el equipo, que una persona la apruebe y que un planificador, no el modelo, emita el veredicto con su evidencia (SOLUCIÓN PROPUESTA). La hipótesis se corroborará midiendo, sobre un conjunto dorado de requisitos del caso demostrador: 
- P1, el porcentaje de veredictos que coinciden con el oráculo
- P2, la reproducibilidad del veredicto en 10 ejecuciones
- P3, la sensibilidad y los falsos positivos en la detección de incumplimientos y conflictos introducidos
- P4, el tiempo por verificación (PARÁMETROS PARA CORROBORAR LA HIPÓTESIS).

## Objetivos

**Objetivo general:** Desarrollar un sistema que traduzca requisitos en lenguaje natural de un sistema ciberfísico a una representación tipada aprobada por una persona y los verifique de forma determinista con un planificador PDDL, para detectar, con evidencia ejecutable y trazable, los incumplimientos y conflictos que hoy se descubren tarde y de forma manual.

| ID | Objetivo específico | Resultado en el repositorio | Criterio de aceptación | Traza a |
|---|---|---|---|---|
| OE1 | Analizar y comparar trabajos sobre traducción de lenguaje natural a especificación formal y verificación de requisitos por planificación | | Se comparan ≥ 6 trabajos (≥ 3 artículos científicos y ≥ 1 herramienta abierta o industrial), todos con DOI o URL comprobados; se consultan ≥ 2 bases bibliográficas y se registran la cadena de búsqueda y la fecha; los 3 trabajos más cercanos se han leído completos; la tabla comparativa recoge enfoque, estándares, limitación frente a nuestro problema y referencia | Causa, trabajos existentes |
| OE2 | Diseñar la arquitectura que separa el LLM (no determinista) del núcleo determinista, la representación tipada y el dominio PDDL del caso demostrador | | La arquitectura se documenta con un diagrama que muestra qué cruza la frontera entre ambas zonas; cada decisión de diseño relevante se registra como ADR con contexto, decisión y consecuencias, y cita los requisitos a los que responde; existe un esquema formal de la representación tipada, validable automáticamente, que cubre ≥ 5 patrones de requisito y la categoría «no formalizable»; el dominio PDDL del caso demostrador queda definido, versionado y revisado por todo el equipo | Causa, estándares |
| OE3 | Implementar el flujo requisito → representación tipada → aprobación → PDDL → veredicto con evidencia, con GitHub Copilot SDK, API y web | | Dado un requisito del caso demostrador, el sistema genera la representación, la presenta para su aprobación y, solo si está aprobada, devuelve un veredicto (se cumple, se viola, inalcanzable o no formalizable) con un plan o contraejemplo validado; ante un conjunto incompatible de requisitos señala un subconjunto mínimo en conflicto; el proveedor y el modelo de lenguaje se cambian sin modificar código; un único comando construye, prueba, despliega y ejecuta el sistema en una máquina limpia; las pruebas unitarias y funcionales se ejecutan automáticamente en CI con cobertura ≥ 80 % | Causa, hipótesis, P2 |
| OE4 | Integrar estándares de trazabilidad: exportar cada requisito aprobado a SysML v2 textual y registrar requisito → representación → problema → veredicto → plan en un grafo RDF validado con SHACL | | El SysML v2 generado de cada requisito aprobado es válido según el validador elegido por el equipo; el grafo valida con SHACL sin violaciones; una consulta SPARQL devuelve la cadena completa de trazabilidad de cualquier requisito | Causa, estándares |
| OE5 | Evaluar la hipótesis midiendo P1 a P4 frente a un LLM que juzga sin verificador | | Existe un conjunto dorado de ≥ 30 requisitos (≥ 10 con incumplimiento o conflicto introducido y ≥ 5 no formalizables) con su veredicto esperado fijado antes de ejecutar la evaluación; P1 ≥ 90 % y superior a la referencia; P2 = 10/10; P3: sensibilidad ≥ 90 % y ningún requisito incumplido declarado «se cumple» cuando la representación aprobada es correcta; P4 < 10 s por requisito; el procedimiento y los datos permiten repetir la medición | Hipótesis, P1–P4 |

## Propuesta
## Validación de la propuesta métricas, umbrales, caso de estudio, procedimiento
## Estándares implicados
## Declaración de uso de IA
## Referencias
