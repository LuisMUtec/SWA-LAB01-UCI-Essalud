# Bloqueos — corrida 2026-08-18-iteracion-12

Lo que esta corrida no pudo corregir porque cae fuera de la lista de editables de
[`../_ESQUEMA.MD`](../_ESQUEMA.MD). No se corrigió a escondidas: se reporta aquí.

| # | Qué está desactualizado | Dónde | Por qué no se corrigió |
|---|---|---|---|
| 1 | La sección «Resultado de las once corridas» habla de once y su tabla llega hasta it11 | `README.md`, sección 3 | Una corrida solo puede tocar la aparición del porcentaje en la línea «Resultado del agente», y nada más de ese archivo |
| 2 | El párrafo que reivindica it9 como mejor corrida no menciona it12 | `README.md`, sección 3 | Igual que el anterior |

Ambos se corrigen en una rama aparte, con su propia revisión. La línea «Resultado del agente» sí se
actualizó, porque es lo único de `README.md` que el esquema declara editable durante una corrida, y
el control C1 exige que publique el global de la última corrida.
