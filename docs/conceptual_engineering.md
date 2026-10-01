# Ingeniería conceptual — Solicitud de envío

## **El problema**

Los usuarios que necesitan materiales en un almacén distinto de aquel donde se encuentran deben pedirlos, y el almacén de origen debe despacharlos y el de destino recibirlos. La solución actual solo permite gestionar un artículo por solicitud, por lo que abastecer una necesidad real, que casi siempre involucra varios artículos, obliga a crear, atender y recibir cada artículo por separado.

Esta operación artículo por artículo multiplica el trabajo de solicitantes y almaceneros, fragmenta el seguimiento de lo que se pidió, envió y recibió para una misma necesidad, y dificulta saber cuándo esa necesidad quedó cubierta. El problema debe resolverse porque el abastecimiento entre almacenes sostiene la operación de producción y su lentitud o falta de trazabilidad retrasa la atención de fechas requeridas.

## **Contexto: cómo y por qué el usuario llega al producto o módulo**

- **Solicitante:** llega cuando necesita materiales en un almacén de su zona de influencia para una fecha. Arma su solicitud desde el carrito de solicitud, al que accede desde Flujo de Materiales (FDM), o agregando artículos desde su detalle al consultar las existencias de un almacén de inventario, dentro o fuera de su zona. Sigue sus solicitudes desde el to-do list de almacén con el filtro "Solicitados por", marcando su almacén de destino.
- **Usuario de origen:** llega desde la tarjeta de la solicitud en el to-do list de almacén, pantalla existente de la aplicación anfitriona donde se concentran sus tareas pendientes.
- **Usuario de destino:** llega desde la misma tarjeta de la solicitud en el to-do list, visible desde que la solicitud se crea. También llega desde la pestaña "En tránsito" de FDM, donde lo enviado se ve como cualquier artículo en tránsito.
- **Cualquier usuario:** consulta una solicitud desde el repositorio de documentos de la web, donde cada artículo de la solicitud aparece como una fila y todas abren el mismo documento.

## Alcance

Flujo cubierto, de inicio a fin:

1. **Consulta de existencias:** el solicitante consulta los artículos disponibles y no reservados en almacenes de inventario, inicialmente filtrados por su zona de influencia y sus almacenes, con la opción de ver fuera de su zona.
2. **Solicitud:** el solicitante arma en su único carrito de solicitud uno o varios artículos, todos desde un único almacén de origen hacia un único destino, e indica una fecha requerida. El destino es un almacén de su zona de influencia, distinto del origen, que elige en "Selecciona el destino", con buscador y la lista de almacenes. Puede armarlo desde el carrito, indicando primero fecha, origen y destino y eligiendo entre las existencias del origen, o agregando artículos a pendiente desde su detalle. Si agrega un artículo de otro origen o hacia otro destino, confirma que descarta el borrador anterior y empieza uno nuevo. Mientras no lo envía, el carrito se conserva como borrador. Desde el detalle de un artículo también puede solicitar solo ese artículo de inmediato, sin afectar su borrador. Cada artículo se solicita en su unidad de solicitud: unidades de inventario si su presentación es fija, o unidad de uso si es variable.
3. **Publicación en el to-do list:** cada solicitud aparece como una sola tarjeta para los usuarios de origen y para los de destino. Un filtro agrupa las tareas en SEN ("Solicitados por", "Por recibir", "Por enviar") y OD. "Solicitados por" despliega los almacenes de destino con solicitudes abiertas, cada uno con su casilla; vienen marcados los de la zona del usuario, y cada almacén marcado muestra todas sus solicitudes abiertas, sin importar quién las creó. La tarjeta tiene el mismo formato para todos: el origen ("Desde origen"), el solicitante con el destino entre paréntesis, y lo que está "En tránsito" y "Por enviar" de toda la solicitud en unidades de inventario (por ejemplo, "2 baldes" o "3 bobinas"), o "Varias unidades" si se mezcla más de una. Si un usuario puede enviar y recibir y hay algo en ambos lados, al abrirla elige Enviar o Recibir.
4. **Atención:** un usuario de origen atiende la solicitud mediante uno o varios envíos, totales o parciales. Al escanear un CU o SKU, se agrega directamente; también puede buscarlo con la lupa, que muestra a pantalla completa todo lo de la solicitud que puede enviar. Un envío es lo que confirma en una sola acción y puede incluir varios artículos: cada CU escaneado y los artículos sin CU en unidades de inventario (por ejemplo, sacos), con una línea por ubicación que puede editar o quitar. En artículos de presentación variable puede enviar hasta la tolerancia de despacho por encima de lo solicitado. Tras un envío parcial, la solicitud permanece abierta para envíos posteriores. Puede truncar la solicitud cuando no se completará y rechazarla antes de haber enviado.
5. **Tránsito:** lo enviado queda en tránsito. Al atender y recibir, el usuario trabaja sobre la solicitud y lo que tiene en tránsito, sin distinguir envíos; los envíos se consultan desde otra parte, que se definirá aparte.
6. **Recepción:** un usuario de destino recibe, total o parcialmente, lo que está en tránsito de la solicitud: desde la solicitud, escaneando o buscando con la lupa cada CU, o el SKU con su cantidad en unidades de inventario, y una ubicación del almacén de destino; o desde FDM, en el detalle de cada artículo en tránsito, indicando cantidad y ubicación. Lo recibido ingresa a inventario en el destino si el artículo es observable (tipo de material film (MP, PP y MP fab), resina o tinta/barniz); si no, se consume. En ambos casos puede rechazar lo que sigue en tránsito, que vuelve al origen.
7. **Cierre:** la solicitud queda Recibida cuando se recibe el 100 % de lo solicitado de cada artículo. Si no se completará, el origen la trunca. También termina cuando se rechaza o se cancela. No se envían correos.
8. **Consulta del documento:** cualquier usuario consulta la solicitud en el repositorio de documentos de la web: sus datos, lo solicitado, enviado y recibido por artículo, y quién y cuándo la rechazó, truncó o canceló. Ahí también se registran anotaciones de uso interno. Todas las acciones de la solicitud se hacen en la app, salvo "Enviar a proveedor" de Consignación, que se abre desde el documento (ver Fuera de alcance).

## Fuera de alcance

Este módulo usa estas piezas, pero no las construye ni las cambia:

- **Inventario:** el saldo por almacén y ubicación, y el registro de los movimientos. Este módulo le pide mover el inventario (salida a tránsito, retorno al origen, ingreso al destino) y usa lo que responde.
- **Mover inventario sin solicitud (FDM):** la reubicación libre entre almacenes sigue como hoy.
- **Reservas:** reservar inventario sigue como hoy; este módulo solo lee cuánto está reservado.
- **Registro del consumo (Inventario):** el consumo de lo recibido se registra como hoy; este módulo solo decide, con la regla de observable, si lo recibido ingresa a inventario o se consume (ver Entradas).
- **Acciones masivas y reimpresión de etiquetas (FDM):** siguen como hoy.
- **To-do list de almacén (aplicación anfitriona):** la pantalla ya existe; este módulo agrega las tarjetas de sus solicitudes y el filtro por tipo de tarea. Las tarjetas de OD siguen como hoy.
- **Detalle de artículo en tránsito (FDM):** los datos que vienen de otros módulos, como la fecha de creación, siguen como hoy. Este módulo solo agrega el aviso de la SEN y las acciones Recibir y Rechazar.
- **Repositorio de documentos (web):** la lista, la búsqueda, los filtros y el marco del documento ya existen; este módulo solo aporta las filas de sus solicitudes y el contenido del documento.
- **Consignación:** el paso de Recibida a Por facturar, "Enviar a proveedor", "Pendientes por facturar" y la facturación con Compras pertenecen a ese módulo. Este módulo debe ser compatible con él (ver Estados de negocio) y requiere este ajuste a lo existente:
  - el documento web de una solicitud de consignación Recibida, Trunca o Enviada muestra el botón "Enviar a proveedor" a cualquier usuario;
  - solo se factura una solicitud cerrada: Recibida, o Trunca sin nada en tránsito (se factura lo recibido). Si hay algo en tránsito o falta enviar, el botón muestra un snackbar de error con el motivo (por ejemplo, "No se puede facturar: falta enviar 2 baldes"); nadie trunca desde ahí, lo hace el origen desde la app;
  - si se puede facturar, el botón abre "Pendientes por facturar" con las solicitudes del mismo proveedor que se pueden facturar, con la actual marcada;
  - se selecciona por solicitud, no por artículo: las filas son por artículo, y la casilla y la celda de la solicitud se combinan sobre las filas de sus artículos.
- **Consulta de envíos e historial de solicitudes cerradas:** se definirán aparte.

## Usuarios y permisos

| Usuario | Relación con el problema | Permisos |
| --- | --- | --- |
| Solicitante | Necesita materiales en un almacén de su zona para una fecha. | Consultar existencias dentro y fuera de su zona; crear solicitudes solo hacia almacenes de su zona de influencia; ver en el to-do list, con "Solicitados por", las solicitudes abiertas hacia cualquier almacén de destino y consultarlas; cancelar sus propias solicitudes mientras no haya nada en tránsito. |
| Usuario de origen | Debe despachar lo solicitado desde su almacén. | Cualquier usuario cuya zona de influencia incluya el almacén de origen: ver la tarjeta de la solicitud; enviar total o parcialmente; truncar la solicitud; rechazarla antes de enviar. |
| Usuario de destino | Debe ingresar lo enviado en su almacén. | Cualquier usuario cuya zona de influencia incluya el almacén de destino: ver la tarjeta de la solicitud desde que se crea; recibir desde la solicitud o desde FDM; rechazar lo que sigue en tránsito. |

Cualquier usuario puede consultar las solicitudes en el repositorio de documentos, independientemente del almacén, y editar sus anotaciones de uso interno. No existen roles diferidos.

## Entradas

| Entrada | Fuente | Propósito de negocio |
| --- | --- | --- |
| Existencias por almacén y ubicación, con su estado (por ejemplo, Disponible), cantidad reservada y códigos únicos (CU) | Inventario | Mostrar qué se puede solicitar y desde dónde se puede enviar. Solo lo disponible y no reservado es solicitable. |
| Unidad de uso, unidad de inventario, unidad de compra y presentación (fija o variable) de cada SKU | Maestro de bienes y servicios (MBS) | La presentación define la unidad de solicitud: si es fija (por ejemplo, sacos de 25 kg), se solicita en unidades de inventario; si es variable (por ejemplo, bobinas de peso distinto), se solicita en unidad de uso (por ejemplo, kg). Siempre se envía y recibe en unidades de inventario: CU completos, o sacos, baldes o paquetes. La unidad de compra es informativa. |
| Almacenes de inventario, indicando si son de consignación y de qué proveedor | Almacenes | Restringir los orígenes de una solicitud. Si el origen es un almacén de consignación, la solicitud es de consignación y su proveedor es el del almacén. |
| Zonas de influencia de cada usuario | Gestor de zonas de influencia | Determinar destinos permitidos y qué usuarios ven cada tarjeta. |
| Tipo de material de cada SKU | Maestro de bienes y servicios (MBS) | Determinar si el artículo es observable: lo es si su tipo de material es film (MP, PP y MP fab), resina o tinta/barniz. Al recibir, lo observable ingresa a inventario en el destino y lo no observable (por ejemplo, rasquetas) se consume. |
| Tolerancia de despacho | Configuración del sistema (sin pantalla; se cambia en base de datos) | Porcentaje que se puede enviar por encima de lo solicitado de cada artículo de presentación variable. Es una variable configurable, con valor inicial 5 %. No aplica a la presentación fija. |
| Artículos, cantidades, origen, destino y fecha requerida | Solicitante | Definir la solicitud. |
| CU escaneado, o SKU con ubicación y cantidad en unidades de inventario | Usuario de origen | Definir cada envío. |
| CU escaneado, o SKU con cantidad en unidades de inventario, y ubicación de recepción | Usuario de destino | Registrar qué y dónde ingresa. |
| Anotaciones de uso interno | Cualquier usuario | Dejar notas sobre la solicitud en su documento. |

## Salidas

- Solicitudes de envío con sus artículos, cantidades solicitadas, enviadas y recibidas, y su estado.
- Envíos asociados a cada solicitud, con sus líneas y cantidades enviadas, recibidas y rechazadas.
- Una tarjeta por solicitud en el to-do list de almacén, publicada y cerrada según la zona de influencia y el filtro "Solicitados por".
- Una fila por artículo de cada solicitud en el repositorio de documentos, y el documento de la solicitud.
- Movimientos registrados en inventario: salida del origen hacia tránsito al enviar, retorno al origen al rechazar, e ingreso o consumo al recibir, según si el artículo es observable.
- Trazabilidad completa de quién solicitó, envió, recibió, truncó, rechazó o canceló, y cuándo, y de quién editó por última vez las anotaciones.

## ¿Qué hace el usuario?

- **Solicitante:** consulta existencias, arma una solicitud con uno o varios artículos del mismo almacén de origen, elige el destino y la fecha, la envía, sigue su avance desde el to-do list con "Solicitados por" y, si ya no la necesita, la cancela mientras no haya nada en tránsito.
- **Usuario de origen:** abre la tarjeta de la solicitud, arma cada envío escaneando o buscando con la lupa cada CU, o el SKU para luego elegir ubicación y cantidad en unidades de inventario; corrige o quita lo agregado; ve lo que ya envió y su estado; confirma envíos parciales o totales, y trunca o rechaza la solicitud cuando corresponde.
- **Usuario de destino:** abre la tarjeta de la solicitud, escanea o busca con la lupa cada CU, o el SKU con su cantidad en unidades de inventario, y la ubicación de recepción, antes o después; confirma recepciones totales o parciales, y rechaza lo que sigue en tránsito cuando corresponde. También puede recibir o rechazar cada artículo en tránsito desde FDM.
- **Cualquier usuario:** abre el documento de la solicitud en el repositorio de la web para consultarla y edita sus anotaciones de uso interno.

## ¿Qué hace el sistema por el usuario?

- Filtra inicialmente las existencias por la zona de influencia del usuario y sus almacenes, y agrega la información del artículo a nivel de todo el almacén al solicitar.
- Limita los orígenes a almacenes de inventario y los destinos a almacenes de la zona del solicitante distintos del origen, en un selector con buscador.
- Mantiene un único carrito en borrador por solicitante con un solo origen y destino. Si el solicitante agrega un artículo de otro origen o hacia otro destino, le pide confirmar que descarta el borrador anterior y empieza uno nuevo con ese artículo.
- Propone la fecha de hoy como fecha requerida y fija el destino cuando la zona del solicitante tiene un solo almacén.
- Muestra el disponible y pide la cantidad de cada artículo en su unidad de solicitud, según su presentación en el MBS.
- Acepta solo cantidades enteras y positivas: en la unidad de solicitud al solicitar y en unidades de inventario al enviar y recibir.
- Revalida la disponibilidad no reservada al enviar la solicitud y bloquea el envío hasta que el solicitante ajuste o quite los artículos que la superan.
- Muestra una sola tarjeta por solicitud en el to-do list: al origen mientras quede algo por enviar; al destino desde que la solicitud se crea y mientras quede algo en tránsito o por enviar; y, mientras esté abierta, a cualquier usuario que marque su almacén de destino en "Solicitados por", aunque su zona no incluya el origen ni el destino.
- Muestra todas las tarjetas SEN con el mismo formato: "Desde origen" y la fecha requerida; los artículos; el solicitante con el destino entre paréntesis; y "En tránsito" y "Por enviar" de toda la solicitud en unidades de inventario (por ejemplo, "2 baldes" o "3 bobinas"), o "Varias unidades" si se mezcla más de una. En presentación variable, lo que aún no se envía va en kg, porque todavía no hay piezas. Lo que está en cero no se muestra, y "Por enviar" no se muestra si la solicitud está Trunca.
- Ofrece en el to-do list un filtro acordeón con casillas: SEN ("Solicitados por", "Por recibir", "Por enviar") y OD. "Solicitados por" es un subgrupo anidado con un almacén de destino por casilla, solo los que tienen solicitudes abiertas; por defecto vienen marcados los de la zona del usuario y las demás casillas están marcadas. Una tarjeta que cumple varias casillas aparece una sola vez.
- Al abrir una tarjeta, pide elegir Enviar o Recibir solo si el usuario puede hacer ambas cosas y hay algo por enviar y algo por recibir; si solo hay una de las dos, abre esa pantalla directamente. Si el usuario no puede enviar ni recibir, abre la solicitud en modo consulta.
- Propone como cantidad a enviar, en unidades de inventario, la que cubre lo pendiente sin superar lo disponible en la ubicación ni lo solicitado; en presentación variable, sin superar lo solicitado más la tolerancia de despacho.
- Muestra lo que se va a enviar con una línea por CU y, sin CU, una línea por ubicación que suma lo añadido desde ella; las líneas por ubicación se pueden editar y todas se pueden quitar.
- Agrega directamente lo que se escanea al enviar y al recibir. Ofrece una lupa que abre un buscador a pantalla completa con todo lo de la solicitud que se puede enviar (o recibir), filtrable por código o nombre: los CU con su ubicación y los SKU sin CU consolidados. Se agrega de a uno; si ya estaba agregado, avisa "Ya agregaste este artículo". Nunca busca fuera de la solicitud.
- Trata cada CU como una unidad independiente que se envía y recibe completa.
- Da un artículo por cubierto solo cuando se envió el 100 % de lo solicitado. La solicitud queda Recibida cuando se recibe el 100 % de cada artículo; si no se completará, el origen debe truncarla.
- Mantiene la solicitud vigente mientras exista cantidad pendiente y vuelve a dejar pendiente lo que se rechaza en tránsito, salvo que la solicitud esté Trunca.
- Valida cada escaneo del envío contra la solicitud, la disponibilidad no reservada del origen y el máximo permitido, y revalida lo pendiente al confirmar el envío.
- En la recepción, reúne todo lo que está en tránsito de la solicitud sin distinguir envíos y reparte internamente las unidades recibidas entre las líneas de envío.
- Muestra en FDM lo que está en tránsito de una solicitud como cualquier artículo en tránsito (una fila por CU). En su detalle, avisa que pertenece a la solicitud con acceso directo a ella; recibir o rechazar ahí tiene el mismo efecto que hacerlo desde la solicitud.
- Evita recibir dos veces lo mismo: si otro usuario ya recibió un ítem, desde FDM o desde la solicitud, lo descarta de la confirmación y avisa.
- Publica cada solicitud, desde que está Pendiente, en el repositorio de documentos con una fila por artículo; todas las filas abren el mismo documento. El borrador no aparece.
- En el documento, muestra cada artículo en su unidad de solicitud: en presentación fija, lo solicitado, enviado y recibido en unidades de inventario (por ejemplo, "10 sacos"); en presentación variable, en unidad de uso con las piezas debajo (por ejemplo, "240.10 kg · 2 bobinas"). No detalla envíos. Si la solicitud está Rechazada, Trunca o Cancelada, muestra un aviso con quién y cuándo.
- Guarda en las anotaciones de uso interno quién las editó por última vez y cuándo.
- Solicita confirmación antes de cada acción irreversible y solo ofrece las acciones disponibles en ese momento.
- Al recibir, ingresa a inventario en el destino lo observable (tipo de material film (MP, PP y MP fab), resina o tinta/barniz) y consume lo que no lo es, y avisa qué se consumió. Registra en inventario los movimientos resultantes.

## Módulos

El producto tiene un único módulo, `SEN` (Solicitud de envío). Este documento también sirve como su especificación de módulo.

## **Estados de negocio**

### Solicitud

| Estado | Significado operativo | Transiciones y disparadores | Acciones permitidas | Acciones prohibidas |
| --- | --- | --- | --- | --- |
| Borrador | Carrito en armado; solo lo ve su creador y no se publica en el to-do list ni en el repositorio de documentos. Cada solicitante tiene como máximo uno. | → Pendiente cuando el solicitante lo envía y supera la revalidación de disponibilidad. Eliminarlo o reemplazarlo, previa confirmación, lo descarta. | Agregar, editar y quitar artículos; cambiar fecha, origen y destino; enviar; eliminar. | Atender; publicar en el to-do list. |
| Pendiente | Enviada por el solicitante, desde el borrador o como solicitud directa de un artículo, sin envíos. Visible como tarjeta en el to-do list para el origen, el destino y quien marque su almacén de destino en "Solicitados por", y no como card en la lista de existencias de FDM. | → Enviada al confirmarse el primer envío; → Rechazada si el origen la rechaza; → Cancelada si el solicitante la cancela. | Enviar y rechazar (origen); cancelar (creador). | Truncar. |
| Enviada | Tiene al menos un envío y aún queda cantidad pendiente de enviar o algo en tránsito. | → Recibida cuando se recibe el 100 % de lo solicitado de cada artículo; → Trunca si el origen la trunca; → Cancelada si el solicitante la cancela sin nada en tránsito. | Enviar lo pendiente y truncar (origen); recibir y rechazar lo que está en tránsito (destino); cancelar (creador, sin nada en tránsito). | Rechazar la solicitud; enviar más de lo solicitado (más la tolerancia de despacho, en presentación variable). |
| Recibida | Se recibió el 100 % de lo solicitado de cada artículo. | Estado final para este módulo. Si el origen es un almacén de consignación, Consignación puede pasarla a Por facturar. | Consultar. | Cualquier modificación desde este módulo. |
| Trunca | Finalizada anticipadamente por el origen con lo ya enviado; no admite nuevos envíos. | Estado final para este módulo. Lo que está en tránsito sigue su ciclo. Si el origen es un almacén de consignación y no queda nada en tránsito, Consignación puede pasarla a Por facturar con lo recibido. | Consultar; recibir o rechazar lo que está en tránsito. | Nuevos envíos. |
| Rechazada | El origen no la atenderá. | Estado final. | Consultar. | Cualquier modificación. |
| Cancelada | El solicitante ya no la necesita; deja de estar disponible para atención. | Estado final. | Consultar. | Cualquier modificación. |
| Por facturar (Consignación) | Solicitud de consignación cerrada (Recibida, o Trunca sin nada en tránsito) y enviada a su proveedor para facturar lo recibido. Lo gestiona Consignación. | → Por facturar desde Recibida, o desde Trunca sin nada en tránsito, cuando se ejecuta "Enviar a proveedor" de Consignación, también desde el documento de la solicitud, solo si el origen es un almacén de consignación. Sus transiciones siguientes pertenecen a Consignación. | Las que defina Consignación. | Cualquier acción de este módulo. |

Rechazar, truncar y cancelar se aplican siempre a la solicitud completa.

**Compatibilidad con Consignación:** los estados y sus nombres son los que usa Consignación. Solo pasan por Consignación las solicitudes cuyo origen es un almacén de consignación; como cada almacén de consignación es de un único proveedor y cada solicitud tiene un solo origen, cada solicitud de consignación tiene un único proveedor. "Pendientes por facturar" de Consignación debe listar una fila por solicitud y artículo, igual que el repositorio de documentos, seleccionar por solicitud e incluir las Truncas sin nada en tránsito (ver Fuera de alcance). Una solicitud con algo en tránsito o por enviar no se puede facturar.

### Envío

| Estado | Significado operativo | Transiciones y disparadores | Acciones permitidas | Acciones prohibidas |
| --- | --- | --- | --- | --- |
| En tránsito | Salió del origen; nada se ha recibido aún. | → Parcialmente recibido cuando el destino recibe una parte; → Cerrado cuando se recibe o se rechaza todo. | Recibir y rechazar (destino, desde la solicitud o desde FDM). | Modificar cantidades enviadas; anular desde el origen. |
| Parcialmente recibido | Parte fue recibida y parte sigue en tránsito. | → Cerrado cuando lo que sigue en tránsito se recibe o se rechaza. | Recibir y rechazar (destino). | Modificar lo ya recibido. |
| Cerrado | No queda nada en tránsito. | Estado final. | Consultar. | Cualquier modificación. |

Al atender y recibir, el usuario trabaja sobre la solicitud y lo que tiene en tránsito, sin distinguir envíos; los envíos se consultan desde otra parte, que se definirá aparte. Cada envío registra por línea las cantidades enviadas, recibidas y rechazadas. Lo recibido ingresa al destino si el artículo es observable, o se consume si no lo es. Lo rechazado retorna al origen y vuelve a quedar pendiente en la solicitud, salvo que esta esté Trunca. Rechazar lo que está en tránsito no cancela la solicitud. En este documento, "en tránsito" incluye los envíos En tránsito y Parcialmente recibido.

## Pantallas

| Pantalla | Propósito y resultado para el usuario | Información y acciones principales | JTBD |
| --- | --- | --- | --- |
| Lista de existencias (FDM) | Encontrar qué solicitar y ver lo que está en tránsito. | Artículos con almacén, ubicación, cantidad y estado; pestañas "En mi zona" y "En tránsito"; búsqueda y escaneo; filtro de almacenes con doble clic en "En mi zona"; ícono de carrito en la cabecera con el número de artículos del borrador. Lo que está en tránsito de una solicitud se ve como cualquier artículo en tránsito, una fila por CU. | `JTBD-SEN-01`, `JTBD-SEN-03` |
| Detalle de artículo (FDM) | Agregar un artículo disponible al borrador o solicitarlo de inmediato. | SKU o CU, almacén y cantidad disponible (no reservada) en todo el almacén, en la unidad de solicitud; cantidad y destino; "Agregar" (si el borrador es de otro origen o destino, confirma el reemplazo; el aviso de agregado ofrece "Ver solicitud") y "Solicitar" (con fecha al confirmar). Solo al entrar desde el filtro de almacenes y en almacenes de inventario. | `JTBD-SEN-01` |
| Detalle de artículo en tránsito (FDM) | Recibir o rechazar un artículo en tránsito sin entrar a la solicitud. | Aviso "Pertenece a la SEN · Ir a SEN" arriba; origen, destino y cantidad en unidades de inventario; tarjeta Recibir (cantidad y ubicación); tarjeta Rechazar ("Rechazar todo", devuelve la fila completa). | `JTBD-SEN-03` |
| Selector de destino | Elegir el almacén de destino de la solicitud. | "Selecciona el destino" con buscador y la lista de almacenes de la zona, distintos del origen. | `JTBD-SEN-01` |
| Carrito de solicitud | Reunir varios artículos del mismo origen en una sola solicitud. | Cabecera con el estilo de la solicitud creada ("Nueva solicitud ‖ solicitante", "De origen · Para: fecha", chip del destino, lápiz para editar); buscador y lista de existencias del origen con checkbox, disponible y cantidad en la unidad de solicitud, con los seleccionados arriba; solicitar; eliminar. | `JTBD-SEN-01` |
| To-do list de almacén (filtro y tarjeta) | Ver qué hay que enviar o recibir, y seguir las solicitudes propias. | Filtro acordeón con casillas: SEN ("Solicitados por", con un almacén de destino por casilla; "Por recibir"; "Por enviar") y OD. Tarjeta por solicitud, igual para todos: código, "Desde origen", fecha requerida, artículos, solicitante (destino), y "En tránsito" y "Por enviar" en unidades de inventario, o "Varias unidades". Si el usuario puede enviar y recibir y hay algo en ambos lados, al abrirla elige Enviar o Recibir. | `JTBD-SEN-01`, `JTBD-SEN-02`, `JTBD-SEN-03` |
| Atender solicitud | Despachar lo solicitado. | Cabecera con solicitante, fecha y chip del destino. Por artículo, plegable: lo enviado frente a lo solicitado con barra en dos tonos; lo escaneado ahora (✓), con una línea por CU o por ubicación, editable por ubicación y quitable; lo enviado antes (🕒 en tránsito, ✓ recibido); los completos al final. Escaneo de CU o SKU, que se agrega directamente, y lupa con buscador a pantalla completa de lo que se puede enviar; "Añadir a lista" para elegir ubicación y cantidad; enviar; truncar y rechazar desde ⋮. | `JTBD-SEN-02` |
| Recibir solicitud | Ingresar lo que llegó de la solicitud. | Todo lo que está en tránsito de la solicitud, por artículo y plegable: 🕒 por recibir, que pasa a ✓ al escanearse o elegirse con la lupa; lo recibido al final. Ubicación en cualquier momento; recibir; desde ⋮, rechazar lo no recibido o cancelar la solicitud (creador, sin nada en tránsito). | `JTBD-SEN-01`, `JTBD-SEN-03` |
| Solicitud en modo consulta | Seguir el avance de una solicitud propia en la que no se puede enviar ni recibir. | Los mismos bloques por artículo, con avance e historial, sin escaneo ni confirmación; desde ⋮, cancelar la solicitud si no hay nada en tránsito. | `JTBD-SEN-01` |
| Documento de la solicitud (web) | Consultar una solicitud desde el repositorio de documentos. | Cabecera "Solicitud de envío / código · estado"; aviso con quién y cuándo si está Rechazada, Trunca o Cancelada; datos de la solicitud (usuario solicitador, fecha de solicitud, origen, destino y fecha requerida), de solo lectura; artículos con ID, nombre, y solicitado, enviado y recibido en la unidad de solicitud (en presentación variable, con las piezas debajo); anotaciones de uso interno editables, con la última edición. Sin acciones sobre la solicitud, salvo el botón "Enviar a proveedor" de Consignación en solicitudes de consignación Recibidas, Truncas o Enviadas; si aún no se puede facturar, muestra un snackbar de error arriba con el motivo. | `JTBD-SEN-01`, `JTBD-SEN-02`, `JTBD-SEN-03` |
| Confirmación de acción irreversible | Evitar rechazos, truncamientos, cancelaciones y reemplazos involuntarios. | Consecuencia de la acción y confirmación. | `JTBD-SEN-01`, `JTBD-SEN-02`, `JTBD-SEN-03` |

## Trabajos por hacer (JTBD)

| JTBD ID | Resultado entregable de forma independiente | Especificación |
| --- | --- | --- |
| `JTBD-SEN-01` | Solicitar materiales de un almacén para una fecha | [jtbd_solicitud_envio_01.md](jtbd_solicitud_envio_01.md) |
| `JTBD-SEN-02` | Atender una solicitud con envíos totales o parciales | [jtbd_solicitud_envio_02.md](jtbd_solicitud_envio_02.md) |
| `JTBD-SEN-03` | Recibir lo enviado de una solicitud en el almacén de destino | [jtbd_solicitud_envio_03.md](jtbd_solicitud_envio_03.md) |
