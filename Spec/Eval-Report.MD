# Evaluación de Requerimientos Funcionales — Sistema UCI Essalud

| | |
|---|---|
| Corrida | 2026-08-18-iteracion-12 |
| Rama | `eval/iteracion-12` |
| Modelo | `claude-opus-5[1m]` |
| Evaluado | `Requirements/ReqFunc.MD` @ `4eb06c0` |
| Rúbrica | `Spec/Eval-Spec.MD` @ `b492237` |
| Personas | `Personas/` @ `ec9143a` |
| Agentes | `Agents/` @ `502e657` |
| Instrumento modificado | no |

`Instrumento modificado` dice `no` porque la comprobación de conformidad no corrigió ningún agente:
las seis tareas de cada agente coinciden con las de su persona y sus frustraciones derivan de ella.
Aun así **esta corrida no es comparable con it11**: entre una y otra, `Personas/*.MD` sumó doce
dolores nuevos —tres por persona— y los cuatro agentes los reflejan. Cambió quien mide. Lo medido no:
`ReqFunc.MD` es byte a byte el documento de it11 y la rúbrica tampoco cambió.

## Puntaje global: 70,0 %

| Dimensión | Puntaje | Peso | Aporte |
|---|---:|---:|---:|
| D1 Satisfacción de las personas | 43,0 | 30 % | 12,900 |
| D2 Cobertura de problemas críticos | 66,7 | 25 % | 16,675 |
| D3 Verificabilidad y forma | 99,5 | 20 % | 19,900 |
| D4 Trazabilidad | 100,0 | 10 % | 10,000 |
| D5 Consistencia y tensiones | 60,0 | 10 % | 6,000 |
| D6 Priorización | 90,0 | 5 % | 4,500 |
| **Global** | | | **69,975 → 70,0** |

## Veredicto: FAILED

| # | Condición | Umbral | Obtenido | Cumple |
|---|---|---:|---:|:---:|
| 1 | Puntaje global | ≥ 80,0 % | 70,0 % | ✗ |
| 2 | D1 | ≥ 80,0 | 43,0 | ✗ |
| 3 | Persona más baja | ≥ 7,0/10 | 2,4 | ✗ |
| 4 | D2 | ≥ 80,0 | 66,7 | ✗ |
| 5 | Controles de `verificar.sh` | 9/9 | 9/9 | ✓ |

## Puntajes por persona

| Persona | Tareas /30 | Estorba fuera | Necesidades /6 | Flujo /4 | Puntaje /10 | Percibida | Δ |
|---|---:|---:|---:|---:|---:|---:|---:|
| Rodrigo | 24 | 1 | 4,4 | 0 | 4,4 | 80 | 36 |
| Milagros | 18 | 3 | 2,4 | 0 | 2,4 | 80 | 56 |
| Carmen | 18 | 1 | 3,2 | 2 | 5,2 | 80 | 28 |
| Aníbal | 20 | 2 | 3,2 | 2 | 5,2 | 85 | 33 |
| **PROMEDIO** | **20,0** | **1,75** | **3,3** | **1,0** | **4,3** | **81,3** | **38** |

## Brechas encontradas

| # | Persona o problema afectado | Brecha | Origen | Severidad |
|---|---|---|---|---|
| 1 | Rodrigo / P1 | Ningún enunciado declara que el médico presencial emita una indicación médica nueva desde el equipo de la unidad; `RF-REG-07` solo cubre la indicación a distancia desde dispositivo personal. Es el paso que corta su escenario | veredicto-rodrigo | Alta |
| 2 | Rodrigo / P1 | No hay forma de registrar qué pendiente queda abierto ni quién responde por él durante el turno; la marca de `RF-DIA-06` señala la cama, no el pendiente | veredicto-rodrigo | Media |
| 3 | Rodrigo / P1 | `RF-DIA-03` señala la cama sin diagnóstico antes de aceptar, pero aceptar es todo o nada: ningún enunciado deja constar que se recibe observada | veredicto-rodrigo | Media |
| 4 | Rodrigo / P3 | Qué entrega la consulta de la actualización informativa no está decidido: `RF-ESC-13` y `RF-ESC-49` fijan dos anclas distintas | veredicto-rodrigo | Alta |
| 5 | Milagros / P2 | El escalamiento no avanza cuando el médico acusa recibo y no toma el caso: `RF-ESC-02` solo avanza por falta de acuse. Es el paso que corta su escenario | veredicto-milagros | Alta |
| 6 | Milagros / P2 | La indicación de un médico que acusó sin tomar el caso no tiene registro: `RF-REG-07` no lo admite como autor | veredicto-milagros | Alta |
| 7 | Milagros / P2 | Ningún enunciado dice a quién alcanzan los avisos junto a la cama mientras rige un relevo declarado | veredicto-milagros | Media |
| 8 | Milagros / P2 | La indisponibilidad declarada sin delegado se informa a la jefatura y no la alcanza junto a la cama | veredicto-milagros | Media |
| 9 | Milagros / P1 | El turno recibido de una saliente que no cerró entrega (`RF-DIA-33`) no comprende marcas vigentes, escalamientos sin cierre ni indicaciones vigentes, que `RF-DIA-11` sí entrega | veredicto-milagros | Media |
| 10 | Carmen / P1 | `RF-TUR-01` **CA-2** declara expresamente que dos guardias localizables de la misma franja en dos sedes no se rechazan, y ningún enunciado presenta esa situación a la coordinadora | veredicto-carmen | Alta |
| 11 | Carmen / P1 | `RF-TUR-14` conserva la versión desplazada del rol y ningún enunciado declara que la coordinadora pueda consultarla | veredicto-carmen | Media |
| 12 | Carmen / P1 | Reconstruir quién estaba de guardia una noche pasada: `RF-TUR-16` no declara si entrega las asignaciones tal como quedaron o las que rigieron día a día | veredicto-carmen | Media |
| 13 | Carmen / P1 | Ningún RF cuenta ni presenta el reparto de feriados y de noches por profesional en el año | veredicto-carmen | Media |
| 14 | Carmen / P1 | No hay vista por profesional de todas sus asignaciones en las unidades de la Red: `RF-TUR-27` consulta una unidad y un periodo | veredicto-carmen | Alta |
| 15 | Carmen / P1 | No se puede asignar directamente una franja descubierta de la programación vigente fuera del horizonte de urgencia: `RF-TUR-44` solo rige dentro de él | veredicto-carmen | Media |
| 16 | Carmen / P2 | Ningún enunciado avisa a la coordinadora cuando `RF-ESC-45` cubre sola una franja de su unidad | veredicto-carmen | Media |
| 17 | Carmen / P1 | Nadie le lista los registros clínicos retenidos sin autoría de su unidad, de cuya trazabilidad responde ante la Red | veredicto-carmen | Media |
| 18 | Carmen / P1 | Falta el indicador de trazabilidad de la documentación clínica: `RF-SUP-02` y `RF-SUP-05` nombran solo dos de los tres que la Red le pide | veredicto-carmen | Media |
| 19 | Aníbal / P2 | Del silencio no se entera: `RF-REG-09` solo devuelve la confirmación de ejecución cuando la enfermera la produce | veredicto-anibal | Alta |
| 20 | Aníbal / P2 | Ningún enunciado declara el acto de revocar anticipadamente la propia indisponibilidad declarada | veredicto-anibal | Media |
| 21 | Aníbal / P2 | Ningún enunciado conserva ni devuelve el contexto clínico que se le adjuntó al escalamiento: si el caso termina en un reclamo, la única versión que queda es la de quien llamó | veredicto-anibal | Alta |
| 22 | Aníbal / P2 | Falta el balance hídrico del día: `RF-REG-08` lo acota a seis horas y `RF-ESC-03` no declara ventana | veredicto-anibal | Media |
| 23 | Aníbal / P2 | El paquete de `RF-ESC-03` no comprende las indicaciones vigentes de la cama | veredicto-anibal | Media |
| 24 | P1 | El tramo de recuperación queda bloqueado: `RF-DIA-47` admite el diagnóstico tardío y `RF-IAM-02` ordena rechazar el registro de quien ya no tiene turno en curso | auditoría | Alta |
| 25 | P2 | Tras la reiteración de `RF-ESC-47` al alcanzado por el agotamiento, ningún enunciado declara qué hace el sistema si tampoco acusa | auditoría | Alta |
| 26 | Forma (D3) | `RF-ESC-13` enuncia dos respuestas: la incorporación y lo que la consulta entrega | auditoría | Media |
| 27 | Consistencia (D5) | `RF-IAM-16` cierra con «exactamente las consultas clínicas, los registros clínicos y los escalamientos» y el resto del documento reclama del mismo dispositivo el acuse, la toma del caso, la confirmación de lectura y la aceptación de entrega | auditoría | Alta |
| 28 | Consistencia (D5) | Los bloques «Por qué existe» de `RF-ESC-49` y `RF-REG-17` meten el rastro de la evaluación dentro del requerimiento | auditoría | Media |
| 29 | Consistencia (D5) | Dos conjuntos de elegibles para el mismo fin: `RF-TUR-03` omite la indisponibilidad declarada vigente que `RF-ESC-45` sí exige | auditoría | Media |
| 30 | Prioridad (D6) | `RF-TUR-04` y `RF-IAM-19` llevan MUST sin que su ausencia deje una tarea crítica descubierta o un problema crítico sin resolver | auditoría | Baja |

## Acciones recomendadas (priorizadas)

1. **Declarar la indicación médica emitida junto a la cama** (brecha 1). Es lo único que corta el
   escenario de Rodrigo y lo que más pesa: su flujo pasó de 4 a 0 y arrastró 10 puntos de D1. Hoy el
   documento declara la indicación a distancia desde dispositivo personal y no la del médico presente.
2. **Hacer avanzar la cadena cuando hay acuse sin toma del caso** (brechas 5 y 6). Corta el escenario
   de Milagros, deja el segundo tramo abierto de P2 y arrastra su puntaje a 2,4 —la persona más baja
   de la corrida—. Con él se cierra también el registro de la indicación de ese médico.
3. **Separar `RF-ESC-13` de `RF-ESC-49` y fijar un solo corte** (brechas 4 y 26). Un cambio corrige a
   la vez el único RF no conforme de D3, la contradicción que tres de los cuatro agentes marcaron
   Ambigua y el apoyo débil del escenario de Aníbal.
4. **Decidir la guardia localizable simultánea entre sedes** (brecha 10). `RF-TUR-01` CA-2 la admite
   hoy de forma expresa, y es el cruce que la coordinadora no puede detectar a mano.
5. **Abrir el contrato cerrado de `RF-IAM-16`** (brecha 27) para que nombre el acuse, la toma del
   caso, la confirmación de lectura y la aceptación de entrega, que el documento ya reclama de ese
   mismo dispositivo.
6. **Devolver a Aníbal el contexto que se le adjuntó y el silencio de sus indicaciones** (brechas 19 y
   21), que son los dos dolores nuevos suyos que la corrida confirmó sin cobertura.

## Nota sobre la comparabilidad

`Spec/HISTORIAL.MD` separa esta fila de la anterior. it11 e it12 miden el **mismo** `ReqFunc.MD` con
la **misma** rúbrica y dan 72,7 % y 70,0 %: la diferencia no es del requerimiento sino del
instrumento y de la variación entre dos lecturas independientes. Lo que esta corrida sí establece es
que los doce dolores nuevos encontraron brecha —la localizable entre sedes de Carmen, el silencio y
la constancia de Aníbal—, y que el cuello de botella sigue siendo D1.

## Evidencia

`Spec/corridas/2026-08-18-iteracion-12/`
