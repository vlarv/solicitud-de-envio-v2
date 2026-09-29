# JTBD-SEN-01 Solicitar materiales de un almacén para una fecha u OT

### **Introducción**

Cuando un usuario necesita varios materiales en un almacén de su zona, debe poder pedirlos juntos en una sola solicitud a un almacén de origen, en lugar de solicitar artículo por artículo, o pedir un único artículo de inmediato, y dejar la solicitud lista para que el origen la atienda.

### **¿Cuál es mi rol?**

Solicitante, según [Usuarios y permisos](conceptual_engineering.md#usuarios-y-permisos).

### **Cuándo**

Necesita materiales en un almacén de su zona de influencia para una fecha requerida o para una OT, y esos materiales están disponibles y no reservados en un almacén de inventario.

### **Quiero – Para**

**Quiero** reunir en una sola solicitud los artículos que necesito de un almacén de origen, o pedir uno solo de inmediato, con su cantidad, destino y fecha u OT,

**Para** que el almacén de origen los atienda juntos y yo pueda seguir el avance de mi necesidad.

### **Resumen (solución funcional esperada)**

1. El solicitante arma su borrador de solicitud de una de dos formas:
   - **Desde el carrito**, al que accede con el ícono de carrito de FDM: indica fecha requerida u OT, almacén de origen y almacén de destino; el sistema muestra las existencias disponibles y no reservadas del origen agrupadas por SKU en unidad de uso, y el solicitante selecciona artículos e indica la cantidad de cada uno. Origen, destino y fecha u OT se muestran en la cabecera del carrito y pueden editarse desde ella.
   - **Desde el detalle de un artículo**, al consultar las existencias de un almacén de inventario: indica la cantidad y el destino y elige Agregar; el artículo se agrega al borrador, el sistema lo confirma con un aviso que ofrece "Ver solicitud" y el solicitante continúa agregando otros. Si no existía borrador, se crea con el almacén del artículo como origen, el destino elegido y la fecha de hoy.
2. El borrador es único por solicitante y tiene un solo origen y un solo destino. Si el solicitante agrega un artículo de otro origen o hacia otro destino, el sistema le pide confirmar que descarta el borrador anterior; si confirma, se crea un borrador nuevo con ese artículo, y si no, nada cambia.
3. Si agrega un SKU que ya está en el borrador, la cantidad se suma a la línea existente.
4. Las cantidades se indican como números enteros positivos en unidad de uso; la unidad de compra se muestra como información. Para SKU con CU, el disponible corresponde a la unidad de uso total del SKU en el almacén (por ejemplo, dos CU de 300.5 kg y 500.7 kg suman 801.2 kg disponibles).
5. La fecha requerida propone por defecto la fecha de hoy. Si la zona de influencia del solicitante tiene un solo almacén, el destino viene definido y no se puede cambiar.
6. Si el solicitante vincula una OT, solo puede elegir OT abiertas o programadas cuya máquina esté en su zona de influencia. El destino pasa a ser el almacén de la máquina de la OT y no se puede cambiar, y la fecha requerida queda ligada a la OT: si la OT se reprograma, la fecha de la solicitud cambia con ella. Si la OT se cierra o se anula mientras la solicitud está Pendiente o Enviada, el sistema la trunca con lo ya enviado; lo que está en tránsito sigue su ciclo y el destino ve un aviso.
7. El solicitante puede revisar el borrador, cambiar cantidades, quitar artículos, cambiar fecha u OT y destino, o eliminarlo previa confirmación de que la acción no se puede deshacer. Si no lo envía, el borrador se conserva sin vencimiento ([Estados de negocio](conceptual_engineering.md#estados-de-negocio)).
8. Desde el detalle de un artículo, el solicitante también puede elegir **Solicitar** para pedir solo ese artículo: el sistema abre un panel para indicar fecha requerida u OT y, al confirmar, crea y envía una solicitud de un único artículo con las mismas validaciones. Si elige una OT, el destino pasa a ser el almacén de su máquina. Esta acción no modifica el borrador existente.
9. Al enviar, el sistema valida los campos obligatorios y revalida la disponibilidad no reservada de cada artículo en el origen. Si alguna cantidad la supera, marca esos artículos con el disponible actual y no envía hasta que el solicitante ajuste o quite esos artículos.
10. Una solicitud enviada pasa a *Pendiente* y ya no se puede editar. Se publica como una tarjeta en el to-do list de almacén para los usuarios de origen y de destino, y en el repositorio de documentos con una fila por artículo. No se muestra como card en la lista de existencias de FDM.
11. El solicitante puede cancelar su solicitud desde ⋮, previa confirmación de que la acción no se puede deshacer, mientras no tenga nada en tránsito. La solicitud pasa a *Cancelada* y su tarjeta sale del to-do list.

### **Campos involucrados en el trabajo**

| Campo | Tipo de dato | Fuente | Validación/Notas |
| --- | --- | --- | --- |
| Almacén de origen | Almacén | Solicitante; Almacenes | Obligatorio. Solo almacenes de inventario. Puede estar fuera de la zona del solicitante. Único por borrador. |
| Almacén de destino | Almacén | Solicitante u OT; Gestor de zonas de influencia | Obligatorio. Solo almacenes de la zona de influencia del solicitante. Distinto del origen. Definido y no editable si la zona tiene un solo almacén. Si se vincula OT, es el almacén de la máquina de la OT y no es editable. |
| Fecha requerida | Fecha | Solicitante u OT | Obligatoria si no se vincula OT; propone la fecha de hoy; no puede estar en el pasado. Si se vincula OT, se deriva de ella, no se ingresa y sigue sus reprogramaciones. |
| OT | Referencia a orden de trabajo | Solicitante; Producción | Obligatoria si no se indica fecha requerida. Se busca por código. Solo OT abiertas o programadas cuya máquina esté en la zona de influencia del solicitante. |
| SKU o CU agrupado | Artículo | Inventario | Solo artículos en estado Disponible del almacén de origen. |
| Disponible no reservado | Cantidad en unidad de uso | Inventario, derivado | Total del SKU en todo el almacén de origen, excluyendo lo reservado. Se revalida al enviar. |
| Cantidad solicitada | Número entero en unidad de uso | Solicitante | Obligatoria, entera, mayor que cero y no mayor que el disponible no reservado. |
| Unidad de uso y unidad de compra | Unidad y equivalencia | Maestro de bienes y servicios (MBS) | La unidad de uso es la de la solicitud; la unidad de compra es informativa y no se captura. |
| Estado de la solicitud | Estado de negocio | Sistema | En este trabajo: Borrador, Pendiente, Cancelada o, por cierre o anulación de la OT, Trunca. |

### **Criterios de aceptación**

*Armar el borrador*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-01 | El solicitante abre el carrito desde FDM sin tener borrador e indica fecha u OT, origen y destino válidos | Ve las existencias disponibles y no reservadas del origen, agrupadas por SKU en unidad de uso, y puede seleccionar artículos con su cantidad. |
| AC-02 | Agrega un artículo desde su detalle con cantidad y destino válidos | El artículo queda en su borrador y aparece un aviso con "Ver solicitud". Si el SKU ya estaba, se suma la cantidad. Si no tenía borrador, se crea con ese origen, ese destino y la fecha de hoy. |
| AC-03 | Agrega un artículo de otro origen o hacia otro destino que su borrador | Se le pide confirmar que reemplaza el borrador. Si confirma, el borrador queda solo con el nuevo artículo, su origen y su destino. Si cancela, no cambia nada. |
| AC-04 | Sale sin enviar el borrador y vuelve más tarde | Encuentra el mismo borrador con sus artículos y datos. El borrador no aparece en el to-do list ni en el repositorio de documentos. |
| AC-05 | Elimina el borrador y confirma | El borrador deja de existir. |

*Validaciones*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-06 | Elige un destino fuera de su zona de influencia o igual al origen | El sistema no lo permite. Si su zona tiene un solo almacén, el destino viene definido y no se puede cambiar. |
| AC-07 | Intenta enviar sin fecha ni OT, o con una fecha pasada | No se envía y se indica el dato que falta o es inválido. La fecha propuesta es hoy. |
| AC-08 | Indica una cantidad igual a 0, negativa, con decimales o mayor que el disponible no reservado | El sistema no la acepta. |
| AC-09 | Envía el borrador y el disponible de algún artículo bajó desde que lo armó | No se envía. Esos artículos se marcan con el disponible actual hasta que el solicitante los ajuste o quite. |

*Enviar la solicitud*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-10 | Envía un borrador válido | La solicitud queda Pendiente y no se puede editar. Aparece como tarjeta en el to-do list de los usuarios del origen y del destino, y en el repositorio de documentos con una fila por artículo. No aparece como card en la lista de existencias de FDM. |
| AC-11 | Elige Solicitar desde el detalle de un artículo, indica fecha u OT y confirma | Se crea una solicitud Pendiente solo con ese artículo. Su borrador no cambia. |

*OT vinculada*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-12 | Vincula una OT | Solo puede elegir OT abiertas o programadas cuya máquina esté en su zona. El destino pasa a ser el almacén de esa máquina y no se puede cambiar. La fecha requerida se toma de la OT. |
| AC-13 | La OT vinculada se reprograma | La fecha requerida de la solicitud cambia a la nueva fecha de la OT. |
| AC-14 | La OT vinculada se cierra o se anula con la solicitud Pendiente o Enviada | La solicitud queda Trunca con lo ya enviado y no admite nuevos envíos. Lo que está en tránsito sigue su ciclo, el destino ve un aviso de la OT y el documento registra al sistema como autor. |

*Cancelar*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-15 | El creador elige Cancelar solicitud en ⋮, sin nada en tránsito, y confirma | La solicitud queda Cancelada, su tarjeta sale del to-do list y el documento muestra quién la canceló y cuándo. |
| AC-16 | Hay algo en tránsito, o quien abre la solicitud no es su creador | La opción Cancelar solicitud no aparece. |
