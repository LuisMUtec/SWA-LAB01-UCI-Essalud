# Caso de Estudio #1 — Sistema de Gestión UCI (Essalud)

Arquitectura de Software · UTEC · 2026-II · **20 ptos**

Enunciado completo: [`docs/LAB-01-ARQ-2026.2.md`](docs/LAB-01-ARQ-2026.2.md)

## Equipo

| Nombre | GitHub | Rol |
|---|---|---|
| Luis Maquera | [@LuisMUtec](https://github.com/LuisMUtec) | |
| Johar Barzola | [@Joharjbe](https://github.com/Joharjbe) | |
| Fernando Aguilar | [@LordFernan](https://github.com/LordFernan) | |

---

## 1. Definición del Problema

Essalud lanza un piloto para gestionar sus Unidades de Cuidados Intensivos (UCI). El problema central es
el **desorden en los horarios de médicos y enfermeras** en un entorno de **alta rotación de médicos
internistas**, donde es crítico que el diagnóstico del turno saliente esté siempre disponible para el
turno entrante.

El piloto arranca en **Lima y sus distritos**, con proyección a escalar a nivel nacional.

El enunciado es la fuente de la tarea, no una fuente de hechos. Sus afirmaciones y sus cifras se
contrastan contra evidencia pública en [`docs/VALIDACION-PROBLEMA.md`](docs/VALIDACION-PROBLEMA.md);
esta sección recoge el resultado de ese contraste.

### 1.1 El desorden de horarios, en sus modos de falla

La Contraloría General de la República auditó 16 hospitales de EsSalud en ocho regiones entre el 29 de
noviembre y el 5 de diciembre de 2024. El desorden no es una queja genérica: se descompone en cuatro
fallas que ocurren por separado y se verifican por separado.

| # | Modo de falla | Extensión medida |
|---|---|---|
| MF-1 | La programación de turnos no existe o no sigue la normativa vigente | 63 % de los establecimientos |
| MF-2 | La programación existe, pero sin la aprobación del director que EsSalud exige | 6 de 16 establecimientos |
| MF-3 | La programación aprobada no se publica | 50 % de los establecimientos |
| MF-4 | La ejecución del turno no queda registrada de forma verificable, con mención expresa de UCI | 75 % de los establecimientos |

El valor del sistema está en convertir la programación en un artefacto aprobado, publicado y
verificable. Almacenarla no basta: MF-2, MF-3 y MF-4 sobreviven a un sistema que solo la guarde.

### 1.2 Punto de partida

El piloto no construye el registro clínico de la UCI. EsSalud opera desde 2019 la historia clínica
electrónica ESSI en sus 409 establecimientos y 30 redes asistenciales, con más de 10,5 millones de
pacientes registrados, y el país avanza hacia el RENHICE sobre los estándares HL7 y FHIR. Lo que el
piloto añade es una **capa de continuidad de turno** sobre un registro que ya existe, y el diagnóstico
que produce debe poder salir hacia el registro nacional.

`[ACLARAR: si el piloto lee y escribe en ESSI o mantiene registro propio y lo reconcilia. La respuesta
decide si el diagnóstico del turno es un dato propio del sistema o una vista sobre la historia clínica
existente.]`

### 1.3 Problemáticas críticas

| # | Problema | Descripción | Qué añade el contraste |
|---|---|---|---|
| P1 | Rotación de doctor | El diagnóstico del médico previo debe estar disponible antes del cambio de turno. | La rotación de personal no intensivista es la situación de base y se intensifica: hay del orden de 900 intensivistas frente a los 3 000 requeridos, y desde febrero de 2026 la NTS N.° 244-MINSA/DGAIN-2026 dejó de exigir que la UCI sea conducida por especialista. La disponibilidad del diagnóstico es necesaria y no suficiente: el efecto medido proviene de la entrega **estructurada y con recepción verificada**. |
| P2 | Medianoche | Un paciente se agrava en turno noche: contactar rápido al médico encargado y manejar su indisponibilidad. | El destinatario está fijado por norma: el médico programado en **guardia de retén**, convocado por el Jefe del Equipo de Guardia, en una ventana de 12 h (D. Leg. 559 y D. S. 024-2001-SA). «Programado en retén» es un estado de la programación de turnos, de modo que una falla de P1 degrada P2. |
| P3 | Actualizaciones en tiempo real | Notificaciones push a múltiples médicos/enfermeras y persistencia del diagnóstico con disponibilidad permanente. | Notificar a varios, leído como difusión, agrava el problema que pretende resolver: en UCI entre el 72 % y el 99 % de las alarmas son falsas y la sobrenotificación degrada la respuesta a la que sí importa. La garantía exigible es notificación **dirigida, con acuse y escalamiento por vencimiento**, con destinatarios derivados del turno vigente. |

### 1.4 Escalamiento esperado

| Hito | Cifra del enunciado |
|---|---|
| Lanzamiento | 1 K |
| 6 meses | 100 K |
| 2 años | 10 M |

Las cifras no admiten lectura como sedes hospitalarias. El Perú registra 247 hospitales y 25 242
IPRESS activas de todas las categorías y sectores; EsSalud opera 409 establecimientos; el mundo suma
del orden de 216 000 hospitales. La cifra de lanzamiento cuadruplica el total de hospitales del país y
la de dos años supera unas 46 veces el total mundial.

`[ACLARAR: qué cuentan las cifras de escalamiento. Personas registradas, camas, episodios de
hospitalización y dispositivos conducen a entornos de carga distintos.]`

`[SUPUESTO: la progresión 1 K, 100 K y 10 M cuenta personas registradas en el sistema y no sedes
hospitalarias. Es la única lectura compatible con las magnitudes del sector y con los 10,5 millones de
pacientes que EsSalud ya tiene en historia clínica electrónica.]`

### 1.5 Metas de rendimiento

- Inicio de aplicación: **< 1 s**
- Configuración de aplicación: **< 5 s**
- Disponibilidad: **99,9 %**
- Recuperación ante caída: **< 5 min**

Un 99,9 % admite 8 h 46 min de indisponibilidad al año y, con recuperación menor a 5 min, más de cien
interrupciones anuales. Ninguna de las dos cifras distingue el momento en que la interrupción ocurre,
siendo el cambio de turno la única ventana en que el enunciado declara crítica la disponibilidad del
diagnóstico.

`[ACLARAR: en qué ventana rige la disponibilidad de 99,9 %. Un mismo porcentaje mensual satisface o
incumple el caso según se distribuya dentro o fuera de las ventanas de cambio de turno.]`

`[SUPUESTO: el piloto opera sobre establecimientos con conectividad permanente. La operación tolerante
a desconexión se exige a partir del escalamiento a regiones, donde 749 establecimientos de salud
carecen de cobertura de internet.]`

---

## 2. Usuarios / Clientes del Sistema

Distinguimos **quién decide y paga** (clientes), **quién opera el sistema a diario** (usuarios directos),
**quién se ve afectado sin operarlo** (usuarios indirectos) y **quién impone restricciones externas**
(reguladores). Solo los usuarios directos generan personas modelo.

### 2.1 Clientes (deciden, financian y son dueños del resultado)

| Cliente | Qué espera del sistema | Criterio con el que juzga el piloto |
|---|---|---|
| Essalud — Gerencia Central de Operaciones | Reducir el desorden de horarios y estandarizar la continuidad clínica en UCI | Piloto replicable a otras regiones sin rehacer el sistema |
| Dirección de Red Prestacional (Lima) | Visibilidad de cobertura de turnos en todos los hospitales de su red | Cero turnos UCI sin médico asignado |
| Jefatura de Departamento de Cuidados Críticos | Que el diagnóstico del turno saliente exista y sea legible | Trazabilidad completa de entregas de turno |
| Oficina de Tecnologías de Información | Desplegar y operar en N hospitales con equipo limitado | Despliegue sin intervención máquina por máquina |

### 2.2 Usuarios directos (operan el sistema)

| Actor | Tipo | Interacción principal | Problema | Persona modelo |
|---|---|---|---|---|
| Médico internista rotante | Directo | Lee el handoff al entrar; registra evolución y diagnóstico al salir | P1 | [Rodrigo.MD](Personas/Rodrigo.MD) |
| Enfermera asistencial de UCI | Directo | Registra signos y eventos a pie de cama; escala deterioro | P2, P3 | [Milagros.MD](Personas/Milagros.MD) |
| Jefe / coordinador de UCI | Directo | Programa turnos mensuales; resuelve cruces y ausencias | P1 | [Carmen.MD](Personas/Carmen.MD) |
| Médico intensivista de guardia localizable | Directo | Recibe escalamiento fuera del hospital; decide conducta remota | P2 | [Anibal.MD](Personas/Anibal.MD) |
| Enfermera jefa de turno | Directo | Distribuye camas y valida la entrega de turno de enfermería | P1 | _(cubierta por Milagros y Carmen)_ |
| Técnico de enfermería | Directo | Consulta indicaciones; registra tareas de cuidado básico | P3 | _(no modelada: ver 2.5)_ |
| Coordinador de TI de red asistencial | Directo | Despliega, monitorea y recupera el sistema en los hospitales | P3 | _(no modelada: ver 2.5)_ |

### 2.3 Usuarios indirectos (afectados, no operan el sistema)

| Actor | Relación con el sistema |
|---|---|
| Paciente crítico | Sujeto de los datos clínicos; no interactúa (sedado/intubado en la mayoría de casos) |
| Familiar o apoderado | Recibe información derivada del sistema; puede ejercer derechos sobre los datos |
| Médico de otras especialidades | Solicita y responde interconsultas sobre pacientes UCI |
| Químico farmacéutico | Valida indicaciones farmacológicas registradas |
| Auditoría médica y calidad | Audita a posteriori la trazabilidad de diagnósticos y turnos |
| Admisión / referencias | Origina el ingreso del paciente a la unidad |

### 2.4 Reguladores y restricciones externas

| Fuente | Restricción que impone |
|---|---|
| Ley N.° 29733 — Protección de Datos Personales | Datos de salud son categoría sensible: consentimiento, cifrado y trazabilidad de acceso |
| D. S. N.° 016-2024-JUS — Reglamento de la Ley N.° 29733, vigente desde el 30 de marzo de 2025 | Notificación de una brecha de datos dentro de las 48 horas siguientes a su detección |
| Ley N.° 30024 — RENHICE | Interoperabilidad de la historia clínica electrónica a nivel nacional |
| D. S. N.° 020-2025-SA — modificación del reglamento del RENHICE | Marco vigente de intercambio; la validación nacional de interoperabilidad se ejecutó sobre HL7 y FHIR |
| D. Leg. N.° 559 y D. S. N.° 024-2001-SA — Ley de Trabajo Médico y su reglamento | Jornada de 150 h mensuales con guardia incluida; guardia hospitalaria de hasta 12 h continuas; guardia de retén de 12 h, sin presencia física, convocada por el Jefe del Equipo de Guardia |
| NTS N.° 244-MINSA/DGAIN-2026 — UPSS Cuidados Intensivos | Organización vigente de la unidad; deja sin efecto la exigencia de que la UCI sea conducida por especialista en medicina intensiva |
| SUSALUD | Estándares de calidad y disponibilidad del registro clínico |

### 2.5 Justificación de las personas elegidas

Cuatro personas, una por cada punto donde el sistema puede fallar de forma distinta:

- **Rodrigo** es el eje de **P1**: encarna la alta rotación de internistas que el enunciado marca como
  causa raíz. Si el handoff no le sirve a él, el sistema no resuelve nada.
- **Milagros** detecta el deterioro antes que nadie y dispara **P2**; además es la usuaria de mayor
  volumen de escritura, lo que tensiona **P3**.
- **Aníbal** es el otro extremo de **P2**: recibe el escalamiento fuera del hospital, con conectividad
  móvil y sin acceso al contexto de la cama.
- **Carmen** es la única que ve el sistema en modo planificación y no en modo emergencia; es quien
  expone el requisito de "horarios sin cruces".

Las cuatro son personal clínico: la evaluación de calidad puntúa únicamente requerimientos funcionales
(sección 3), y el comportamiento observable del sistema se juzga desde quien lo opera al lado del
paciente.

Quedan fuera del modelado:

| Actor | Motivo |
|---|---|
| Coordinador de TI de red asistencial | Sus necesidades son atributos de calidad —disponibilidad, recuperación, escalamiento—, que el enunciado ya fija como metas numéricas y que la evaluación no puntúa. Su exclusión desplaza el origen de los RNF: dejan de trazar a una persona y trazan al enunciado del caso. |
| Técnico de enfermería | Sus necesidades son un subconjunto de las de Milagros. |
| Paciente y familiar | No operan el sistema en el alcance del piloto. |

La Oficina de Tecnologías de Información permanece en la tabla de clientes (2.1): sigue siendo dueña
del resultado del piloto aunque no se modele como persona.

---

## 3. Prompt de Evaluación

> Prompt usado para contrastar los requerimientos contra las definiciones de personas (usuarios modelo).

**Alcance de la evaluación: requerimientos funcionales.** La rúbrica de
[`[Spec/Eval-Spec.MD](https://claude.ai/cowork/Spec/Eval-Spec.MD)`](Spec/Eval-Spec.MD) puntúa `Requirements/ReqFunc.MD` frente a las personas y los
problemas críticos. `Requirements/ReqNoFunc.MD` se entrega —el enunciado lo exige— pero queda fuera
del porcentaje: los atributos de calidad se verifican contra las metas numéricas del caso, no contra
la satisfacción de una persona.

El prompt se ejecuta en un cliente de IA con los archivos del repositorio adjuntos. Produce dos
salidas encadenadas: una evaluación por cada persona modelo y una agregación ponderada que devuelve
el porcentaje de calidad.

```text
Actúas sobre la especificación de requerimientos del sistema de gestión de UCI de Essalud.
Trabajas únicamente con los archivos adjuntos. No supongas contenido que no esté en ellos.

INSUMOS
1. README.md — problema, clientes y usuarios del sistema.
2. Personas/Rodrigo.MD, Personas/Milagros.MD, Personas/Carmen.MD, Personas/Anibal.MD —
   las cuatro personas modelo, con sus tareas críticas, criterios de éxito, escenario clave
   y restricciones.
3. Agents/agent-rodrigo.MD, Agents/agent-milagros.MD, Agents/agent-carmen.MD,
   Agents/agent-anibal.MD — la definición de agente derivada de cada persona.
4. Requirements/ReqFunc.MD — los requerimientos funcionales a evaluar.
5. Spec/Eval-Spec.MD — la rúbrica de agregación.
6. docs/CONVENCIONES.md — la forma que todo requerimiento debe adoptar.

ALCANCE
Se evalúa exclusivamente Requirements/ReqFunc.MD. Requirements/ReqNoFunc.MD no se puntúa:
sus afirmaciones no suman ni restan en ninguna dimensión. Si un RNF aparece dentro de
ReqFunc.MD, se reporta como requerimiento mal ubicado.

Ejecuta las dos fases en orden y no las mezcles. La fase 2 consume la salida literal de la fase 1.

═══ FASE 1 — EVALUACIÓN POR PERSONA ═══

Repite este bloque una vez por cada uno de los cuatro agentes.

Adopta íntegramente la definición de Agents/agent-<nombre>.MD: su identidad, su contexto
operativo, lo que le importa y lo que le frustra. Responde desde esa persona y no como
asistente. Recorre Requirements/ReqFunc.MD requerimiento por requerimiento y, para cada uno,
decide si te sirve, te es indiferente o te estorba, y justifícalo en una frase anclada a tu
día a día.

Reglas de la fase 1:
- No atribuyas al requerimiento capacidades que su enunciado no declara.
- Si un requerimiento admite más de una lectura, decláralo ambiguo. No adoptes la
  interpretación que te conviene.
- Prioriza la seguridad del paciente sobre la comodidad operativa.
- Recorre tus tareas críticas y tus criterios de éxito uno por uno y señala cuáles quedan
  sin cubrir por ningún RF. Esa lista es el insumo más importante de la fase 2.
- Verifica que tu escenario clave pueda recorrerse de principio a fin encadenando RF.
  Si se corta, indica en qué paso.
- No propongas requerimientos nuevos. Señalas la carencia; no la resuelves.

Formato de salida de cada agente:

## Evaluación de <Nombre>

| ID | Veredicto | Justificación |
|---|---|---|
| RF-XXX-NN | Sirve / Indiferente / Estorba / Ambiguo | |

### Necesidades no cubiertas
- <tarea crítica o criterio de éxito sin RF que lo cubra>

### Riesgos que veo
-

### Recorrido del escenario clave
<RF encadenados, o el paso en que se interrumpe>

### Cobertura percibida: __ %

═══ FASE 2 — AGREGACIÓN ═══

Adopta la rúbrica de Spec/Eval-Spec.MD. Recibe las cuatro evaluaciones de la fase 1 junto con
los insumos originales. No eres complaciente: un puntaje alto debe costar, y ante la duda se
penaliza.

Procedimiento:
1. Construye la matriz persona × tarea crítica → RF con las 24 tareas críticas de las cuatro
   personas. Marca los vacíos.
2. Verifica que P1 (rotación de doctor), P2 (medianoche) y P3 (actualizaciones en tiempo real)
   tengan al menos un RF que los resuelva, no que solo los nombre.
3. Revisa cada RF contra la forma exigida: un patrón EARS identificable, una sola respuesta,
   el sistema como sujeto del DEBE, sin mecanismo ni interfaz, con criterios de aceptación en
   pares Cuando/Entonces.
4. Incorpora los veredictos de la fase 1: un RF marcado Estorba o Ambiguo por una persona resta
   en D1.
5. Revisa las tensiones documentadas en Personas/README.md. Una tensión que ningún RF decide es
   brecha de D5, aunque cada RF por separado esté bien escrito.
6. Puntúa cada dimensión de 0 a 100, aplica los pesos y muestra el cálculo.

Reglas duras:
- Si una persona no tiene ninguna tarea crítica cubierta, D1 no supera 50.
- Si P1, P2 o P3 queda sin resolver, D2 no supera 60.
- Un RF cuyo sujeto del DEBE es una persona y no el sistema cuenta como no verificable en D3.
- Un RF sin criterios de aceptación cuenta como no verificable en D3.
- Un RF que nombra un mecanismo, un producto o una pantalla resta en D3 aunque sea claro.
- Reporta el porcentaje con un decimal como máximo.

Emite el resultado en el formato de salida que Spec/Eval-Spec.MD define: puntaje global, tabla
de dimensiones con peso y aporte, veredicto, matriz de cobertura, brechas, tensiones sin decidir,
requerimientos huérfanos, requerimientos mal formados, requerimientos mal ubicados y acciones
recomendadas priorizadas.

RESTRICCIONES DE TODA LA EJECUCIÓN
- No redactes ni reescribas requerimientos. El resultado es un diagnóstico, no una corrección.
- No inventes tareas críticas, criterios de éxito ni restricciones que las personas no declaren.
- Si falta un insumo, decláralo y no supongas su contenido.
- No consultes fuentes externas al repositorio adjunto.
```

Resultado del agente [`[Spec/Eval-Spec.MD](https://claude.ai/cowork/Spec/Eval-Spec.MD)`](Spec/Eval-Spec.MD): **__ %**

---

## Estructura del repositorio

```
README.md              # Este archivo: problema, usuarios/clientes y prompt de evaluación
/docs                  # Enunciado del caso, convenciones de redacción y validación del problema
/Personas              # Un MD por persona / usuario modelo
/Requirements          # ReqFunc.MD y ReqNoFunc.MD
/Agents                # Definición de agente por cada persona
/Spec                  # Eval-Spec.MD — agente evaluador de calidad de requerimientos
```

## Cómo trabajamos

1. Antes de escribir, se lee [`docs/CONVENCIONES.md`](docs/CONVENCIONES.md): fija qué afirma cada
   documento, la forma EARS de los requerimientos funcionales, el escenario de atributo de calidad de
   los no funcionales y la verificación de cada entregable contra la rúbrica de `Eval-Spec`.
   [`docs/VALIDACION-PROBLEMA.md`](docs/VALIDACION-PROBLEMA.md) fija qué del enunciado está
   comprobado, qué queda refutado y qué restricción del entorno obliga sin estar enunciada. Una
   afirmación del enunciado que ese documento refuta no entra en un requerimiento sin supuesto
   declarado.
2. Cada quien toma una persona y escribe su MD en `/Personas` + su agente en `/Agents`.
3. Los requerimientos F y NF se consensúan en grupo antes de commitear.
4. Ramas por tema (`persona/pablo`, `reqs/funcionales`), PR a `main`.
5. La corrida de `Eval-Spec` se documenta al final en este README.
