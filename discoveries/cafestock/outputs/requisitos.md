# Requisitos candidatos — CafeStock

Derivados de la evidencia. Cada requisito cita su origen. Cero invención: lo que
no está en una entrevista no se enuncia como hecho.

## Funcionales

- **[R-01]** Registrar una venta de forma rápida marcando los productos vendidos.
  - Tipo: funcional
  - Origen: entrevista_02_empleado_ventas.md · Empleado de ventas

- **[R-02]** Descontar/estimar automáticamente el inventario y los insumos al registrar una venta (p. ej. un capuchino descuenta sus insumos principales).
  - Tipo: funcional
  - Origen: entrevista_03_encargado_compras.md · Encargado de compras; entrevista_02_empleado_ventas.md · Empleado de ventas

- **[R-03]** Mantener mínimos de stock por producto/insumo y generar alertas cuando algo está por acabarse.
  - Tipo: funcional
  - Origen: entrevista_01_dueno_cafeteria.md · Dueño; entrevista_02_empleado_ventas.md · Empleado de ventas; entrevista_03_encargado_compras.md · Encargado de compras

- **[R-04]** Mostrar las ventas del día (resumen para el cierre).
  - Tipo: funcional
  - Origen: entrevista_01_dueno_cafeteria.md · Dueño

- **[R-05]** Mostrar los productos más vendidos / reporte de rotación por día y por semana.
  - Tipo: funcional
  - Origen: entrevista_01_dueno_cafeteria.md · Dueño; entrevista_03_encargado_compras.md · Encargado de compras

- **[R-06]** Registrar entradas de inventario (compras) y salidas para que el stock real refleje el consumo.
  - Tipo: funcional
  - Origen: entrevista_03_encargado_compras.md · Encargado de compras

- **[R-07]** Apoyar el cuadre de cierre conciliando ventas registradas con el dinero recibido (efectivo/transferencia).
  - Tipo: funcional
  - Origen: entrevista_02_empleado_ventas.md · Empleado de ventas; entrevista_01_dueno_cafeteria.md · Dueño

- **[R-08]** Sugerir qué comprar a partir del stock actual, los mínimos y el consumo estimado.
  - Tipo: funcional
  - Origen: entrevista_03_encargado_compras.md · Encargado de compras

## No funcionales

- **[R-09]** Registro de venta veloz: usable durante horas pico sin frenar la atención (mínimos datos a llenar).
  - Tipo: no funcional (usabilidad/rendimiento)
  - Origen: entrevista_01_dueno_cafeteria.md · Dueño; entrevista_02_empleado_ventas.md · Empleado de ventas

- **[R-10]** Simplicidad: solución fácil de usar, apta para una cafetería pequeña sin personal técnico.
  - Tipo: no funcional (usabilidad)
  - Origen: entrevista_01_dueno_cafeteria.md · Dueño

- **[R-11]** Las alertas de stock bajo deben ser proactivas (no depender de que alguien mire perchas/refrigeradora).
  - Tipo: no funcional (usabilidad/confiabilidad)
  - Origen: entrevista_02_empleado_ventas.md · Empleado de ventas