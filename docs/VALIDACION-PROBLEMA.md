# Validación de la definición del problema

Contraste de la [definición del problema](../README.md#1-definición-del-problema) y de las cifras del
[enunciado del caso](LAB-01-ARQ-2026.2.md) contra evidencia pública verificable. El enunciado es la
fuente de la tarea, no una fuente de hechos: cada afirmación que condiciona los entregables se
somete aquí a comprobación externa antes de que un requerimiento la dé por buena.

Este documento registra evidencia y sus consecuencias. No enuncia requerimientos: la afirmación que
resulta de un hallazgo se escribe en el documento que la posee, según la tabla de fronteras de
[`CONVENCIONES.md`](CONVENCIONES.md).

Veredictos empleados:

| Veredicto | Significado |
|---|---|
| Confirmado | La evidencia sostiene la afirmación del enunciado. |
| Confirmado y agravado | La evidencia la sostiene y muestra que la condición se intensifica dentro del horizonte del piloto. |
| Reencuadrado | El hecho subyacente es real, pero la formulación del enunciado apunta a la palanca equivocada. |
| Refutado | La afirmación no resiste el contraste y no puede usarse en su literalidad. |
| Ausente | Condición del entorno que restringe el problema y que el enunciado no contempla. |

## Resumen

| # | Afirmación contrastada | Veredicto |
|---|---|---|
| V-01 | Existe desorden en los horarios de médicos y enfermeras | Confirmado |
| V-02 | Hay alta rotación de médicos internistas en UCI | Confirmado y agravado |
| V-03 | La disponibilidad del diagnóstico del turno saliente es la palanca correcta | Confirmado |
| V-04 | Ante un agravamiento nocturno hay que contactar rápido al médico encargado | Confirmado |
| V-05 | La solución de P3 son notificaciones push a varios médicos y enfermeras | Reencuadrado |
| V-06 | El sistema escala a 1 K, 100 K y 10 M hospitales | Refutado |
| V-07 | El piloto construye el registro clínico de la UCI | Ausente en el enunciado |
| V-08 | Las metas de rendimiento del enunciado expresan la exigencia clínica | Reencuadrado |

---

## V-01 — El desorden de turnos en UCI está documentado por el órgano de control

**Afirmación del enunciado.** «La principal problemática es que se tiene un desorden en los horarios
de los médicos/enfermeras.»

**Evidencia.** La Contraloría General de la República ejecutó un operativo de control en 16
hospitales de EsSalud de ocho regiones —Piura, Arequipa, Lima, Lambayeque, Junín, Cusco, Tacna y
Amazonas— entre el 29 de noviembre y el 5 de diciembre de 2024, con resultados difundidos el 3 de
febrero de 2025 en 34 informes de visita de control [F1]:

| Hallazgo | Alcance |
|---|---|
| No evaluó la asistencia y permanencia del personal médico durante sus turnos, con mención expresa del servicio de UCI | 75 % (12 de 16) |
| No programó los turnos médicos conforme a la normativa vigente | 63 % (10 de 16) |
| No publicó la programación de turnos de enero a abril de 2025 | 50 % (8 de 16) |
| Contaba con programación, pero sin la aprobación del director exigida por EsSalud | 6 establecimientos |
| No dispuso de análisis de oferta y demanda de horas médicas en algunos servicios | 25 % (4 de 16) |

El patrón persiste en controles posteriores. En el Hospital III de EsSalud de Chimbote se constató
que el establecimiento no controla la asistencia, permanencia y ejecución de actividades del personal
asistencial, principalmente en áreas críticas [F2]. En los hospitales Albrecht, Víctor Lazarte
Echegaray y Alta Complejidad Virgen de la Puerta se constató que los registros manuales de asistencia
del personal médico carecen de supervisión efectiva y no reflejan la hora real de ingreso o salida
[F3].

**Veredicto.** Confirmado.

**Consecuencia para los entregables.** El problema es más preciso que «desorden» y se descompone en
cuatro fallas verificables por separado: la programación no existe o no sigue la norma; existiendo,
carece de aprobación de la autoridad que debe darla; aprobada, no se publica; y la ejecución del
turno no queda registrada de forma que alguien pueda verificarla. El valor del sistema está en
convertir la programación en un artefacto aprobado, publicado y verificable, no en almacenarla.

Contrastados los cuatro modos contra el área `RF-TUR`:

| Modo de falla | Estado |
|---|---|
| MF-1 — programación inexistente o no conforme | Cubierto por `RF-TUR-01`, `RF-TUR-02` y `RF-TUR-07` |
| MF-2 — programación sin aprobación de la autoridad | **Sin cubrir.** `RF-TUR-04` permite que la coordinadora publique una modificación que pasa a ser la única vigente sin que ninguna aprobación condicione su validez |
| MF-3 — programación aprobada no publicada | Cubierto por `RF-TUR-04`, `RF-TUR-05` y `RF-TUR-08` |
| MF-4 — ejecución del turno no verificable | Cubierto por `RF-SUP-01` y `RF-SUP-03` |

MF-2 es el modo que la Contraloría observó en 6 de los 16 establecimientos auditados. Cubrirlo exige
un requerimiento del área `RF-TUR` que condicione la vigencia de la programación a su aprobación.

---

## V-02 — La rotación de médicos no intensivistas en UCI es real y la norma vigente la amplía

**Afirmación del enunciado.** «Hay una alta rotación de médicos internistas.»

**Evidencia.** La Sociedad Peruana de Medicina Intensiva reporta del orden de 900 médicos
intensivistas y 64 intensivistas pediátricos frente a un requerimiento de al menos 3 000
profesionales de la especialidad, sobre un parque de 1 264 camas UCI en el país; el país debería
contar con 12 camas UCI por cada 100 000 habitantes y cuenta con cerca de 1 [F4]. La brecha no se ha
cerrado desde que se documentó por primera vez: en 2020 se contabilizaban 700 intensivistas
registrados y se declaraba necesario duplicar esa cifra [F5].

El 11 de febrero de 2026, la Resolución Ministerial N.° 115-2026/MINSA aprobó la NTS
N.° 244-MINSA/DGAIN-2026 sobre la Unidad Productora de Servicios de Salud de Cuidados Intensivos y
dejó sin efecto la RM 489-2005 y la RM 161-2020. Con ello desaparece del texto normativo la exigencia
—vigente durante veinte años— de que la UCI sea conducida por especialista en medicina intensiva
[F6]. El Colegio Médico del Perú y la Sociedad Peruana de Medicina Intensiva rechazaron el cambio e
instalaron una mesa para modificar la norma [F7][F8].

**Veredicto.** Confirmado y agravado.

**Consecuencia para los entregables.** La rotación de personal no intensivista por la UCI no es una
simplificación del caso: es la situación de base, y su probabilidad crece durante el horizonte del
piloto. De ahí se sigue una exigencia sobre el contenido de la entrega de turno: el sistema no puede
suponer que quien la recibe conoce la unidad, el caso ni el criterio con que se decidió. La persona
[Rodrigo](../Personas/Rodrigo.MD) queda validada como ancla de P1 y su escenario clave —recibir camas
que no vio nunca— es el caso normal, no el peor caso.

---

## V-03 — La entrega de turno es el punto de falla correcto, y el mecanismo que funciona es la estructura

**Afirmación del enunciado.** «Es clave que los diagnósticos siempre estén disponibles para el
siguiente turno en el sistema.»

**Evidencia.** La comunicación en las transiciones de cuidado es un problema de seguridad del
paciente con norma internacional propia: la Joint Commission le dedicó la Alerta de Evento Centinela
n.° 58 [F9] y la Organización Mundial de la Salud, la solución de seguridad del paciente n.° 3
[F10]. Alrededor del 67 % de los errores de comunicación se relacionan con la entrega de turno, y las
fallas de comunicación contribuyen a entre el 50 % y el 80 % de los eventos centinela [F11].

La magnitud del efecto alcanzable está medida. El estudio del paquete de entrega de turno I-PASS,
publicado en el *New England Journal of Medicine* en 2014 sobre 10 740 admisiones en nueve centros
académicos, registró una reducción relativa del 23 % en la tasa de errores médicos —de 24,5 a 18,8
por cada 100 admisiones— y del 30 % en eventos adversos prevenibles —de 4,7 a 3,3 por cada 100
admisiones—, sin efecto negativo sobre el flujo de trabajo [F12].

**Veredicto.** Confirmado.

**Consecuencia para los entregables.** El beneficio no proviene de que el diagnóstico esté guardado y
disponible, sino de que la entrega sea estructurada y su recepción quede verificada. Persistir el
texto es condición necesaria y no suficiente; un requerimiento que solo garantice disponibilidad
declara resuelto P1 sin resolverlo.

La cláusula «sin efecto negativo sobre el flujo de trabajo» del mismo estudio es el criterio que
decide la tensión «velocidad frente a completitud del registro» de
[`Personas/README.md`](../Personas/README.md): la estructura es exigible en tanto no aumente el
tiempo de registro, y ese es el umbral contra el que se juzga, no la preferencia de una persona sobre
la otra.

---

## V-04 — El destinatario del escalamiento nocturno ya está definido por norma: la guardia de retén

**Afirmación del enunciado.** «¿Cómo el sistema ayudará a contactar rápidamente al médico encargado?
¿Qué pasa si el médico no está disponible?»

**Evidencia.** La Ley de Trabajo Médico —Decreto Legislativo N.° 559— y su reglamento —Decreto
Supremo N.° 024-2001-SA— fijan la figura y sus límites [F13][F14]:

| Elemento | Contenido normativo |
|---|---|
| Jornada médica | 6 horas diarias, 36 semanales o 150 mensuales, guardia incluida |
| Guardia hospitalaria | No excede 12 horas continuas; excepcionalmente, por necesidad de servicio, hasta 24 |
| Guardia de retén | El médico programado en retén está exento de presencia física permanente y permanece disponible para ser llamado durante el turno correspondiente; dura 12 horas |
| Quién convoca | El Jefe del Equipo de Guardia |
| Exceso de jornada | Lo que supera las 150 horas mensuales es guardia extraordinaria |

Sobre el escalamiento fuera de horario, la revisión sistemática de los determinantes del escalamiento
del deterioro clínico fuera de horario documenta que la conducción de enfermería nocturna es reducida
o inexistente y que los niveles de dotación se asocian a demoras en el escalamiento [F15].

**Veredicto.** Confirmado.

**Consecuencia para los entregables.** La pregunta abierta del enunciado tiene respuesta normativa y
deja de ser una pregunta de diseño: el sistema alcanza al médico programado en retén, la convocatoria
corresponde al Jefe del Equipo de Guardia y la ventana es el turno de 12 horas. La indisponibilidad
del retén es, por tanto, un requerimiento de borde con respuesta definida y no una incógnita.

El hallazgo ata P2 a P1: «programado en retén» es un estado de la programación de turnos. Sin
programación aprobada y vigente, el sistema no tiene a quién llamar, de modo que un fallo de P1
degrada P2. La persona [Aníbal](../Personas/Anibal.MD) queda validada: corresponde a un rol con
definición legal, no a un actor inventado.

---

## V-05 — La notificación amplia enunciada en P3 es, sin acotar, un riesgo de seguridad del paciente

**Afirmación del enunciado.** «¿Cómo maneja las notificaciones push a varios médicos/enfermeras?»

**Evidencia.** En cuidados intensivos, la proporción de alarmas falsas se sitúa entre el 72 % y el
99 % según el contexto de monitoreo, con estudios que superan el 85 %, y hasta el 77 % de las alarmas
falsas proviene de apenas el 2 % de los pacientes [F16][F17]. El obstáculo mejor puntuado por el
personal de enfermería es precisamente «alarmas falsas frecuentes, que reducen la atención o la
respuesta a las alarmas» [F17]. Entre 2009 y 2012 la Joint Commission recibió 98 incidentes
relacionados con alarmas, de los cuales 80 terminaron en muerte del paciente [F16]. Los riesgos por
alarmas clínicas figuran de forma recurrente en la lista de los diez principales riesgos tecnológicos
del ECRI [F16].

**Veredicto.** Reencuadrado.

**Consecuencia para los entregables.** «Notificar a varios médicos y enfermeras», leído como
difusión, produce fatiga de alarma y reduce la respuesta a la alarma que sí importa: el enunciado
nombra el mecanismo y el mecanismo, sin acotar, agrava el problema que pretende resolver. El
comportamiento exigible es notificación dirigida, con acuse de recibo y escalamiento por vencimiento
del acuse; el conjunto de destinatarios se deriva del turno vigente y no de una lista de difusión.

Este es también el criterio que decide la tensión «sensibilidad del escalamiento» entre
[Milagros](../Personas/Milagros.MD) y [Aníbal](../Personas/Anibal.MD): la evidencia no deja la
decisión en empate, porque el costo de la sobrenotificación está medido y es el que degrada la
respuesta.

---

## V-06 — La escala de 1 K, 100 K y 10 M no admite lectura como hospitales

**Afirmación del enunciado.** Lanzamiento con 1 K hospitales, 100 K a seis meses y 10 M a dos años.

**Evidencia.**

| Magnitud | Valor | Fuente |
|---|---|---|
| Hospitales en el Perú | 247 | Diagnóstico de Brechas de Infraestructura y Equipamiento del Sector Salud, MINSA, 2020 [F18] |
| Establecimientos de salud del primer nivel en el Perú | 8 783 | Mismo diagnóstico [F18] |
| IPRESS activas en el Perú, todas las categorías y sectores | 25 242 | SUSALUD [F19] |
| Establecimientos de EsSalud | 409, en 30 redes asistenciales | EsSalud [F20] |
| Hospitales en el mundo | ≈ 216 000 (proyección 2026) | Juniper Research vía Statista [F21] |

La cifra de lanzamiento —1 K— cuadruplica el total de hospitales del país y supera el doble de los
establecimientos de EsSalud de todas las categorías. La cifra a dos años —10 M— supera unas 46 veces
el total mundial de hospitales. Ninguna lectura del término «hospital» sostiene la progresión.

Una lectura sí cierra con las magnitudes reales de la institución: EsSalud declara más de 10,5
millones de pacientes con historia clínica electrónica [F20] y del orden de 12,5 millones de
asegurados [F22]. El único eje que alcanza el orden de 10 M en el dominio del caso es el de personas
registradas.

**Veredicto.** Refutado en su literalidad.

**Consecuencia para los entregables.** Las tres cifras no pueden entrar como entorno de carga sin
declarar qué cuentan. La ambigüedad pertenece al README —que es dueño de los supuestos sobre el
enunciado— y desde allí la referencia `RNF-ESC`. Mientras no se resuelva, el trabajo continúa bajo el
supuesto declarado en la sección de escalamiento del README.

---

## V-07 — El piloto no parte de cero: EsSalud ya opera una historia clínica electrónica

**Afirmación del enunciado.** Ninguna. El enunciado describe el piloto como si el registro clínico
fuera a construirse con él.

**Evidencia.** EsSalud opera desde 2019 el Sistema de Gestión de Servicios de Salud, ESSI, que
incluye la historia clínica electrónica y está desplegado en sus 409 establecimientos de salud y 30
redes asistenciales, con más de 10,5 millones de pacientes con historia clínica electrónica; el
sistema da acceso a atenciones recibidas, exámenes auxiliares, diagnósticos y prescripciones, y
EsSalud autorizó a MINSA a usarlo [F20][F23].

En paralelo avanza la interoperabilidad nacional. MINSA y EsSalud suscribieron convenio para la
implementación del RENHICE [F23]; el Decreto Supremo N.° 020-2025-SA modificó el reglamento de la ley
que lo crea [F24]; y la Conectatón IPS Perú 2025, celebrada el 17 y 18 de junio de 2025 con 36
entidades públicas, privadas y mixtas, validó el intercambio de historias clínicas sobre los
estándares HL7 y FHIR [F25].

**Veredicto.** Ausente en el enunciado, y restrictivo.

**Consecuencia para los entregables.** El sistema de gestión de UCI no puede ser la fuente de verdad
de la historia clínica: existe una y está desplegada en toda la red donde correría el piloto. Lo que
el piloto añade es una capa de continuidad de turno sobre un registro preexistente, y el diagnóstico
que produce debe poder salir hacia el registro nacional.

Esto es una restricción del problema y no una decisión de arquitectura: cambia qué comportamiento se
le exige al sistema —referenciar y componer en lugar de pedir de nuevo—, y por eso pertenece al
README y no a `docs/` como nota técnica. Cubre además de forma directa una frustración documentada de
Rodrigo: «sistemas que le piden ingresar de nuevo datos que ya están en otro sistema».

La salida hacia el registro nacional está enunciada en `RNF-INT-01`. La entrada desde el registro
institucional ya desplegado no lo está: `RNF-INT-01` toma como fuente el sistema nacional, y
`RF-REG-06` presenta los resultados de laboratorio e imágenes del episodio sin declarar de dónde
provienen. Queda abierta la pregunta de la sección final sobre si el piloto lee de ESSI o mantiene
registro propio; la respuesta decide si `RF-REG-06` describe una vista o un almacén.

---

## V-08 — Las metas de rendimiento del enunciado no expresan la exigencia clínica

**Afirmación del enunciado.** Inicio de aplicación menor a 1 s, configuración menor a 5 s,
disponibilidad de 99,9 % y recuperación menor a 5 min.

**Evidencia.** Una disponibilidad de 99,9 % admite 8 horas y 46 minutos de indisponibilidad al año.
Combinada con una recuperación menor a 5 minutos, el presupuesto admite más de cien interrupciones
anuales. Ninguna de las dos cifras distingue el momento en que la interrupción ocurre, siendo que el
propio enunciado declara crítica la disponibilidad del diagnóstico en una ventana concreta: el cambio
de turno.

Que la caída ocurre está medido. El 96 % de las organizaciones de salud encuestadas reportó al menos
una caída no planificada de la historia clínica electrónica en tres años y el 70 % al menos una de
ocho horas o más; entre 2012 y 2018 se registraron 43 eventos que afectaron a 166 hospitales y
sumaron 701 hospital-día de caída; las caídas se asocian a errores de medicación y a mayor duración
de la estancia postoperatoria [F26].

Para el escalamiento posterior al piloto de Lima pesa además la conectividad: 749 centros de salud
permanecen sin cobertura de internet, y en 2024 el uso de internet alcanzó al 58,4 % de la población
rural frente al 84,2 % de la urbana [F27].

**Veredicto.** Reencuadrado.

**Consecuencia para los entregables.** Tres consecuencias, todas para `ReqNoFunc.MD`:

1. La disponibilidad se condiciona a la ventana de cambio de turno, y la caída exige un modo
   degradado con procedimiento definido: un sistema del que se espera que sea la única fuente del
   handoff no puede dejar la caída sin respuesta enunciada.
2. El inicio en menos de 1 s no es la latencia que ata clínicamente. La que ata es el tiempo hasta
   disponer del handoff completo de la unidad, que es lo que la persona Rodrigo mide en minutos.
3. La operación tolerante a desconexión es requisito del escalamiento nacional y no una
   optimización; para el piloto en Lima, es un supuesto declarado.

---

## Consecuencias consolidadas

| Hallazgo | Dónde aterriza | Estado |
|---|---|---|
| V-01 | `README.md` §1.1 · `RF-TUR`, `RF-SUP` | Aplicado, salvo MF-2: la vigencia de la programación no depende de su aprobación |
| V-02 | `README.md` §1.3 · `Personas/Rodrigo.MD` | Aplicado: la rotación de personal no intensivista es la situación de base |
| V-03 | `RF-DIA-02`, `RF-DIA-04` | Cubierto: la entrega tiene contenido fijado y cierre con autoría e instante |
| V-04 | `README.md` §2.4 · `RF-ESC-01`, `RF-ESC-07`, `RF-ESC-09` | Aplicado: el destinatario, la delegación y el agotamiento de la cadena están enunciados |
| V-05 | `RF-ESC-01` a `RF-ESC-06` · `RNF-PER-03` | Cubierto: notificación dirigida, con acuse, severidades separadas y avance por vencimiento |
| V-06 | `README.md` §1.4 · `RNF-PER-01`, `RNF-PER-03`, `RNF-PER-04`, `RNF-ESC` | Aplicado: el entorno de carga cuenta personas registradas bajo supuesto declarado |
| V-07 | `README.md` §1.2 · `RNF-INT-01` | Parcial: la salida hacia el registro nacional está enunciada; la entrada desde ESSI, no |
| V-08 | `README.md` §1.5 · `RNF-DIS-03`, `RNF-DIS-04`, `RNF-PER-04` | Aplicado: modo degradado, operación sin conectividad y latencia del handoff |

Brechas que la validación deja abiertas y que corresponden al equipo decidir:

| # | Brecha | Área |
|---|---|---|
| B-1 | Ningún requerimiento condiciona la vigencia de la programación a su aprobación por la autoridad que EsSalud exige | `RF-TUR` |
| B-2 | Ningún escenario declara la procedencia del registro clínico preexistente que el sistema presenta | `RNF-INT`, `RF-REG-06` |

## Supuestos declarados

`[SUPUESTO: la progresión 1 K, 100 K y 10 M cuenta personas registradas en el sistema y no sedes
hospitalarias. Es la única lectura compatible con las magnitudes del sector —247 hospitales en el
país, 409 establecimientos de EsSalud, 10,5 millones de pacientes con historia clínica electrónica— y
con el orden de magnitud de 10 M.]`

`[SUPUESTO: el piloto opera sobre establecimientos con conectividad permanente. La operación
tolerante a desconexión se exige a partir del escalamiento a regiones, donde 749 establecimientos
carecen de cobertura de internet.]`

## Preguntas abiertas

`[ACLARAR: qué cuentan las cifras de escalamiento. Personas registradas, camas, episodios de
hospitalización y dispositivos conducen a entornos de carga distintos y a decisiones de diseño
distintas.]`

`[ACLARAR: si el piloto puede leer y escribir en ESSI o si debe mantener su propio registro y
reconciliarlo. La respuesta decide si el diagnóstico del turno es un dato propio o una vista sobre la
historia clínica existente.]`

`[ACLARAR: en qué ventana rige la disponibilidad de 99,9 %. Un mismo porcentaje mensual satisface o
incumple el caso según se distribuya dentro o fuera de las ventanas de cambio de turno.]`

## Fuentes

| # | Referencia |
|---|---|
| F1 | Contraloría revela deficiencias en turnos médicos en hospitales de EsSalud. *Gestión*, 3 de febrero de 2025. https://gestion.pe/peru/contraloria-revela-deficiencias-en-turnos-medicos-en-hospitales-de-essalud-noticia/ |
| F2 | Alertan deficiencias y falta de control en áreas críticas del hospital de EsSalud de Chimbote. Contraloría General de la República, Informe de Visita de Control n.° 5814-2026-CG/GRAN-SVC. https://www.gob.pe/institucion/contraloria/noticias/1391587-alertan-deficiencias-y-falta-de-control-en-areas-criticas-del-hospital-de-essalud-de-chimbote |
| F3 | Contraloría alerta sobre irregularidades en el cumplimiento de turnos en 8 hospitales públicos de Trujillo. https://sientetrujillo.com/contraloria-alerta-sobre-irregularidades-en-el-cumplimiento-de-turnos-en-8-hospitales-publicos-de-trujillo/ |
| F4 | Hospitales del Minsa en crisis: «No existen camas libres en UCI», advierte la Sociedad Peruana de Medicina Intensiva. *Infobae*, 9 de junio de 2024. https://www.infobae.com/peru/2024/06/09/hospitales-del-minsa-en-crisis-no-existen-camas-libres-en-uci-advierte-la-sociedad-peruana-de-medicina-intensiva/ |
| F5 | Sin respiro: Unidades de Cuidados Intensivos necesitan 700 médicos. *Salud con Lupa*, 9 de abril de 2020. https://saludconlupa.com/entrevistas/sin-respiro-unidades-de-cuidados-intensivos-necesitan-700-medicos/ |
| F6 | Minsa elimina obligatoriedad de intensivistas en UCI. *Infobae*, febrero de 2026. RM N.° 115-2026/MINSA y NTS N.° 244-MINSA/DGAIN-2026. https://www.infobae.com/peru/2026/02/12/minsa-elimina-obligatoriedad-de-intensivistas-en-uci-medicos-no-especialistas-podrian-atender-a-pacientes-criticos/ |
| F7 | CMP y SOPEMI: se realizó la primera reunión para modificar la NTS UCI. Colegio Médico del Perú. https://www.cmp.org.pe/cmp-y-sopemi-se-realizo-la-primera-reunion-para-modificar-la-nts-uci/ |
| F8 | Sociedad Peruana de Medicina Intensiva advierte que nueva norma del Minsa abre la puerta al intrusismo en las UCI. *El Comercio*. https://elcomercio.pe/lima/sociedad-peruana-de-medicina-intensiva-advierte-que-nueva-norma-del-minsa-abre-la-puerta-al-intrusismo-en-las-uci-ultimas-noticia/ |
| F9 | Sentinel Event Alert 58: Inadequate hand-off communication. The Joint Commission. https://www.jointcommission.org/en-us/knowledge-library/newsletters/sentinel-event-alert/issue-58 |
| F10 | Communication During Patient Hand-Overs. Patient Safety Solutions, vol. 1, solución 3. Organización Mundial de la Salud. https://cdn.who.int/media/docs/default-source/patient-safety/patient-safety-solutions/ps-solution3-communication-during-patient-handovers.pdf |
| F11 | Patient Handoffs: The Gap Where Mistakes Are Made. *Patient Safety & Quality Healthcare*. https://www.psqh.com/analysis/patient-handoffs-gap-mistakes-made/ |
| F12 | Starmer A. J. et al. Changes in Medical Errors after Implementation of a Handoff Program. *New England Journal of Medicine*, 2014. https://www.nejm.org/doi/full/10.1056/NEJMsa1405556 |
| F13 | Decreto Legislativo N.° 559, Ley de Trabajo Médico. https://www.amp.pe/D_LEG_N_559.htm |
| F14 | Decreto Supremo N.° 024-2001-SA, Reglamento de la Ley de Trabajo Médico. https://www.minsa.gob.pe/Recursos/OGTI/SINADEF/DS-024-2001-SA.pdf |
| F15 | The determinants of nursing staff escalating clinical deterioration out-of-hours: a mixed methods systematic review. *International Journal of Nursing Studies*. https://www.sciencedirect.com/science/article/pii/S0020748926000787 |
| F16 | Alarm Fatigue. En *Making Healthcare Safer III*. Agency for Healthcare Research and Quality. https://www.ncbi.nlm.nih.gov/books/NBK555522/ |
| F17 | Nurses' Perceptions and Practices Toward Clinical Alarms in a Transplant Cardiac Intensive Care Unit. *JMIR Human Factors*, 2015. https://pmc.ncbi.nlm.nih.gov/articles/PMC4797660/ |
| F18 | El 97 % de los establecimientos de salud del primer nivel de atención cuenta con capacidad instalada inadecuada. ComexPerú, 25 de febrero de 2021, sobre el Diagnóstico de Brechas de Infraestructura y Equipamiento del Sector Salud del MINSA. https://www.comexperu.org.pe/articulo/el-97-de-los-establecimientos-de-salud-del-primer-nivel-de-atencion-cuenta-con-capacidad-instalada-inadecuada |
| F19 | El 94,5 % de establecimientos de salud del primer nivel de atención pública presenta capacidad instalada inadecuada. ComexPerú, 8 de agosto de 2023, sobre datos de SUSALUD. https://www.comexperu.org.pe/articulo/el-945-de-establecimientos-de-salud-del-primer-nivel-de-atencion-publica-presenta-capacidad-instalada-inadecuada |
| F20 | EsSalud: más de 10 millones de pacientes cuentan con historia clínica electrónica. *Agencia Andina*, 5 de febrero de 2022. https://andina.pe/agencia/noticia-essalud-mas-10-millones-pacientes-cuentan-historia-clinica-electronica-879832.aspx |
| F21 | Total number of hospitals globally 2021-2026. Juniper Research vía Statista. https://www.statista.com/statistics/1550124/number-of-hospitals-globally/ |
| F22 | EsSalud: población asegurada aumentó en más de 6 % en el tercer trimestre de 2022. EsSalud. http://noticias.essalud.gob.pe/?inno-noticia=essalud-poblacion-asegurada-aumento |
| F23 | Minsa y EsSalud pactan la implementación del registro de historia clínica electrónica. *ConsultorSalud*. https://consultorsalud.com/minsa-y-essalud-historia-clinica-electronica/ |
| F24 | Decreto Supremo N.° 020-2025-SA, que modifica el reglamento de la ley que crea el RENHICE. https://lpderecho.pe/modifican-reglamento-ley-crea-registro-nacional-historias-clinicas-electronicas-renhice-decreto-supremo-020-2025-sa/ |
| F25 | Transformación digital: Perú valida interoperabilidad de historias clínicas electrónicas. OPS/OMS, 20 de junio de 2025. https://www.paho.org/es/noticias/20-6-2025-transformacion-digital-peru-valida-interoperabilidad-historias-clinicas |
| F26 | Larsen E. P., Rao A. H., Sasangohar F. Understanding the scope of downtime threats: a scoping review of downtime-focused literature and news media. *Health Informatics Journal*, 2020. https://journals.sagepub.com/doi/full/10.1177/1460458220918539 |
| F27 | Brecha digital en el Perú: el reto pendiente de conectar salud y educación con impacto real. *Expreso*. https://www.expreso.com.pe/tecnologia/brecha-digital-en-el-peru-el-reto-pendiente-conectar-salud-y-educacion-con-impacto-real-noticia/1287641/ |
| F28 | Decreto Supremo N.° 016-2024-JUS, Reglamento de la Ley N.° 29733, vigente desde el 30 de marzo de 2025. https://www.garrigues.com/es_ES/noticia/peru-publica-nuevo-reglamento-ley-proteccion-datos-personales |
