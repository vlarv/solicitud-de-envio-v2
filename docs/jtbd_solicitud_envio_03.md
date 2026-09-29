# JTBD-SEN-03 Recibir lo enviado de una solicitud en el almacén de destino

### **Introducción**

Cuando llega al almacén de destino lo enviado de una solicitud, el almacenero debe ingresar lo que efectivamente llegó, aunque sea solo una parte y en distintas ubicaciones, y devolver lo que no acepta, para que la solicitud refleje lo recibido y pueda completarse.

### **¿Cuál es mi rol?**

Usuario de destino, según [Usuarios y permisos](conceptual_engineering.md#usuarios-y-permisos).

### **Cuándo**

Tiene en su to-do list de almacén la tarjeta de una solicitud con algo en tránsito hacia un almacén de su zona de influencia, o encuentra ese material en la pestaña "En tránsito" de FDM.

### **Quiero – Para**

**Quiero** ingresar a mi almacén lo que llegó de la solicitud y devolver lo que no acepto,

**Para** que el inventario refleje lo recibido y la necesidad del solicitante quede cubierta.

### **Resumen (solución funcional esperada)**

1. El usuario de destino ve la tarjeta de la solicitud desde que queda Pendiente, con 0 por recibir hasta el primer envío. Si su zona también incluye el origen, la tarjeta muestra "por recibir // por enviar" y al abrirla elige Recibir.
2. Al abrirla ve en la cabecera el origen, el solicitante y la fecha requerida u OT, y todo lo que está en tránsito de la solicitud, sin distinguir envíos. Por cada artículo ve un bloque plegable, cerrado por defecto, con cada ítem por recibir (🕒) y lo ya recibido al final. Si la OT vinculada se cerró o se anuló, ve un aviso para decidir si recibe o rechaza.
3. Escanea lo que recibe:
   - para artículos con CU, escanea cada CU, que se recibe completo y pasa de 🕒 a ✓;
   - para artículos sin CU, escanea el SKU e indica la cantidad en unidades de inventario (por ejemplo, sacos).
4. Indica la ubicación de recepción en cualquier momento, antes o después de escanear los ítems. Debe pertenecer al almacén de destino. Todo lo escaneado en una confirmación ingresa en esa ubicación.
5. El sistema rechaza, indicando el motivo, el escaneo que no está en tránsito en esta solicitud, un CU ya recibido o escaneado, una cantidad igual a cero, con decimales o mayor que la que sigue en tránsito, o una ubicación de otro almacén.
6. Al confirmar, el sistema:
   - reparte internamente lo recibido entre las líneas de los envíos en tránsito;
   - descarta y avisa lo que otro usuario ya recibió, desde FDM o desde la solicitud;
   - registra el movimiento en Inventario; FDM aplica sin cambios su regla de consumo según el maestro de FyE y el sistema muestra el resultado que determine, incluido el aviso de consumo y la opción Deshacer cuando FDM la ofrece.
7. El usuario puede repetir los pasos 3 a 6 para recibir en otras ubicaciones o más tarde. Mientras quede algo en tránsito o por enviar, la tarjeta permanece en el to-do list del destino.
8. Desde ⋮ puede **rechazar** lo que sigue en tránsito y no recibió, previa confirmación de que no se puede deshacer. Esa cantidad retorna al origen en Inventario y vuelve a quedar por enviar, salvo que la solicitud esté *Trunca*. El origen no puede anular lo enviado.
9. También puede recibir o rechazar desde FDM. En la pestaña "En tránsito", lo de la solicitud aparece como cualquier artículo en tránsito, una fila por CU. Su detalle muestra arriba el aviso "Pertenece a la SEN · Ir a SEN" y, si corresponde, el aviso de la OT, además de dos tarjetas:
   - **Recibir:** cantidad en unidades de inventario y ubicación, sin escanear;
   - **Rechazar:** "Rechazar todo" devuelve la fila completa.
   Ambas tienen el mismo efecto que hacerlo desde la solicitud.
10. Cuando se recibe el 100 % de lo solicitado de cada artículo, la solicitud pasa a *Recibida* y su tarjeta sale del to-do list. No se envían correos ([Estados de negocio](conceptual_engineering.md#estados-de-negocio)).

### **Campos involucrados en el trabajo**

| Campo | Tipo de dato | Fuente | Validación/Notas |
| --- | --- | --- | --- |
| Origen, solicitante, fecha requerida u OT | Datos de la solicitud | Solicitud | Solo lectura. |
| Cantidad en tránsito y recibida por artículo | Cantidad en unidad de uso y en unidades de inventario | Solicitud y envíos, derivado | Solo lectura. Lo en tránsito es lo enviado menos lo recibido y lo rechazado, sumando todos los envíos. |
| CU escaneado | Código único | Usuario de destino; Inventario | Debe estar en tránsito en esta solicitud y no estar ya recibido ni escaneado. Se recibe completo. |
| SKU escaneado y cantidad recibida | Artículo; número entero en unidades de inventario | Usuario de destino | El SKU debe estar en tránsito en esta solicitud. Cantidad mayor que cero y no mayor que la que sigue en tránsito. |
| Ubicación de recepción | Ubicación | Usuario de destino; Inventario | Obligatoria para confirmar. Se indica en cualquier momento y debe pertenecer al almacén de destino. |
| Resultado de FDM | Ingreso o consumo | FDM | Informativo; lo determina FDM. |
| Estado de la solicitud y de los envíos | Estado de negocio | Sistema | Solicitud: Recibida cuando se recibe el 100 %. Envíos: Parcialmente recibido o Cerrado. |

### **Criterios de aceptación**

*Ver lo que hay por recibir*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-01 | Existe una solicitud Pendiente o Enviada hacia un almacén de la zona del usuario | Ve su tarjeta en el to-do list, con 0 por recibir si aún no hay envíos; un usuario sin ese almacén en su zona no la ve. |
| AC-02 | Abre la solicitud para recibir | Ve el origen, el solicitante, la fecha u OT y todo lo que está en tránsito, agrupado por artículo en bloques cerrados y sin distinguir envíos. |
| AC-03 | La OT vinculada se cerró o se anuló y hay algo en tránsito | Ve un aviso de la OT y puede recibir o rechazar. |

*Recibir desde la solicitud*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-04 | Escanea un CU en tránsito de la solicitud | Pasa de 🕒 a ✓ en su artículo. |
| AC-05 | Escanea el SKU de un artículo sin CU | Indica la cantidad recibida en unidades de inventario. |
| AC-06 | Escanea algo que no está en tránsito en esta solicitud o un CU ya recibido o escaneado, indica una cantidad igual a 0, con decimales o mayor que la que sigue en tránsito, o una ubicación de otro almacén | Se rechaza con su motivo. |
| AC-07 | Indica la ubicación antes o después de escanear y confirma | Todo lo escaneado ingresa en esa ubicación, se registra en Inventario y se muestra el resultado de FDM. |
| AC-08 | Recibe en dos confirmaciones con ubicaciones distintas | Cada parte queda en la ubicación de su confirmación. |
| AC-09 | Confirma y otro usuario ya recibió alguno de esos ítems | Esos ítems se descartan con un aviso y el resto se recibe. |
| AC-10 | Recibe solo una parte | Lo recibido pasa al final como ✓ y la tarjeta permanece en el to-do list. |

*Recibir o rechazar desde FDM*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-11 | Abre en FDM el detalle de un artículo en tránsito que pertenece a una solicitud | Ve arriba "Pertenece a la SEN · Ir a SEN"; al tocar "Ir a SEN" abre la solicitud. |
| AC-12 | Recibe desde la tarjeta Recibir con cantidad y ubicación válidas | Tiene el mismo efecto que recibir desde la solicitud. |
| AC-13 | Elige "Rechazar todo" y confirma | La fila completa retorna al origen con el mismo efecto que rechazar desde la solicitud. |

*Rechazar*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-14 | Rechaza desde ⋮ lo que sigue en tránsito y confirma | Esa cantidad retorna al origen en Inventario y vuelve a quedar por enviar, salvo que la solicitud esté Trunca. |
| AC-15 | Es usuario de origen | No tiene opción para anular lo enviado. |
| AC-16 | Cancela la confirmación de rechazar | Nada cambia. |

*Cierre*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-17 | Se recibe lo último pendiente y queda recibido el 100 % de cada artículo | La solicitud queda Recibida, su tarjeta sale del to-do list y no se envía correo. |
| AC-18 | La solicitud está Trunca y el destino recibe lo que seguía en tránsito | La recepción se registra normalmente y la solicitud sigue Trunca. |
