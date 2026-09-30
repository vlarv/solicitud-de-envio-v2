# Solicitud de envío (SEN)

Documentación del módulo **Solicitud de envío**. Permite pedir varios artículos de un almacén a otro en una sola solicitud, atenderla con envíos parciales o totales y recibirla desde la solicitud o desde FDM.

Este repositorio solo contiene la especificación funcional; no hay código.

## Orden de lectura

1. [Ingeniería conceptual](docs/conceptual_engineering.md): problema, alcance, usuarios y permisos, entradas y salidas, estados de negocio y pantallas.
2. [JTBD-SEN-01 · Solicitar materiales de un almacén para una fecha](docs/jtbd_solicitud_envio_01.md)
3. [JTBD-SEN-02 · Atender una solicitud con envíos totales o parciales](docs/jtbd_solicitud_envio_02.md)
4. [JTBD-SEN-03 · Recibir lo enviado de una solicitud en el almacén de destino](docs/jtbd_solicitud_envio_03.md)

Cada JTBD cierra con sus criterios de aceptación en formato "Si… / Entonces…".

## Prototipo

[Prototipo interactivo](https://claude.ai/artifact/BGymuVPaGiY8VWTcBTXZ4T). Incluye la app móvil (FDM, carrito, to-do list, atender y recibir) y el documento web de la SEN. Es una referencia de comportamiento y apariencia; si hay diferencias con los documentos, mandan los documentos.

## Lo que no está aquí

- **Diseño visual:** se sigue el EDS de la aplicación anfitriona.
- **Especificación técnica:** la define el equipo de desarrollo a partir de estos documentos.
- **Consignación:** el paso a "Por facturar" y "Pendientes por facturar" pertenecen a ese módulo. Ver la compatibilidad requerida en [Estados de negocio](docs/conceptual_engineering.md#estados-de-negocio).

## Mantenimiento

[AGENTS.md](AGENTS.md) define cómo se mantiene esta documentación.
