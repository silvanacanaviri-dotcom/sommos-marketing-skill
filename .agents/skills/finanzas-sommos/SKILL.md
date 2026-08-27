---
name: finanzas-sommos
description: Opera, audita y mantiene el control financiero de SOMMOS en Google Sheets. Aplica las reglas de Transacciones, CxC/CxP, grants, clientes recurrentes, bancos, tipo de cambio, presupuesto, runway, conciliación e importación de extractos sin duplicar movimientos ni inventar datos.
---

# Finanzas Sommos

## 1. Propósito

Esta skill sirve para operar y auditar el modelo financiero de SOMMOS de forma consistente, trazable y conservadora.

El objetivo es responder con claridad a preguntas como:
- cuánto efectivo real hay;
- cuánto se debe cobrar y pagar;
- qué obligaciones están vencidas;
- cuánto se ejecutó frente al presupuesto;
- cuánto runway existe;
- qué movimientos están conciliados;
- qué datos faltan para cerrar un mes.

No convertir el Google Sheet en un ERP complejo. Mantener una operación simple, auditable y con una sola fuente de verdad.

## 2. Fuente principal

Archivo principal:
- Título: `Finanzas Sommos — Workflow y Control`
- Spreadsheet ID: `1RXy19WZMPQePflFaFeIIHnh09BpJbwOnk6Wumw8bW4E`
- URL: `https://docs.google.com/spreadsheets/d/1RXy19WZMPQePflFaFeIIHnh09BpJbwOnk6Wumw8bW4E/edit`
- Zona horaria: `America/La_Paz`

Pestañas actuales:
- Config
- Reglas categorización
- TC BCB
- Transacciones
- Estado de pagos
- CxC
- CxP
- Presupuesto
- Bancos
- Runway Mensual
- Dashboard
- Importación extractos
- `_CalcPresupuesto` (técnica/oculta; no asumir que sigue siendo necesaria)

## 3. Regla operativa más importante

Antes de modificar el Sheet:
1. Leer metadata del archivo.
2. Leer los encabezados exactos de las pestañas afectadas.
3. Leer fórmulas, validaciones y formato de las filas donde se escribirá.
4. No usar posiciones históricas de columnas si el usuario pudo haber eliminado, movido o insertado columnas.
5. Hacer el cambio mínimo necesario.
6. Verificar valores calculados y buscar errores de fórmula en el rango afectado.
7. Revisar dependencias relevantes: Dashboard, Runway Mensual, CxC, CxP, Bancos y Presupuesto.

Nunca reescribir una pestaña completa si basta con editar unas pocas celdas.

## 4. Fuente de verdad de movimientos

`Transacciones` es la fuente de verdad de movimientos financieros.

No registrar manualmente la misma obligación en CxC/CxP y además en Transacciones.

Reglas:
- `Ingreso + Pendiente` -> aparece automáticamente en `CxC`.
- `Egreso + Pendiente` -> aparece automáticamente en `CxP`.
- Al cambiar a `Pagado/Cobrado`, debe desaparecer de CxC/CxP.
- `Estado de pagos` es una vista derivada, no una segunda fuente de verdad.
- Una factura o cuenta pendiente NO es caja.
- Un pendiente NO cuenta como ingreso realizado ni como egreso pagado.
- Las transferencias entre cuentas propias no son ingreso ni gasto.

Tipos de transacción principales:
- Ingreso
- Egreso
- Transferencia

Estado de pago:
- Pendiente
- Pagado/Cobrado

## 5. Transacciones: estructura y criterios

Usar la estructura vigente descrita en `references/sheet-schema.md`.

Criterios de registro:
- Fecha: fecha de emisión/registro del movimiento u obligación.
- Tipo: Ingreso, Egreso o Transferencia.
- País: solo si está confirmado. No inventarlo.
- Categoría: usar reglas automáticas; si no existe coincidencia válida, usar `Por categorizar`.
- Descripción: suficientemente específica para distinguir factura, mes, desembolso o contraparte.
- Moneda y monto original: conservar la moneda contractual/real.
- Estado pago: `Pendiente` mientras no exista cobro/pago real.
- Fecha vencimiento: registrar la fecha contractual/esperada real. Si el usuario no la dio, dejarla vacía salvo que exista una convención explícitamente aceptada.
- Cuenta/medio y conciliación: solo cuando corresponda a dinero efectivamente movido en banco/cuenta.

Para obligaciones futuras sin movimiento bancario real:
- normalmente dejar `Cuenta / medio` vacío;
- no marcar `Conciliado`;
- no convertirlas en efectivo disponible.

## 6. Categorización automática

La pestaña `Reglas categorización` es la fuente para asignar categorías según descripción, tipo y dimensiones opcionales.

Principios:
- Primera regla activa coincidente gana.
- No inventar categorías.
- Si no hay regla: `Por categorizar`.
- Las transferencias internas deben categorizarse como `Transferencias internas`.

Categorías de ingresos relevantes:
- Full new integration incomes
- Extra features development income
- Proof of concept incomes
- Other financing cash flow
- Bank interest earned

`Other financing cash flow` se usa para grants/desembolsos de financiamiento no operativo.

## 7. Tipo de cambio a USD

Reglas vigentes:
- USD -> TC = 1.
- SOL -> TC = 0.28 fijo, salvo instrucción posterior del usuario.
- BOB -> TCO oficial BCB según fecha de transacción; fines de semana/feriados usan el último valor oficial <= fecha.
- Otras monedas -> usar `TC manual (otras)`.

Conversión:
- USD: `Monto USD = Monto original`.
- SOL y otras monedas multiplicativas: `Monto USD = Monto original * TC`.
- BOB: `Monto USD = Monto original / TC`.

No sobrescribir manualmente cálculos automáticos de TC/Monto USD sin verificar primero las fórmulas spill existentes.

## 8. CxC: principio general

CxC debe representar dinero exigible o esperado con suficiente certeza, no todo el valor teórico de un contrato.

Para clientes:
- registrar cada factura/mes como obligación separada;
- evitar crear una sola CxC agregada cuando existen vencimientos distintos;
- si existe un cobro recurrente, registrar cada mensualidad pendiente individualmente.

Para facturas históricas sin vencimiento informado:
- conservar la fecha de emisión o el cierre del mes informado;
- dejar `Fecha vencimiento` vacía si no fue proporcionada;
- no marcarla como vencida solo por inferencia.

Para recurrencias futuras donde el usuario solo indique el mes y acepte la convención mensual:
- usar el último día del mes como fecha esperada de cobro.

## 9. Grants

Regla central:
`Monto total aprobado del grant != CxC actual`.

Un grant puede tener saldo contractual pendiente, pero solo registrar como CxC los desembolsos que ya sean exigibles o suficientemente definidos por monto y fecha/hito.

Cada desembolso debe ser una fila independiente en Transacciones como:
- Tipo: Ingreso
- Categoría: Other financing cash flow
- Estado pago: Pendiente
- Monto: desembolso específico
- Fecha vencimiento: fecha esperada/contractual del desembolso

Cuando se cobre:
- cambiar a `Pagado/Cobrado`;
- completar cuenta/medio y conciliación según el movimiento bancario real;
- no duplicar el cobro como una nueva transacción si la obligación pendiente ya existe, salvo que el modelo vigente requiera una conciliación explícita y se preserve trazabilidad.

Consultar `references/accounts-receivable.md` para el snapshot vigente conocido de grants y clientes.

## 10. CxP

CxP sigue la misma lógica de una sola fuente de verdad:
- registrar la obligación en Transacciones como `Egreso + Pendiente`;
- completar fecha vencimiento real;
- al pagar, cambiar a `Pagado/Cobrado` y conciliar contra banco/cuenta.

No crear una fila manual paralela directamente en CxP.

## 11. Aprobaciones de egresos

Regla operativa provisional:
- < USD 300: Líder.
- USD 300 a 1,999.99: Líder + Finanzas.
- >= USD 2,000: Gerencia.

Siempre escalar a Gerencia cuando corresponda a:
- proveedor nuevo;
- contrato nuevo;
- inversión estratégica;
- compromiso material no recurrente.

## 12. Bancos y conciliación

La pestaña `Bancos` controla cierres mensuales por cuenta y moneda.

Principios:
- Saldo inicial del mes = cierre real del mes anterior.
- Saldo final banco = dato manual real del extracto/cierre.
- Solo movimientos `Pagado/Cobrado` afectan flujo bancario.
- Pendientes no afectan saldo bancario.
- Saldo calculado = saldo inicial + ingresos pagados/cobrados - egresos pagados.
- Diferencia = saldo final real - saldo final calculado.

Estado automático esperado:
- sin cierre real -> `Pendiente cierre`;
- |diferencia| < 0.01 -> `Conciliado`;
- de lo contrario -> `Revisar`.

El Dashboard debe usar cierres reales completos para caja disponible, no saldos provisionales.

## 13. Presupuesto

La vista vigente es un `FINANCIAL CONTROL` por categorías y meses.

Estructura conceptual:
- columna A: categoría;
- por cada mes: Budget / P&L / %;
- puede existir columna `Resumen` para comentarios del mes;
- `P&L` se calcula automáticamente desde Transacciones con `Egreso + Pagado/Cobrado`;
- `Salaries` consolida todas las categorías que terminan/contienen `salaries`.

Fórmula de variación:
`(P&L - Budget) / Budget`

Interpretación:
- negativo = se gastó menos de lo presupuestado -> verde;
- positivo = se gastó más de lo presupuestado -> rojo;
- Budget = 0 -> mostrar N/A, no error.

Mantener el orden y colores vigentes del control financiero salvo que el usuario pida rediseño.

No desagregar `Salaries` en Presupuesto aunque Transacciones conserve el detalle de IT, Sales, Finance, Operative y RH salaries.

## 14. Runway

`Runway Mensual` debe partir de caja real y combinarla con flujos esperados de CxC/CxP.

Reglas:
- el saldo inicial del primer mes proyectado toma el cierre real del mes anterior;
- cobros esperados provienen de CxC con vencimiento en el mes;
- pagos comprometidos provienen de CxP con vencimiento en el mes;
- burn histórico = promedio de hasta los últimos 3 meses reales disponibles antes del mes proyectado;
- burn usa solo `Egreso + Pagado/Cobrado` y excluye `Transferencias internas`;
- runway = saldo final proyectado / burn histórico, con mínimo 0;
- CxC futura puede formar parte de la proyección mensual, pero nunca debe presentarse como caja actual.

Cuando se carguen nuevas obligaciones o cobros esperados, verificar que Runway Mensual se actualice correctamente.

## 15. Dashboard

El Dashboard debe distinguir:
- Cash disponible real.
- CxC pendiente.
- CxC vencida.
- CxP pendiente.
- Ingresos último mes con datos.
- Burn último mes con datos.
- Runway.
- Presupuesto disponible/control presupuestario.

Nunca mezclar CxC con cash disponible.

Antes de citar métricas, leer valores en vivo; no usar montos históricos memorizados.

## 16. Importación de extractos

La pestaña `Importación extractos` se usa como staging.

Flujo:
1. Parsear movimientos del extracto.
2. Identificar banco, fecha, moneda, monto, saldo, descripción e ID bancario si existe.
3. Marcar posibles duplicados antes de importar.
4. Importar solo movimientos confirmados.
5. Conciliar totales de ingresos, egresos y saldo final contra el extracto.
6. No inventar movimientos faltantes para forzar una conciliación.

Consultar `references/importacion-extractos.md` para el caso de BancoSol julio 2026 y reglas aprendidas.

## 17. Seguridad y QA

Después de cualquier modificación:
- verificar que no existan `#REF!`, `#VALUE!`, `#N/A` inesperados ni errores de spill;
- revisar fórmulas de las filas afectadas;
- comprobar que CxC/CxP derivadas reflejen el cambio;
- revisar Bancos si hubo movimiento pagado/cobrado;
- revisar Presupuesto si hubo egreso realizado;
- revisar Runway/Dashboard si se alteraron caja, CxC, CxP o burn;
- comprobar que los totales relevantes cuadren con la fuente externa cuando exista extracto/contrato/listado.

Nunca corregir silenciosamente un dato contractual dudoso. Señalar la inconsistencia y pedir confirmación cuando sea material.

## 18. Respuesta al usuario

Responder en español, de forma práctica y ejecutiva.

Cuando se realicen cambios:
- indicar qué se modificó;
- indicar qué quedó automático;
- indicar cualquier supuesto o dato no confirmado;
- si corresponde, mostrar el total resultante;
- evitar afirmar que una métrica está actualizada si no fue leída después del cambio.

## 19. Archivos de referencia

Leer cuando sean relevantes:
- `references/sheet-schema.md`
- `references/automation-rules.md`
- `references/accounts-receivable.md`
- `references/importacion-extractos.md`
- `references/qa-checklist.md`

Si una referencia contradice el Google Sheet actual, prevalece la lectura en vivo del Sheet y luego debe actualizarse la documentación.