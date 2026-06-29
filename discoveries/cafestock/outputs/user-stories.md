# User stories — CafeStock

Historias en formato INVEST con criterios de aceptación y fuente. Ordenadas por
prioridad: primero el núcleo de valor (registrar venta → descontar insumos →
alertar antes del quiebre), que es lo que más duele y más se repite entre las tres
personas.

## Núcleo del MVP

- **[US-01]** Como empleado de ventas, quiero marcar los productos vendidos en pocos toques para registrar la venta sin frenar la atención en hora pico.
  - Criterios de aceptación:
    - Dado un producto del catálogo, cuando lo marco como vendido, entonces la venta queda registrada con fecha/hora en pocos segundos y sin formularios largos.
    - Dado que estoy atendiendo, cuando registro una venta, entonces no necesito salir de la pantalla de venta para completar el registro.
  - Fuente: entrevista_02_empleado_ventas.md, entrevista_01_dueno_cafeteria.md

- **[US-02]** Como encargado de compras, quiero que al registrar una venta se descuenten automáticamente los insumos principales de ese producto para que el inventario refleje el consumo real sin anotar salidas a mano.
  - Criterios de aceptación:
    - Dado un producto con receta de insumos definida (p. ej. capuchino → leche, café), cuando se registra su venta, entonces el stock de esos insumos disminuye según la receta.
    - Dado un producto sin receta, cuando se vende, entonces se descuenta como unidad simple (p. ej. un pan) y el sistema no falla.
  - Fuente: entrevista_03_encargado_compras.md, entrevista_02_empleado_ventas.md

- **[US-03]** Como empleado de ventas, quiero recibir una alerta cuando un insumo o producto baja de su mínimo para no enterarme cuando ya se acabó.
  - Criterios de aceptación:
    - Dado un insumo con mínimo configurado, cuando su stock cae por debajo del mínimo, entonces el sistema muestra una alerta visible sin que yo tenga que revisar perchas o refrigeradora.
    - Dado un insumo en alerta, cuando consulto el listado, entonces aparece destacado como "bajo stock".
  - Fuente: entrevista_02_empleado_ventas.md, entrevista_01_dueno_cafeteria.md, entrevista_03_encargado_compras.md

## Soporte cercano al núcleo

- **[US-04]** Como dueño de cafetería, quiero ver el resumen de ventas del día para cuadrar el cierre sin reconstruir todo de memoria.
  - Criterios de aceptación:
    - Dado el cierre del día, cuando abro el resumen, entonces veo el total vendido y el detalle por producto del día.
  - Fuente: entrevista_01_dueno_cafeteria.md, entrevista_02_empleado_ventas.md

- **[US-05]** Como encargado de compras, quiero definir un mínimo de stock por insumo para que las alertas se disparen a tiempo según mi experiencia del negocio.
  - Criterios de aceptación:
    - Dado un insumo, cuando configuro su mínimo, entonces el sistema usa ese umbral para generar alertas (US-03).
  - Fuente: entrevista_03_encargado_compras.md

- **[US-06]** Como encargado de compras, quiero registrar las entradas de inventario (compras recibidas) para que el stock se reponga y las alertas vuelvan a su estado normal.
  - Criterios de aceptación:
    - Dado un insumo, cuando registro una entrada por una cantidad, entonces su stock aumenta en esa cantidad y, si supera el mínimo, deja de estar en alerta.
  - Fuente: entrevista_03_encargado_compras.md

## Posterior al MVP (valiosas, pero no imprescindibles para el núcleo)

- **[US-07]** Como dueño de cafetería, quiero ver los productos más vendidos por día y por semana para decidir mejor qué preparar y comprar.
  - Criterios de aceptación:
    - Dado un rango de fechas, cuando consulto el reporte de rotación, entonces veo los productos ordenados por cantidad vendida.
  - Fuente: entrevista_01_dueno_cafeteria.md, entrevista_03_encargado_compras.md

- **[US-08]** Como encargado de compras, quiero una sugerencia de qué comprar a partir del stock, los mínimos y el consumo estimado para evitar compras de emergencia y sobrecompra.
  - Criterios de aceptación:
    - Dado el estado de inventario, cuando abro la sugerencia de compra, entonces veo una lista de insumos a reponer con una cantidad sugerida.
  - Fuente: entrevista_03_encargado_compras.md
