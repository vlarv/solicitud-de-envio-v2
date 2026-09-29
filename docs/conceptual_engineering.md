# Ingeniería conceptual — Solicitud de envío

## **El problema**

Los usuarios que necesitan materiales en un almacén distinto de aquel donde se encuentran deben pedirlos, y el almacén de origen debe despacharlos y el de destino recibirlos. La solución actual solo permite gestionar un artículo por solicitud, por lo que abastecer una necesidad real, que casi siempre involucra varios artículos, obliga a crear, atender y recibir cada artículo por separado.

Esta operación artículo por artículo multiplica el trabajo de solicitantes y almaceneros, fragmenta el seguimiento de lo que se pidió, envió y recibió para una misma necesidad, y dificulta saber cuándo esa necesidad quedó cubierta. El problema debe resolverse porque el abastecimiento entre almacenes sostiene la operación de producción y su lentitud o falta de trazabilidad retrasa la atención de fechas y órdenes de trabajo.

## **Contexto: cómo y por qué el usuario llega al producto o módulo**

- **Solicitante:** llega cuando necesita materiales en un almacén de su zona de influencia para una fecha o una orden de trabajo (OT). Arma su solicitud desde el carrito de solicitud, al que accede desde Flujo de Materiales (FDM), o agregando artículos desde su detalle al consultar las existencias de un almacén de inventario, dentro o fuera de su zona.
- **Usuario de origen:** llega desde la tarjeta de la solicitud en el to-do list de almacén, pantalla existente de la aplicación anfitriona donde se concentran sus tareas pendientes.
- **Usuario de destino:** llega desde la misma tarjeta de la solicitud en el to-do list, visible desde que la solicitud se crea. También llega desde la pestaña "En tránsito" de FDM, donde lo enviado se ve como cualquier artículo en tránsito.
- **Cualquier usuario:** consulta una solicitud desde el repositorio de documentos de la web, donde cada artículo de la solicitud aparece como una fila y todas abren el mismo documento.

## Alcance

Flujo cubierto, de inicio a fin:

1. **Consulta de existencias:** el solicitante consulta los artículos disponibles y no reservados en almacenes de inventario, inicialmente filtrados por su zona de influencia y sus almacenes, con la opción de ver fuera de su zona.
2. **Solicitud:** el solicitante arma en su único carrito de solicitud uno o varios artículos, todos desde un único almacén de origen hacia un único almacén de destino de su zona de influencia, distinto del origen, e indica una fecha requerida o una OT. Puede armarlo desde el carrito, indicando primero fecha u OT, origen y destino y eligiendo entre las existencias del origen, o agregando artículos a pendiente desde su detalle. Si agrega un artículo de otro origen o hacia otro destino, confirma que descarta el borrador anterior y empieza uno nuevo. Mientras no lo envía, el carrito se conserva como borrador. Desde el detalle de un artículo también puede solicitar solo ese artículo de inmediato, sin afectar su borrador. Las cantidades se solicitan en unidad de uso.
3. **Publicación en el to-do list:** cada solicitud aparece como una sola tarjeta para los usuarios de origen y para los de destino. Si la zona de un usuario incluye ambos almacenes, la tarjeta le muestra lo que falta recibir y lo que falta enviar, y al abrirla elige Enviar o Recibir.
4. **Atención:** un usuario de origen atiende la solicitud mediante uno o varios envíos, totales o parciales. Un envío es lo que confirma en una sola acción y puede incluir varios artículos: cada CU escaneado y los artículos sin CU en unidades de inventario (por ejemplo, sacos). Puede enviar hasta la tolerancia de despacho por encima de lo solicitado. Tras un envío parcial, la solicitud permanece abierta para envíos posteriores. Puede truncar la solicitud cuando no se completará y rechazarla antes de haber enviado.
5. **Tránsito:** lo enviado queda en tránsito. Al atender y recibir, el usuario trabaja sobre la solicitud y lo que tiene en tránsito, sin distinguir envíos; los envíos se consultan desde otra parte, que se definirá aparte.
6. **Recepción:** un usuario de destino recibe, total o parcialmente, lo que está en tránsito de la solicitud: desde la solicitud, escaneando cada CU, o el SKU con su cantidad en unidades de inventario, y una ubicación del almacén de destino; o desde FDM, en el detalle de cada artículo en tránsito, indicando cantidad y ubicación. En ambos casos puede rechazar lo que sigue en tránsito, que vuelve al origen.
7. **Cierre:** la solicitud queda Recibida cuando se recibe el 100 % de lo solicitado de cada artículo. Si no se completará, el origen la trunca. También termina cuando se rechaza o se cancela. No se envían correos.
8. **Consulta del documento:** cualquier usuario consulta la solicitud en el repositorio de documentos de la web: sus datos, lo solicitado, enviado y recibido por artículo, y quién y cuándo la rechazó, truncó o canceló. Ahí también se registran anotaciones de uso interno. Todas las acciones de la solicitud se hacen en la app.

## Fuera de alcance

Este módulo usa estas piezas, pero no las construye ni las cambia:

- **Inventario:** el saldo por almacén y ubicación, y el registro de los movimientos. Este módulo le pide mover el inventario (salida a tránsito, retorno al origen, ingreso al destino) y usa lo que responde.
- **Mover inventario sin solicitud (FDM):** la reubicación libre entre almacenes sigue como hoy.
- **Reservas:** reservar inventario sigue como hoy; este módulo solo lee cuánto está reservado.
- **Consumo al recibir (FDM):** FDM decide, según el maestro de FyE, si lo recibido se consume o ingresa a inventario. No cambia.
- **Acciones masivas y reimpresión de etiquetas (FDM):** siguen como hoy.
- **To-do list de almacén (aplicación anfitriona):** la pantalla ya existe; este módulo solo agrega y quita sus tarjetas.
- **Detalle de artículo en tránsito (FDM):** los datos que vienen de otros módulos, como la fecha de creación, siguen como hoy. Este módulo solo agrega el aviso de la SEN y las acciones Recibir y Rechazar.
- **Repositorio de documentos (web):** la lista, la búsqueda, los filtros y el marco del documento ya existen; este módulo solo aporta las filas de sus solicitudes y el contenido del documento.
- **Consignación:** el paso de Recibida a Por facturar, "Enviar a proveedor", "Pendientes por facturar" y la facturación con Compras pertenecen a ese módulo. Este módulo solo debe ser compatible con él (ver Estados de negocio).
- **Consulta de envíos e historial de solicitudes cerradas:** se definirán aparte.

## Usuarios y permisos

| Usuario | Relación con el problema | Permisos |
| --- | --- | --- |
| Solicitante | Necesita materiales en un almacén de su zona para una fecha u OT. | Consultar existencias dentro y fuera de su zona; crear solicitudes solo hacia almacenes de su zona de influencia, incluidos los almacenes de máquina de las OT que vincula; cancelar sus propias solicitudes mientras no haya nada en tránsito. |
| Usuario de origen | Debe despachar lo solicitado desde su almacén. | Cualquier usuario cuya zona de influencia incluya el almacén de origen: ver la tarjeta de la solicitud; enviar total o parcialmente; truncar la solicitud; rechazarla antes de enviar. |
| Usuario de destino | Debe ingresar lo enviado en su almacén. | Cualquier usuario cuya zona de influencia incluya el almacén de destino: ver la tarjeta de la solicitud desde que se crea; recibir desde la solicitud o desde FDM; rechazar lo que sigue en tránsito. |

Cualquier usuario puede consultar las solicitudes en el repositorio de documentos, independientemente del almacén, y editar sus anotaciones de uso interno. No existen roles diferidos.

## Entradas

| Entrada | Fuente | Propósito de negocio |
| --- | --- | --- |
| Existencias por almacén y ubicación, con su estado (por ejemplo, Disponible), cantidad reservada y códigos únicos (CU) | Inventario | Mostrar qué se puede solicitar y desde dónde se puede enviar. Solo lo disponible y no reservado es solicitable. |
| Unidad de uso, unidad de inventario y unidad de compra de cada SKU | Maestro de bienes y servicios (MBS) | Se solicita en unidad de uso (por ejemplo, kg). Se envía y recibe en unidades de inventario: CU completos, o sacos, baldes o paquetes. La unidad de compra es informativa. |
| Almacenes de inventario, indicando si son de consignación y de qué proveedor | Almacenes | Restringir los orígenes de una solicitud. Si el origen es un almacén de consignación, la solicitud es de consignación y su proveedor es el del almacén. |
| Zonas de influencia de cada usuario | Gestor de zonas de influencia | Determinar destinos permitidos y qué usuarios ven cada tarjeta. |
| Regla de consumo por artículo | Maestro de FyE | FDM la usa para decidir si lo recibido se consume o ingresa a inventario. |
| Órdenes de trabajo, con su estado, fecha programada y máquina | Producción | Vincular la solicitud a una OT abierta o programada cuya máquina esté en la zona de influencia del solicitante; de ella se derivan el destino (el almacén de su máquina) y la fecha requerida, que permanece ligada a la OT. |
| Tolerancia de despacho | Configuración del sistema (sin pantalla; se cambia en base de datos) | Porcentaje que se puede enviar por encima de lo solicitado de cada artículo. Es una variable configurable, con valor inicial 5 %. |
| Artículos, cantidades, origen, destino y fecha requerida u OT | Solicitante | Definir la solicitud. |
| CU escaneado, o SKU con ubicación y cantidad en unidades de inventario | Usuario de origen | Definir cada envío. |
| CU escaneado, o SKU con cantidad en unidades de inventario, y ubicación de recepción | Usuario de destino | Registrar qué y dónde ingresa. |
| Anotaciones de uso interno | Cualquier usuario | Dejar notas sobre la solicitud en su documento. |

## Salidas

- Solicitudes de envío con sus artículos, cantidades solicitadas, enviadas y recibidas, y su estado.
- Envíos asociados a cada solicitud, con sus líneas y cantidades enviadas, recibidas y rechazadas.
- Una tarjeta por solicitud en el to-do list de almacén, publicada y cerrada según la zona de influencia.
- Una fila por artículo de cada solicitud en el repositorio de documentos, y el documento de la solicitud.
- Movimientos registrados en inventario: salida del origen hacia tránsito al enviar, retorno al origen al rechazar, e ingreso o consumo al recibir.
- Trazabilidad completa de quién solicitó, envió, recibió, truncó, rechazó o canceló, y cuándo, y de quién editó por última vez las anotaciones.

## ¿Qué hace el usuario?

- **Solicitante:** consulta existencias, arma una solicitud con uno o varios artículos del mismo almacén de origen, elige el destino y la fecha u OT, la envía, sigue su avance desde la tarjeta de la solicitud y, si ya no la necesita, la cancela mientras no haya nada en tránsito.
- **Usuario de origen:** abre la tarjeta de la solicitud, arma cada envío escaneando cada CU, o el SKU para luego elegir ubicación y cantidad en unidades de inventario; ve lo que ya envió y su estado; confirma envíos parciales o totales, y trunca o rechaza la solicitud cuando corresponde.
- **Usuario de destino:** abre la tarjeta de la solicitud, escanea cada CU, o el SKU con su cantidad en unidades de inventario, y la ubicación de recepción, antes o después; confirma recepciones totales o parciales, y rechaza lo que sigue en tránsito cuando corresponde. También puede recibir o rechazar cada artículo en tránsito desde FDM.
- **Cualquier usuario:** abre el documento de la solicitud en el repositorio de la web para consultarla y edita sus anotaciones de uso interno.

## ¿Qué hace el sistema por el usuario?

- Filtra inicialmente las existencias por la zona de influencia del usuario y sus almacenes, y agrega la información del artículo a nivel de todo el almacén al solicitar.
- Limita los orígenes a almacenes de inventario y los destinos a almacenes de la zona del solicitante distintos del origen.
- Mantiene un único carrito en borrador por solicitante con un solo origen y destino. Si el solicitante agrega un artículo de otro origen o hacia otro destino, le pide confirmar que descarta el borrador anterior y empieza uno nuevo con ese artículo.
- Propone la fecha de hoy como fecha requerida y fija el destino cuando la zona del solicitante tiene un solo almacén.
- Cuando se vincula una OT, fija como destino el almacén de su máquina y mantiene la fecha requerida ligada a la OT: si la OT se reprograma, la fecha de la solicitud cambia con ella. Si la máquina de la OT cambia después de enviada la solicitud, el destino no cambia.
- Trunca automáticamente una solicitud Pendiente o Enviada cuando su OT vinculada se cierra o se anula. Lo que está en tránsito sigue su ciclo, y el destino ve un aviso de que la OT se cerró o anuló para decidir si recibe o rechaza.
- Acepta solo cantidades enteras y positivas: en unidad de uso al solicitar y en unidades de inventario al enviar y recibir.
- Revalida la disponibilidad no reservada al enviar la solicitud y bloquea el envío hasta que el solicitante ajuste o quite los artículos que la superan.
- Muestra una sola tarjeta por solicitud en el to-do list, según la zona de influencia: al origen mientras quede algo por enviar; al destino desde que la solicitud se crea (con 0 por recibir hasta el primer envío) y mientras quede algo en tránsito o por enviar. Si el usuario puede enviar y recibir, la tarjeta muestra "por recibir // por enviar" y al abrirla le pide elegir.
- Propone como cantidad a enviar, en unidades de inventario, la que cubre lo pendiente sin superar lo disponible en la ubicación ni lo solicitado más la tolerancia de despacho.
- Trata cada CU como una unidad independiente que se envía y recibe completa.
- Da un artículo por cubierto solo cuando se envió el 100 % de lo solicitado. La solicitud queda Recibida cuando se recibe el 100 % de cada artículo; si no se completará, el origen debe truncarla.
- Mantiene la solicitud vigente mientras exista cantidad pendiente y vuelve a dejar pendiente lo que se rechaza en tránsito, salvo que la solicitud esté Trunca.
- Valida cada escaneo del envío contra la solicitud, la disponibilidad no reservada del origen y el máximo permitido, y revalida lo pendiente al confirmar el envío.
- En la recepción, reúne todo lo que está en tránsito de la solicitud sin distinguir envíos y reparte internamente las unidades recibidas entre las líneas de envío.
- Muestra en FDM lo que está en tránsito de una solicitud como cualquier artículo en tránsito (una fila por CU). En su detalle, avisa que pertenece a la solicitud con acceso directo a ella; recibir o rechazar ahí tiene el mismo efecto que hacerlo desde la solicitud.
- Evita recibir dos veces lo mismo: si otro usuario ya recibió un ítem, desde FDM o desde la solicitud, lo descarta de la confirmación y avisa.
- Publica cada solicitud, desde que está Pendiente, en el repositorio de documentos con una fila por artículo; todas las filas abren el mismo documento. El borrador no aparece.
- En el documento, muestra lo solicitado en unidad de uso y lo enviado y recibido en unidad de uso con las piezas debajo (por ejemplo, "240.10 kg · 2 bobinas"), sin detallar envíos. Si la solicitud está Rechazada, Trunca o Cancelada, muestra un aviso con quién y cuándo; si se truncó por la OT, el autor es el sistema.
- Guarda en las anotaciones de uso interno quién las editó por última vez y cuándo.
- Solicita confirmación antes de cada acción irreversible y solo ofrece las acciones disponibles en ese momento.
- Registra en inventario los movimientos resultantes y muestra el resultado que FDM determine, incluido el consumo al recibir.

## Módulos

El producto tiene un único módulo, `SEN` (Solicitud de envío). Este documento también sirve como su especificación de módulo.

## **Estados de negocio**

### Solicitud

| Estado | Significado operativo | Transiciones y disparadores | Acciones permitidas | Acciones prohibidas |
| --- | --- | --- | --- | --- |
| Borrador | Carrito en armado; solo lo ve su creador y no se publica en el to-do list ni en el repositorio de documentos. Cada solicitante tiene como máximo uno. | → Pendiente cuando el solicitante lo envía y supera la revalidación de disponibilidad. Eliminarlo o reemplazarlo, previa confirmación, lo descarta. | Agregar, editar y quitar artículos; cambiar fecha u OT, origen y destino; enviar; eliminar. | Atender; publicar en el to-do list. |
| Pendiente | Enviada por el solicitante, desde el borrador o como solicitud directa de un artículo, sin envíos. Visible como tarjeta en el to-do list para el origen y el destino, y no como card en la lista de existencias de FDM. | → Enviada al confirmarse el primer envío; → Rechazada si el origen la rechaza; → Cancelada si el solicitante la cancela; → Trunca automáticamente si su OT vinculada se cierra o se anula. | Enviar y rechazar (origen); cancelar (creador). | Truncar manualmente. |
| Enviada | Tiene al menos un envío y aún queda cantidad pendiente de enviar o algo en tránsito. | → Recibida cuando se recibe el 100 % de lo solicitado de cada artículo; → Trunca si el origen la trunca o, automáticamente, si su OT vinculada se cierra o se anula; → Cancelada si el solicitante la cancela sin nada en tránsito. | Enviar lo pendiente y truncar (origen); recibir y rechazar lo que está en tránsito (destino); cancelar (creador, sin nada en tránsito). | Rechazar la solicitud; enviar más de lo solicitado más la tolerancia de despacho. |
| Recibida | Se recibió el 100 % de lo solicitado de cada artículo. | Estado final para este módulo. Si el origen es un almacén de consignación, Consignación puede pasarla a Por facturar. | Consultar. | Cualquier modificación desde este módulo. |
| Trunca | Finalizada anticipadamente con lo ya enviado, por el origen o por el cierre o anulación de su OT; no admite nuevos envíos. | Estado final. Lo que está en tránsito sigue su ciclo; si fue por la OT, el destino ve un aviso. | Consultar; recibir o rechazar lo que está en tránsito. | Nuevos envíos. |
| Rechazada | El origen no la atenderá. | Estado final. | Consultar. | Cualquier modificación. |
| Cancelada | El solicitante ya no la necesita; deja de estar disponible para atención. | Estado final. | Consultar. | Cualquier modificación. |
| Por facturar (Consignación) | Solicitud de consignación recibida y enviada a su proveedor para facturar. Lo gestiona Consignación. | → Por facturar desde Recibida cuando Consignación ejecuta "Enviar a proveedor", solo si el origen es un almacén de consignación. Sus transiciones siguientes pertenecen a Consignación. | Las que defina Consignación. | Cualquier acción de este módulo. |

Rechazar, truncar y cancelar se aplican siempre a la solicitud completa.

**Compatibilidad con Consignación:** los estados y sus nombres son los que usa Consignación. Solo pasan por Consignación las solicitudes cuyo origen es un almacén de consignación; como cada almacén de consignación es de un único proveedor y cada solicitud tiene un solo origen, cada solicitud de consignación tiene un único proveedor. "Pendientes por facturar" de Consignación debe listar una fila por solicitud y artículo, igual que el repositorio de documentos.

### Envío

| Estado | Significado operativo | Transiciones y disparadores | Acciones permitidas | Acciones prohibidas |
| --- | --- | --- | --- | --- |
| En tránsito | Salió del origen; nada se ha recibido aún. | → Parcialmente recibido cuando el destino recibe una parte; → Cerrado cuando se recibe o se rechaza todo. | Recibir y rechazar (destino, desde la solicitud o desde FDM). | Modificar cantidades enviadas; anular desde el origen. |
| Parcialmente recibido | Parte fue recibida y parte sigue en tránsito. | → Cerrado cuando lo que sigue en tránsito se recibe o se rechaza. | Recibir y rechazar (destino). | Modificar lo ya recibido. |
| Cerrado | No queda nada en tránsito. | Estado final. | Consultar. | Cualquier modificación. |

Al atender y recibir, el usuario trabaja sobre la solicitud y lo que tiene en tránsito, sin distinguir envíos; los envíos se consultan desde otra parte, que se definirá aparte. Cada envío registra por línea las cantidades enviadas, recibidas y rechazadas. Lo recibido ingresa al destino o se consume según FDM. Lo rechazado retorna al origen y vuelve a quedar pendiente en la solicitud, salvo que esta esté Trunca. Rechazar lo que está en tránsito no cancela la solicitud. En este documento, "en tránsito" incluye los envíos En tránsito y Parcialmente recibido.

## Pantallas

| Pantalla | Propósito y resultado para el usuario | Información y acciones principales | JTBD |
| --- | --- | --- | --- |
| Lista de existencias (FDM) | Encontrar qué solicitar y ver lo que está en tránsito. | Artículos con almacén, ubicación, cantidad y estado; pestañas "En mi zona" y "En tránsito"; búsqueda y escaneo; filtro de almacenes con doble clic en "En mi zona"; ícono de carrito en la cabecera con el número de artículos del borrador. Lo que está en tránsito de una solicitud se ve como cualquier artículo en tránsito, una fila por CU. | `JTBD-SEN-01`, `JTBD-SEN-03` |
| Detalle de artículo (FDM) | Agregar un artículo disponible al borrador o solicitarlo de inmediato. | SKU o CU, almacén y cantidad disponible (no reservada) en todo el almacén; cantidad en unidad de uso y destino; "Agregar" (si el borrador es de otro origen o destino, confirma el reemplazo; el aviso de agregado ofrece "Ver solicitud") y "Solicitar" (con fecha u OT al confirmar). Solo al entrar desde el filtro de almacenes y en almacenes de inventario. | `JTBD-SEN-01` |
| Detalle de artículo en tránsito (FDM) | Recibir o rechazar un artículo en tránsito sin entrar a la solicitud. | Aviso "Pertenece a la SEN · Ir a SEN" arriba y, si corresponde, aviso de OT cerrada o anulada; origen, destino y cantidad en unidades de inventario; tarjeta Recibir (cantidad y ubicación); tarjeta Rechazar ("Rechazar todo", devuelve la fila completa). | `JTBD-SEN-03` |
| Carrito de solicitud | Reunir varios artículos del mismo origen en una sola solicitud. | Cabecera con el estilo de la solicitud creada ("Nueva solicitud ‖ solicitante", "De origen · Para: fecha u OT", chip del destino, lápiz para editar); buscador y lista de existencias del origen con checkbox, disponible y cantidad en unidad de uso, con los seleccionados arriba; solicitar; eliminar. | `JTBD-SEN-01` |
| Tarjeta de solicitud (to-do list) | Ver qué hay que enviar o recibir de cada solicitud. | Código, lugar, fecha requerida u OT, artículos y cantidad pendiente; si el usuario puede enviar y recibir, "por recibir // por enviar" y elección de Enviar o Recibir al abrirla. | `JTBD-SEN-02`, `JTBD-SEN-03` |
| Atender solicitud | Despachar lo solicitado. | Cabecera con solicitante, fecha u OT y chip del destino. Por artículo, plegable: lo enviado frente a lo solicitado con barra en dos tonos; lo escaneado ahora (✓, se puede quitar); lo enviado antes (🕒 en tránsito, ✓ recibido); los completos al final. Escaneo de CU o SKU, con "Añadir a lista" para elegir ubicación y cantidad; enviar; truncar y rechazar desde ⋮. | `JTBD-SEN-02` |
| Recibir solicitud | Ingresar lo que llegó de la solicitud. | Todo lo que está en tránsito de la solicitud, por artículo y plegable: 🕒 por recibir, que pasa a ✓ al escanearse; lo recibido al final. Ubicación en cualquier momento; recibir; desde ⋮, rechazar lo no recibido o cancelar la solicitud (creador, sin nada en tránsito); aviso de OT cerrada o anulada. | `JTBD-SEN-01`, `JTBD-SEN-03` |
| Documento de la solicitud (web) | Consultar una solicitud desde el repositorio de documentos. | Cabecera "Solicitud de envío / código · estado"; aviso con quién y cuándo si está Rechazada, Trunca o Cancelada; datos de la solicitud (usuario solicitador, fecha de solicitud, origen, destino, y fecha requerida u OT con su máquina), de solo lectura; artículos con ID, nombre, solicitado en unidad de uso, y enviado y recibido en unidad de uso con las piezas debajo; anotaciones de uso interno editables, con la última edición. Sin acciones sobre la solicitud. | `JTBD-SEN-01`, `JTBD-SEN-02`, `JTBD-SEN-03` |
| Confirmación de acción irreversible | Evitar rechazos, truncamientos, cancelaciones y reemplazos involuntarios. | Consecuencia de la acción y confirmación. | `JTBD-SEN-01`, `JTBD-SEN-02`, `JTBD-SEN-03` |

## Trabajos por hacer (JTBD)

| JTBD ID | Resultado entregable de forma independiente | Especificación |
| --- | --- | --- |
| `JTBD-SEN-01` | Solicitar materiales de un almacén para una fecha u OT | [jtbd_solicitud_envio_01.md](jtbd_solicitud_envio_01.md) |
| `JTBD-SEN-02` | Atender una solicitud con envíos totales o parciales | [jtbd_solicitud_envio_02.md](jtbd_solicitud_envio_02.md) |
| `JTBD-SEN-03` | Recibir lo enviado de una solicitud en el almacén de destino | [jtbd_solicitud_envio_03.md](jtbd_solicitud_envio_03.md) |
