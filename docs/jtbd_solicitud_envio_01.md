# JTBD-SEN-01 Solicitar materiales de un almacén para una fecha

### **Introducción**

Cuando un usuario necesita varios materiales en un almacén de su zona, debe poder pedirlos juntos en una sola solicitud a un almacén de origen, en lugar de solicitar artículo por artículo, o pedir un único artículo de inmediato, dejar la solicitud lista para que el origen la atienda y seguir su avance.

### **¿Cuál es mi rol?**

Solicitante, según [Usuarios y permisos](conceptual_engineering.md#usuarios-y-permisos).

### **Cuándo**

Necesita materiales en un almacén de su zona de influencia para una fecha requerida, y esos materiales están disponibles y no reservados en un almacén de inventario.

### **Quiero – Para**

**Quiero** reunir en una sola solicitud los artículos que necesito de un almacén de origen, o pedir uno solo de inmediato, con su cantidad, destino y fecha,

**Para** que el almacén de origen los atienda juntos y yo pueda seguir el avance de mi necesidad.

### **Resumen (solución funcional esperada)**

1. El solicitante arma su borrador de solicitud de una de dos formas:
   - **Desde el carrito**, al que accede con el ícono de carrito de FDM: indica fecha requerida, almacén de origen y destino; el sistema muestra las existencias disponibles y no reservadas del origen agrupadas por SKU, y el solicitante selecciona artículos e indica la cantidad de cada uno. Origen, destino y fecha se muestran en la cabecera del carrito y pueden editarse desde ella.
   - **Desde el detalle de un artículo**, al consultar las existencias de un almacén de inventario: indica la cantidad y el destino y elige Agregar; el artículo se agrega al borrador, el sistema lo confirma con un aviso que ofrece "Ver solicitud" y el solicitante continúa agregando otros. Si no existía borrador, se crea con el almacén del artículo como origen, el destino elegido y la fecha de hoy.
2. El destino se elige en "Selecciona el destino", con buscador y la lista de almacenes de su zona de influencia, distintos del origen.
3. Cada artículo se solicita en su unidad de solicitud, según la presentación del SKU en el MBS ([Entradas](conceptual_engineering.md#entradas)):
   - **presentación fija:** en unidades de inventario (por ejemplo, 10 sacos); el disponible también se muestra en unidades de inventario;
   - **presentación variable:** en unidad de uso (por ejemplo, 240 kg); el disponible es la unidad de uso total del SKU en el almacén (por ejemplo, dos bobinas de 300.5 kg y 500.7 kg suman 801.2 kg).
   Las cantidades son números enteros positivos; la unidad de compra se muestra como información.
4. El borrador es único por solicitante y tiene un solo origen y un solo destino. Si el solicitante agrega un artículo de otro origen o hacia otro destino, el sistema le pide confirmar que descarta el borrador anterior; si confirma, se crea un borrador nuevo con ese artículo, y si no, nada cambia.
5. Si agrega un SKU que ya está en el borrador, la cantidad se suma a la línea existente.
6. La fecha requerida propone por defecto la fecha de hoy. Si la zona de influencia del solicitante tiene un solo almacén, el destino viene definido y no se puede cambiar.
7. El solicitante puede revisar el borrador, cambiar cantidades, quitar artículos, cambiar fecha y destino, o eliminarlo previa confirmación de que la acción no se puede deshacer. Si no lo envía, el borrador se conserva sin vencimiento ([Estados de negocio](conceptual_engineering.md#estados-de-negocio)).
8. Desde el detalle de un artículo, el solicitante también puede elegir **Solicitar** para pedir solo ese artículo: el sistema abre un panel para indicar la fecha requerida y, al confirmar, crea y envía una solicitud de un único artículo con las mismas validaciones. Esta acción no modifica el borrador existente.
9. Al enviar, el sistema valida los campos obligatorios y revalida la disponibilidad no reservada de cada artículo en el origen. Si alguna cantidad la supera, marca esos artículos con el disponible actual y no envía hasta que el solicitante ajuste o quite esos artículos.
10. Una solicitud enviada pasa a *Pendiente* y ya no se puede editar. Se publica como una tarjeta en el to-do list de almacén para los usuarios de origen y de destino, y en el repositorio de documentos con una fila por artículo. No se muestra como card en la lista de existencias de FDM.
11. El solicitante sigue sus solicitudes abiertas en el to-do list con la casilla "Solicitadas por mí" del filtro, aunque su zona no incluya el origen ni el destino. La tarjeta tiene el mismo formato para todos: "Desde origen" y la fecha requerida; los artículos; el solicitante con el destino entre paréntesis, y lo que está "En tránsito" y "Por enviar" de toda la solicitud, consolidado en kg solo en la tarjeta. Lo que está en cero no se muestra. Si no puede enviar ni recibir en una de ellas, al abrirla la ve en modo consulta: los bloques por artículo con su avance e historial, sin escaneo ni confirmación.
12. El solicitante puede cancelar su solicitud desde ⋮, previa confirmación de que la acción no se puede deshacer, mientras no tenga nada en tránsito. La solicitud pasa a *Cancelada* y su tarjeta sale del to-do list.

### **Campos involucrados en el trabajo**

| Campo | Tipo de dato | Fuente | Validación/Notas |
| --- | --- | --- | --- |
| Almacén de origen | Almacén | Solicitante; Almacenes | Obligatorio. Solo almacenes de inventario. Puede estar fuera de la zona del solicitante. Único por borrador. |
| Almacén de destino | Almacén | Solicitante; Gestor de zonas de influencia | Obligatorio. Solo almacenes de la zona de influencia del solicitante. Distinto del origen. Definido y no editable si la zona tiene un solo almacén. |
| Fecha requerida | Fecha | Solicitante | Obligatoria; propone la fecha de hoy; no puede estar en el pasado. |
| SKU o CU agrupado | Artículo | Inventario | Solo artículos en estado Disponible del almacén de origen. |
| Presentación | Fija o variable | Maestro de bienes y servicios (MBS) | Define la unidad de solicitud del SKU. |
| Disponible no reservado | Cantidad en la unidad de solicitud | Inventario, derivado | Total del SKU en todo el almacén de origen, excluyendo lo reservado. Se revalida al enviar. |
| Cantidad solicitada | Número entero en la unidad de solicitud | Solicitante | Obligatoria, entera, mayor que cero y no mayor que el disponible no reservado. |
| Unidad de uso, de inventario y de compra | Unidad y equivalencia | Maestro de bienes y servicios (MBS) | La unidad de uso o la de inventario es la de solicitud, según la presentación; la unidad de compra es informativa y no se captura. |
| Estado de la solicitud | Estado de negocio | Sistema | En este trabajo: Borrador, Pendiente o Cancelada. |

### **Criterios de aceptación**

*Armar el borrador*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-01 | El solicitante abre el carrito desde FDM sin tener borrador e indica fecha, origen y destino válidos | Ve las existencias disponibles y no reservadas del origen, agrupadas por SKU, y puede seleccionar artículos con su cantidad. |
| AC-02 | Consulta o solicita un SKU de presentación fija, o uno de presentación variable | El disponible y la cantidad se expresan en unidades de inventario (por ejemplo, sacos) si es fija, y en unidad de uso (por ejemplo, kg) si es variable. |
| AC-03 | Agrega un artículo desde su detalle con cantidad y destino válidos | El artículo queda en su borrador y aparece un aviso con "Ver solicitud". Si el SKU ya estaba, se suma la cantidad. Si no tenía borrador, se crea con ese origen, ese destino y la fecha de hoy. |
| AC-04 | Agrega un artículo de otro origen o hacia otro destino que su borrador | Se le pide confirmar que reemplaza el borrador. Si confirma, el borrador queda solo con el nuevo artículo, su origen y su destino. Si cancela, no cambia nada. |
| AC-05 | Sale sin enviar el borrador y vuelve más tarde | Encuentra el mismo borrador con sus artículos y datos. El borrador no aparece en el to-do list ni en el repositorio de documentos. |
| AC-06 | Elimina el borrador y confirma | El borrador deja de existir. |

*Validaciones*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-07 | Elige un destino fuera de su zona de influencia o igual al origen | El sistema no lo permite. Si su zona tiene un solo almacén, el destino viene definido y no se puede cambiar. |
| AC-08 | Intenta enviar sin fecha o con una fecha pasada | No se envía y se indica el dato que falta o es inválido. La fecha propuesta es hoy. |
| AC-09 | Indica una cantidad igual a 0, negativa, con decimales o mayor que el disponible no reservado | El sistema no la acepta. |
| AC-10 | Envía el borrador y el disponible de algún artículo bajó desde que lo armó | No se envía. Esos artículos se marcan con el disponible actual hasta que el solicitante los ajuste o quite. |

*Enviar la solicitud*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-11 | Envía un borrador válido | La solicitud queda Pendiente y no se puede editar. Aparece como tarjeta en el to-do list de los usuarios del origen y del destino, y en el repositorio de documentos con una fila por artículo. No aparece como card en la lista de existencias de FDM. |
| AC-12 | Elige Solicitar desde el detalle de un artículo, indica la fecha y confirma | Se crea una solicitud Pendiente solo con ese artículo. Su borrador no cambia. |

*Seguir mis solicitudes*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-13 | Deja marcada la casilla "Solicitadas por mí" en el filtro del to-do list | Ve todas las solicitudes abiertas que creó, aunque su zona no incluya el origen ni el destino. |
| AC-14 | Abre una solicitud propia en la que no puede enviar ni recibir | La ve en modo consulta, con el avance por artículo y sin escaneo ni confirmación. |

*Cancelar*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-15 | El creador elige Cancelar solicitud en ⋮, sin nada en tránsito, y confirma | La solicitud queda Cancelada, su tarjeta sale del to-do list y el documento muestra quién la canceló y cuándo. |
| AC-16 | Hay algo en tránsito, o quien abre la solicitud no es su creador | La opción Cancelar solicitud no aparece. |

*Destino*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-17 | Abre el destino y escribe en el buscador | Ve solo los almacenes de su zona, distintos del origen, que coinciden con lo escrito. |

*Tarjeta del to-do list*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-18 | Ve una solicitud en el to-do list | La tarjeta muestra "Desde origen", la fecha, los artículos, el solicitante con el destino entre paréntesis, y "En tránsito" y "Por enviar" en kg; lo que está en cero no aparece. Es igual para el origen, el destino y el creador. |
