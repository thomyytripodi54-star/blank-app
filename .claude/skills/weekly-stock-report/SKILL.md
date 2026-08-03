---
name: weekly-stock-report
description: Genera el reporte semanal de inversión — corre el análisis extendido de la watchlist con el subagente market-analyst, elige las 10 mejores oportunidades de la semana, y le pasa ese análisis al subagente report-designer para que arme el email prolijo con accionables y lo deje como borrador en Gmail. Usar cuando el usuario pida el reporte semanal de acciones, o cuando dispare la rutina programada para generarlo.
---

# Weekly Stock Report

Este skill orquesta el sistema de reporte semanal de inversión. Tiene dos subagentes:

1. **market-analyst** — investiga a fondo y recomienda.
2. **report-designer** — traduce eso a un email prolijo con accionables y lo deja como borrador en Gmail.

## Pasos

1. Leé `.claude/skills/weekly-stock-report/watchlist.json` para obtener `universe` (la lista completa de tickers a evaluar), `select_top_n` (cuántos elegir, hoy 10) y `recipient_email`.

2. Invocá al subagente **market-analyst** (Agent tool, `subagent_type: market-analyst`) con un prompt que incluya:
   - La lista completa de tickers del `universe` (con sus notas de mapeo, ej. BA:IRSA → NYSE:IRS).
   - La instrucción de seleccionar las `select_top_n` con mejor perfil riesgo/beneficio *para esta semana*, y producir el análisis extendido de cada una siguiendo su propio framework (fundamental + técnico + catalizadores + riesgos + recomendación con precio objetivo).
   - Corré este agente en foreground (`run_in_background: false`) porque el siguiente paso depende de su resultado.

3. Con el reporte extendido devuelto, invocá al subagente **report-designer** (Agent tool, `subagent_type: report-designer`) pasándole:
   - El texto completo del análisis del market-analyst (no lo resumas vos antes de pasarlo — el designer necesita el detalle completo para condensarlo bien).
   - `recipient_email` como destinatario del borrador.
   - La fecha de esta semana para el subject y el nombre de carpeta de archivo (`reports/<YYYY-MM-DD>/`).
   - También en foreground, ya que necesitás confirmar que el borrador se creó antes de reportarle al usuario.

4. Al terminar, resumile al usuario en 2-3 líneas: qué 10 activos quedaron seleccionados esta semana, la recomendación de mayor convicción, y que el borrador ya está listo en Gmail para revisar y enviar (aclarando que es un borrador, no un envío automático — la integración actual de Gmail solo puede crear borradores).

## Notas operativas

- Si `market-analyst` marca algún ticker como "dato no disponible" en Massive, no lo fuerces — que quede afuera de la selección final y se lo mencione en la sección de excluidos.
- Si el usuario pide correr esto sobre una lista distinta a la del `watchlist.json` (ej. "analizame estos 5 tickers puntuales"), usá esa lista ad hoc en vez de la del archivo, sin tocar el archivo de configuración salvo que el usuario pida explícitamente actualizar la watchlist permanente.
- Para automatizar la ejecución semanal, configurá una Routine (`create_trigger`) que dispare el prompt: *"Ejecutá el skill weekly-stock-report"* con el cron deseado (ej. lunes 8am hora Argentina → `0 11 * * 1` en UTC).
