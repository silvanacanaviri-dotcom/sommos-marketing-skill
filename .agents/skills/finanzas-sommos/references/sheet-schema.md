# Sheet schema — Finanzas Sommos

Snapshot documentado: 2026-08-27. Antes de escribir, leer el Sheet en vivo porque el usuario puede insertar, eliminar o mover columnas.

## Archivo

- Título: `Finanzas Sommos — Workflow y Control`
- Spreadsheet ID: `1RXy19WZMPQePflFaFeIIHnh09BpJbwOnk6Wumw8bW4E`
- Locale: `en_GB`
- Timezone: `America/La_Paz`

## Tabs

1. Config
2. Reglas categorización
3. TC BCB
4. Transacciones
5. Estado de pagos
6. CxC
7. CxP
8. Presupuesto
9. Bancos
10. Runway Mensual
11. Dashboard
12. Importación extractos
13. `_CalcPresupuesto` (oculta/técnica)

## Transacciones

Encabezados vigentes desde fila 3:

| Col | Campo |
|---|---|
| A | Fecha |
| B | Tipo |
| C | País |
| D | Categoría |
| E | Descripción |
| F | Moneda |
| G | Monto original |
| H | TC a USD |
| I | Monto USD |
| J | Cuenta / medio |
| K | Conciliación |
| L | Presupuestado |
| M | Estado pago |
| N | Fecha vencimiento |
| O | Responsable |
| P | TC manual (otras) |
| Q | Cuenta origen |
| R | Cuenta destino |
| S | Detalle transferencia |

Hay columnas adicionales vacías hasta el límite físico de la hoja; no asumir que forman parte del modelo.

Validaciones clave:
- Tipo -> `Config!C4:C6`
- País -> `Config!A4:A...`
- Categoría -> lista de `Config!E...`
- Moneda -> `Config!B4:B...`
- Cuenta / medio -> `Config!N4:N...`
- Conciliación -> `Config!F4:F...`
- Presupuestado -> `Config!L4:L...`
- Estado pago -> `Config!M4:M...`

Fórmulas importantes:
- D: categorización automática por reglas.
- H: TC automático (spill desde H4).
- I: Monto USD automático (spill desde I4).

No escribir manualmente sobre celdas cubiertas por fórmulas spill sin verificar antes.

## CxC

Encabezados vigentes:

| Col | Campo |
|---|---|
| A | Cliente |
| B | País |
| C | Moneda |
| D | Monto factura |
| E | TC a USD |
| F | Monto USD |
| G | Fecha emisión |
| H | Fecha vencimiento |
| I | Estado |
| J | Fecha cobro |
| K | Días vencido |
| L | Responsable |

CxC es una vista derivada de `Transacciones` filtrando `Ingreso + Pendiente`.

La fórmula vigente en A4 construye una matriz con descripción, país, moneda, monto, TC, monto USD, fecha, vencimiento, estado y responsable.

No usar CxC como segunda fuente de escritura.

## CxP

Estructura conceptualmente equivalente a CxC, alimentada desde `Transacciones` con `Egreso + Pendiente`.

Antes de modificar cualquier fórmula de CxP, leer encabezados/formulas en vivo.

## Bancos

Encabezados vigentes conocidos:

| Col | Campo |
|---|---|
| A | Banco / Entidad |
| B | País |
| C | Mes |
| D | Moneda |
| E | Saldo inicial |
| F | TC inicial |
| G | Saldo inicial USD |
| H | Saldo final banco (manual) |
| I | TC cierre |
| J | Saldo final banco USD |
| K | Responsable |
| L | Notas |
| M | Cierre completo (aux) |
| N | Ingresos del mes |
| O | Egresos del mes |
| P | Saldo final calculado |
| Q | Diferencia |
| R | Conciliación auto |

Reglas: N/O solo usan movimientos pagados/cobrados del banco/cuenta, moneda y mes correspondiente.

## Presupuesto

Vista vigente `FINANCIAL CONTROL`:

| Col | Campo |
|---|---|
| A | ACCOUNTS / categoría |
| B | Julio Budget |
| C | Julio P&L |
| D | Julio % |
| E | Julio Resumen |
| F | Agosto Budget |
| G | Agosto P&L |
| H | Agosto % |

Fila 1 contiene encabezados de mes.
Fila 2 contiene subencabezados.
Filas 3:19 contienen categorías de gasto.
Fila 20 contiene Total.

Orden vigente de categorías:
1. Salaries
2. Outsourced services
3. Contingency
4. RH expenses
5. Administrative expenses
6. Travel
7. Innovatech
8. Sales expenses
9. Product expenses
10. Softwares for development
11. Platform cost
12. Bank fees
13. Exchange rate differences
14. Financial expense
15. Taxes
16. Startup Chile
17. Marketing services

`Salaries` suma cualquier categoría de Transacciones que coincida con `*salaries`.
El resto usa coincidencia exacta con el nombre de la categoría.

Variación:
`(P&L - Budget) / Budget`

Convención visual:
- encabezados morado oscuro;
- grupos de categorías en lavanda/celeste/amarillo según diseño vigente;
- % negativo (menor gasto) verde;
- % positivo (sobrepresupuesto) rojo;
- Budget 0 -> N/A.

## Runway Mensual

Encabezados vigentes desde fila 4:

| Col | Campo |
|---|---|
| A | Mes |
| B | Saldo inicial USD |
| C | Cobros esperados CxC |
| D | Otros ingresos USD |
| E | Pagos comprometidos CxP |
| F | Otros egresos USD |
| G | Flujo neto USD |
| H | Saldo final USD |
| I | Burn proyectado USD |
| J | Burn histórico prom. 3M |
| K | Runway (meses) |
| L | Comentario |

Proyección actual: agosto 2026 a julio 2027.

El saldo inicial de agosto toma el cierre real de julio desde Bancos.
CxC y CxP se suman por fecha de vencimiento dentro del mes.
Burn histórico promedio usa hasta 3 meses reales disponibles antes del mes proyectado.

## Config: categorías conocidas

Gastos/operación relevantes:
- Outsourced services
- RH expenses
- Administrative expenses
- Sales expenses
- Travel
- Product expenses
- Softwares for development
- Innovatech
- Platform cost
- Bank fees
- Exchange rate differences
- Financial expense
- Taxes
- Startup Chile
- Marketing services
- Contingency
- IT salaries
- Sales salaries
- Finance salaries
- Operative salaries
- RH salaries

Ingresos relevantes:
- Full new integration incomes
- Extra features development income
- Proof of concept incomes
- Other financing cash flow
- Bank interest earned

Otros:
- Por categorizar
- Transferencias internas

`Salaries` es una categoría agregada de Presupuesto, no necesariamente una categoría de Transacciones.