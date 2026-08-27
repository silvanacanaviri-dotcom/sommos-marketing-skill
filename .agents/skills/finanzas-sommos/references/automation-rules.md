# Automation rules — Finanzas Sommos

## 1. Principio de automatización

Automatizar cálculos y vistas, no duplicar fuentes de verdad.

Fuente primaria:
- movimientos y obligaciones -> `Transacciones`.

Vistas derivadas:
- `CxC`
- `CxP`
- `Estado de pagos`
- `Presupuesto` P&L
- `Bancos` flujos del mes
- `Runway Mensual`
- `Dashboard`

## 2. Categorización

`Transacciones!D` se calcula a partir de `Reglas categorización`.

Comportamiento esperado:
- si descripción vacía -> categoría vacía;
- si Tipo = Transferencia -> `Transferencias internas`;
- si una regla activa coincide por Tipo + palabra/frase + dimensiones opcionales -> usar la categoría de esa regla;
- si no hay coincidencia -> `Por categorizar`.

Las reglas pueden usar Responsable y Moneda como dimensiones opcionales.

No crear una regla genérica que capture movimientos que deberían distinguirse por responsable/moneda.

## 3. Tipo de cambio

Reglas vigentes:
- USD = 1.
- SOL = 0.28 fijo.
- BOB = TCO oficial BCB del día; si no hay dato exacto, usar el último disponible <= fecha.
- otras monedas = valor manual en columna P.

Lógica de `Monto USD`:
- USD -> G.
- BOB -> G / H.
- otras -> G * H.

## 4. CxC

Condición de entrada:
- Tipo = `Ingreso`;
- Estado pago = `Pendiente`;
- Fecha no vacía.

Datos mostrados desde Transacciones:
- Descripción como Cliente/obligación;
- País;
- Moneda;
- Monto original;
- TC;
- Monto USD;
- Fecha como emisión/registro;
- Fecha vencimiento;
- Estado;
- Responsable.

Días vencido:
- si no hay vencimiento -> vacío;
- si TODAY() > vencimiento -> TODAY() - vencimiento;
- si no está vencido -> 0.

No inferir vencimiento cuando el usuario solo proporciona el mes de una factura histórica.

## 5. CxP

Condición equivalente:
- Tipo = `Egreso`;
- Estado pago = `Pendiente`;
- Fecha no vacía.

Al pagar:
- cambiar Estado pago a `Pagado/Cobrado`;
- completar banco/cuenta, conciliación y datos necesarios;
- la obligación debe salir automáticamente de CxP.

## 6. Grants

Categoría: `Other financing cash flow`.

Modelar por desembolso, no por grant total.

Ejemplo conceptual:
- grant aprobado USD 90,000;
- ya recibido USD 30,000;
- desembolso futuro USD 25,000;
- desembolso futuro USD 35,000.

CxC = 25,000 + 35,000, no 60,000 en una sola fila y no 90,000.

Si el desembolso está condicionado a hito y aún no es exigible, registrar solo si el usuario desea proyectarlo como cobro esperado; dejar clara la condición en descripción/comentario cuando exista campo disponible.

## 7. Clientes recurrentes

Si el usuario confirma una mensualidad recurrente:
- crear una fila pendiente por cada mes aún no cobrado;
- conservar moneda contractual;
- no duplicar meses ya conciliados/cobrados;
- usar fecha de vencimiento mensual acordada.

Convención vigente aceptada para recurrencias sin día específico:
- vencimiento = último día del mes.

Facturas históricas:
- usar fecha de emisión basada en el mes informado;
- si no se dio vencimiento, dejarlo vacío.

## 8. Bancos

Solo `Pagado/Cobrado` afecta bancos.

Por fila mensual de Bancos:
- Ingresos mes = suma de ingresos pagados/cobrados que coinciden con cuenta, moneda y mes.
- Egresos mes = suma de egresos pagados que coinciden con cuenta, moneda y mes.
- Saldo final calculado = saldo inicial + ingresos - egresos.
- Diferencia = saldo final real - saldo final calculado.

Estado:
- cierre real vacío -> `Pendiente cierre`;
- abs(diferencia) < 0.01 -> `Conciliado`;
- otro -> `Revisar`.

No usar pendientes para cuadrar bancos.

## 9. Transferencias internas

Transferencias entre cuentas propias:
- Tipo = `Transferencia`;
- Categoría = `Transferencias internas`;
- usar Cuenta origen, Cuenta destino y Detalle transferencia;
- no contarlas como ingreso ni egreso operativo;
- excluirlas de burn.

## 10. Presupuesto / P&L

P&L mensual se calcula desde `Transacciones` con:
- Tipo = Egreso;
- Estado pago = Pagado/Cobrado;
- Fecha dentro del mes.

Para `Salaries`:
- sumar categorías que coincidan con `*salaries`.

Para otras categorías:
- coincidencia exacta con la categoría de la fila.

Variación:
`(P&L - Budget) / Budget`

Formato condicional:
- < 0 -> verde;
- > 0 -> rojo.

No usar egresos Pendiente como P&L ejecutado.

## 11. Runway

Por mes proyectado:
- Saldo inicial del primer mes = cierre real anterior.
- Meses siguientes = saldo final del mes previo.
- Cobros esperados = CxC con vencimiento dentro del mes y estado distinto de Cobrado.
- Pagos comprometidos = CxP con vencimiento dentro del mes y estado distinto de Pagado.
- Flujo neto = CxC + Otros ingresos - CxP - Otros egresos.
- Saldo final = saldo inicial + flujo neto.
- Burn proyectado = pagos comprometidos + otros egresos.

Burn histórico promedio:
- tomar hasta los últimos 3 meses reales disponibles antes del mes proyectado;
- filtrar Tipo = Egreso;
- Estado = Pagado/Cobrado;
- excluir Transferencias internas;
- dividir el total por el número de meses con datos.

Runway:
`MAX(0, Saldo final / Burn histórico promedio)`

## 12. Dashboard

Métricas deben usar rangos que cubran toda el área operativa vigente de Transacciones/CxC/CxP.

Cuando se amplíe el rango operativo de Transacciones, revisar fórmulas antiguas que puedan haberse quedado en límites menores.

Cualquier cambio en:
- CxC;
- CxP;
- bancos;
- burn;
- presupuesto;

debe verificarse en Dashboard después de escribir.

## 13. Regla de no duplicación

Antes de crear un registro pendiente:
1. buscar por contraparte/descripción;
2. buscar por mes/vencimiento;
3. buscar por monto/moneda;
4. revisar si ya existe como Pagado/Cobrado;
5. si existe una fila pendiente equivalente, actualizarla en vez de crear otra.

Especial cuidado con:
- facturas mensuales recurrentes;
- grants divididos en desembolsos;
- movimientos bancarios importados que ya estaban cargados manualmente.