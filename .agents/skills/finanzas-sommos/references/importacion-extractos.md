# Importación de extractos — Finanzas Sommos

## Flujo estándar

1. Cargar/leer el extracto bancario.
2. Determinar cuenta, moneda y periodo.
3. Extraer por movimiento:
   - Fecha
   - Hora si existe
   - Banco
   - Tipo
   - Moneda
   - Monto
   - Saldo
   - Descripción original
   - ID bancario si existe
4. Llevar primero a `Importación extractos` (staging).
5. Buscar posibles duplicados en `Transacciones`.
6. Marcar estado de duplicado/observación/importar.
7. Importar únicamente filas confirmadas.
8. Conciliar total ingresos, total egresos y saldo final contra el extracto.
9. Verificar Bancos y Dashboard.

## Regla de duplicados

Comparar, en la medida de lo posible:
- banco/cuenta;
- fecha;
- moneda;
- monto;
- descripción/contraparte;
- ID bancario.

Si dos filas tienen mismo monto y fecha pero podrían ser movimientos distintos, no eliminar una sin evidencia adicional.

## BancoSol — julio 2026

Caso validado usado como referencia de QA:
- 36 movimientos detectados.
- Saldo inicial calculado: Bs 3,202.06.
- Ingresos: Bs 43,617.92.
- Egresos: Bs 11,888.82.
- Saldo final: Bs 34,931.16.

El saldo final coincidió con el cierre real registrado en `Bancos`.

Dos ingresos de Bs 17,500 del 31/07/2026 inicialmente se retuvieron por riesgo de duplicidad con filas de prueba de agosto. Después de confirmar que las filas de agosto eran pruebas, se eliminaron esas pruebas y se importaron ambos ingresos reales de julio.

Resultado BancoSol julio:
- ingresos mes: Bs 43,617.92;
- egresos mes: Bs 11,888.82;
- saldo final calculado: Bs 34,931.16;
- diferencia: 0;
- conciliación: `Conciliado`.

Aprendizaje:
- no forzar conciliación eliminando movimientos reales;
- identificar y retirar primero datos de prueba confirmados;
- movimientos de igual importe pueden ser legítimamente distintos;
- el extracto y el saldo final real son controles externos esenciales.

## Cuentas sin movimientos

Si el usuario confirma que una cuenta no tuvo movimientos en un mes:
- no crear transacciones cero;
- no inventar actividad;
- conservar el cierre/saldo según el modelo vigente;
- documentar que no hubo movimientos cuando sea útil para el cierre.

Para julio 2026, el usuario confirmó que Meru y Scotiabank no tuvieron movimientos durante el mes.