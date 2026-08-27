# QA checklist — Finanzas Sommos

Usar este checklist después de cambios en el Google Sheet.

## Antes de escribir

- [ ] Confirmar spreadsheet correcto por ID/título.
- [ ] Leer metadata y pestañas actuales.
- [ ] Leer encabezados exactos de las pestañas afectadas.
- [ ] Leer fórmulas y validaciones de filas cercanas.
- [ ] Identificar si existen fórmulas spill.
- [ ] Buscar duplicados del movimiento/obligación a registrar.
- [ ] No asumir país, fecha de vencimiento, categoría o cuenta si no están confirmados.

## Transacciones

- [ ] Tipo correcto: Ingreso/Egreso/Transferencia.
- [ ] Moneda original correcta.
- [ ] Monto original correcto.
- [ ] TC automático/manual según regla.
- [ ] Monto USD calculado correctamente.
- [ ] Estado pago correcto.
- [ ] Pendiente no marcado como Conciliado.
- [ ] Transferencia interna no clasificada como ingreso/egreso.

## CxC

- [ ] Cada obligación pendiente aparece una sola vez.
- [ ] Grants separados por desembolso.
- [ ] Recurrencias separadas por mes.
- [ ] Facturas históricas sin vencimiento informado mantienen vencimiento vacío.
- [ ] CxC cobrada desaparece al cambiar a Pagado/Cobrado.
- [ ] CxC pendiente no incrementa cash disponible.
- [ ] Días vencido solo se calcula cuando existe fecha de vencimiento.

## CxP

- [ ] Egreso pendiente aparece en CxP.
- [ ] Pago real elimina la obligación de CxP.
- [ ] No existe una copia manual paralela.

## Bancos

- [ ] Solo Pagado/Cobrado afecta ingresos/egresos del mes.
- [ ] Cuenta y moneda coinciden.
- [ ] Saldo inicial corresponde al cierre anterior.
- [ ] Saldo final real proviene del extracto/cierre.
- [ ] Diferencia calculada correctamente.
- [ ] Conciliación = Conciliado solo si diferencia < 0.01 en valor absoluto.

## Presupuesto

- [ ] P&L usa solo Egreso + Pagado/Cobrado.
- [ ] Salaries consolida todas las categorías `*salaries`.
- [ ] No aparecen salaries duplicados por área en la vista presupuestaria.
- [ ] Variación = (P&L - Budget) / Budget.
- [ ] Menor gasto -> porcentaje negativo/verde.
- [ ] Sobrepresupuesto -> porcentaje positivo/rojo.
- [ ] Budget 0 -> N/A sin error.
- [ ] Total mensual cuadra con suma de categorías.

## Runway

- [ ] Primer saldo inicial usa cierre real anterior.
- [ ] CxC se incorpora por vencimiento del mes, no como cash actual.
- [ ] CxP se incorpora por vencimiento del mes.
- [ ] Burn excluye Transferencias internas.
- [ ] Burn usa Pagado/Cobrado.
- [ ] Promedio histórico usa hasta 3 meses reales disponibles.
- [ ] Runway no devuelve valor negativo.

## Dashboard

- [ ] Cash disponible = caja real, no CxC.
- [ ] CxC pendiente cuadra con la vista CxC.
- [ ] CxC vencida cuadra con vencimientos reales.
- [ ] CxP pendiente cuadra con CxP.
- [ ] Ingresos realizados no incluyen pendientes.
- [ ] Burn refleja datos reales.
- [ ] Runway refleja Runway Mensual.
- [ ] Fórmulas cubren el rango operativo vigente de Transacciones.

## Errores de fórmula

Buscar después de cambios:
- [ ] `#REF!`
- [ ] `#VALUE!`
- [ ] `#DIV/0!`
- [ ] `#N/A` inesperado
- [ ] errores de ARRAY/MAP/spill

## Cierre de tarea

Antes de responder al usuario:
- [ ] releer valores resultantes;
- [ ] confirmar el total relevante;
- [ ] mencionar datos que quedaron sin confirmar;
- [ ] no citar métricas antiguas si no fueron leídas en vivo después del cambio;
- [ ] enlazar el Sheet cuando sea útil.