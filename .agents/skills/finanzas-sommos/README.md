# Finanzas Sommos Skill

Skill independiente para operar el modelo financiero de SOMMOS.

## Ruta

`.agents/skills/finanzas-sommos/`

## Invocación en Codex

Usar:

`$finanzas-sommos`

Ejemplos:

- `$finanzas-sommos revisa las cuentas por cobrar y dime cuáles están vencidas`
- `$finanzas-sommos carga estas cuentas por pagar sin duplicar movimientos`
- `$finanzas-sommos importa este extracto y concílialo contra Bancos`
- `$finanzas-sommos revisa el presupuesto de agosto y dime qué categorías están sobre presupuesto`
- `$finanzas-sommos actualiza el runway usando los últimos cierres reales`
- `$finanzas-sommos audita el Dashboard contra Transacciones, CxC, CxP y Bancos`

## Fuente principal

Google Sheet:
`Finanzas Sommos — Workflow y Control`

La skill está diseñada para trabajar leyendo la estructura actual antes de escribir. Si el entorno donde se ejecuta no tiene acceso al Google Sheet, no debe inventar datos: debe solicitar el archivo/exportación o indicar qué lectura falta.

## Contenido

- `SKILL.md`: reglas principales.
- `references/sheet-schema.md`: esquema vigente del Sheet.
- `references/automation-rules.md`: automatizaciones y fórmulas conceptuales.
- `references/accounts-receivable.md`: snapshot de grants y clientes por cobrar.
- `references/importacion-extractos.md`: proceso de importación y caso BancoSol julio.
- `references/qa-checklist.md`: checklist de validación después de cambios.

## Mantenimiento

Los datos operativos cambian. Las referencias con montos/fechas son snapshots y deben actualizarse cuando cambien contratos, cobros o estructura del Sheet.

Las reglas estructurales deben mantenerse alineadas con el modelo financiero real.