# Accounts receivable — snapshot de referencia

Fecha del snapshot: 2026-08-27.

Este archivo documenta obligaciones conocidas al momento de crear la skill. NO sustituye la lectura en vivo de `Transacciones`/`CxC`. Si una fila ya fue cobrada o modificada, prevalece el Sheet.

## 1. Grants

Categoría contable/operativa: `Other financing cash flow`.

### INNOVATECH

Monto pactado total: USD 90,000.
Monto recibido informado: USD 30,000.
Saldo pendiente: USD 60,000.

Desembolsos pendientes:
- USD 25,000 — vencimiento/fecha esperada 2026-08-31.
- USD 35,000 — vencimiento/fecha esperada 2026-09-30.

No registrar USD 60,000 como una sola CxC si ya existen los dos desembolsos individuales.

### Startup Perú

Programa finalizado.
Desembolso final pendiente:
- USD 934 — 2026-08-31.

No existen más hitos futuros conocidos después de este desembolso.

### INCOFIN

Programa finalizado el 2026-01-31.
Desembolso pendiente:
- USD 3,945 — vencido desde 2026-01-31.

El usuario no proporcionó explícitamente el país en el mapeo. No inferirlo al crear/editar registros futuros. Si el Sheet contiene un país, verificarlo antes de presentarlo como dato confirmado.

### FIID Guatemala

Monto pactado total: USD 100,000.
Monto recibido informado: USD 29,970.
Saldo pendiente: USD 70,030.

Desembolsos pendientes:
- USD 40,000 — 2026-10-31.
- USD 30,030 — 2027-02-28.

### Total de grants pendiente del snapshot

USD 134,909.

Este total es CxC/grants pendiente, no efectivo disponible.

## 2. Clientes — facturas históricas pendientes

### Banco de Crédito del Perú
- USD 3,672 — Factura de julio 2026.
- Fecha de vencimiento no proporcionada en el mapeo original; no inventarla.

### BCP Perú + Habitat
- USD 3,000 — Factura de febrero 2026.
- Fecha de vencimiento no proporcionada.

### RENDINERO
- USD 1,000 — Factura de junio 2026.
- USD 1,000 — Factura de julio 2026.
- Fecha de vencimiento de estas facturas históricas no proporcionada.
- País no confirmado en el mapeo original.

### Primaa
- USD 1,750 — Factura de junio 2026.
- Fecha de vencimiento no proporcionada.
- País no confirmado en el mapeo original.

## 3. Clientes — cobros recurrentes/futuros conocidos

Convención vigente aceptada: cuando el usuario indicó pago mensual sin día exacto, se utilizó el último día del mes como fecha esperada.

### BancoSol

Moneda contractual: BOB.
Cobro mensual: Bs 17,500.

Julio 2026 ya fue cobrado/conciliado y NO debe duplicarse.

Pendientes conocidos:
- Bs 17,500 — 2026-08-31.
- Bs 17,500 — 2026-09-30.

No existen mensualidades posteriores a septiembre dentro del acuerdo informado.

### Banco de Crédito del Perú

Cobro mensual: USD 3,672 hasta final de 2026.

Pendientes recurrentes conocidos:
- USD 3,672 — 2026-08-31.
- USD 3,672 — 2026-09-30.
- USD 3,672 — 2026-10-31.
- USD 3,672 — 2026-11-30.
- USD 3,672 — 2026-12-31.

La factura de julio por USD 3,672 se mantiene separada como factura histórica pendiente.

### RENDINERO

Cobro mensual: USD 1,000 hasta noviembre 2026.

Pendientes recurrentes conocidos:
- USD 1,000 — 2026-08-31.
- USD 1,000 — 2026-09-30.
- USD 1,000 — 2026-10-31.
- USD 1,000 — 2026-11-30.

Las facturas de junio y julio se mantienen separadas.

### Leads Quiero BCP

Cobro conocido:
- USD 4,000 — 2026-08-31.

## 4. Totales de control del snapshot

Obligaciones de clientes denominadas en USD (sin convertir BancoSol): USD 36,782.

BancoSol pendiente: Bs 35,000 en total.

Con TC BOB 11.57 utilizado por el Sheet para registros del 2026-08-26, Bs 35,000 equivalían aproximadamente a USD 3,025.06.

Por tanto, el bloque de clientes del snapshot era aproximadamente USD 39,807.06 y la CxC total grants + clientes aproximadamente USD 174,716.06.

Estos equivalentes cambian si cambia la fecha/TC aplicable. Nunca hardcodear estos totales en fórmulas o reportes.

## 5. Reglas de actualización

Cuando llegue un cobro:
1. localizar la obligación pendiente exacta;
2. actualizar Estado pago a `Pagado/Cobrado`;
3. registrar cuenta/medio real;
4. conciliar contra extracto;
5. verificar que salga de CxC;
6. verificar Bancos, Runway y Dashboard;
7. evitar crear otra fila de ingreso si eso duplicaría la obligación ya registrada.

Cuando se informe una nueva mensualidad futura:
- agregar solo los meses todavía no registrados;
- mantener cada mes como fila independiente;
- no extender contratos más allá de la fecha final informada sin confirmación.