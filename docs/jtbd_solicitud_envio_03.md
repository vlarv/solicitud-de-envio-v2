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

1. El usuario de destino ve la tarjeta de la solicitud desde que queda Pendiente, también con la casilla "Por recibir" del filtro. La tarjeta muestra "Desde origen", el solicitante con el destino entre paréntesis, y "En tránsito" y "Por enviar" en unidades de inventario, o "Varias unidades"; hasta el primer envío, solo "Por enviar". Al abrirla, si su zona también incluye el origen y hay algo por enviar y algo por recibir, le pide elegir Enviar o Recibir. Si no hay nada por enviar, abre Recibir directamente.
2. Al abrirla ve en la cabecera el origen, el solicitante y la fecha requerida, y todo lo que está en tránsito de la solicitud, sin distinguir envíos. Por cada artículo ve un bloque plegable, cerrado por defecto, con cada ítem por recibir (🕒) y lo ya recibido al final.
3. Escanea lo que recibe, que se agrega directamente. También puede tocar la lupa, que abre un buscador a pantalla completa con todo lo que está en tránsito de esta solicitud, cada resultado con "De origen → destino": los CU uno por uno y los SKU sin CU consolidados. Al escribir, filtra por código o nombre; al tocar un resultado, se agrega como si lo hubiera escaneado. Si ya estaba agregado, aparece "Ya agregaste este artículo".
   - para artículos con CU, escanea cada CU, que se recibe completo y pasa de 🕒 a ✓;
   - para artículos sin CU, escanea el SKU e indica la cantidad en unidades de inventario (por ejemplo, sacos).
4. Indica la ubicación de recepción en cualquier momento, antes o después de escanear los ítems. Debe pertenecer al almacén de destino. Todo lo escaneado en una confirmación ingresa en esa ubicación.
5. El sistema rechaza, indicando el motivo, el escaneo que no está en tránsito en esta solicitud, un CU ya recibido o escaneado, una cantidad igual a cero, con decimales o mayor que la que sigue en tránsito, o una ubicación de otro almacén.
6. Al confirmar, el sistema:
   - reparte internamente lo recibido entre las líneas de los envíos en tránsito;
   - descarta y avisa lo que otro usuario ya recibió, desde FDM o desde la solicitud;
   - si el artículo es observable (tipo de material film (MP, PP y MP fab), resina o tinta/barniz en el MBS), lo ingresa a inventario en la ubicación indicada; si no lo es (por ejemplo, rasquetas), lo registra como consumido y avisa qué se consumió;
   - registra el movimiento en Inventario.
7. El usuario puede repetir los pasos 3 a 6 para recibir en otras ubicaciones o más tarde. Mientras quede algo en tránsito o por enviar, la tarjeta permanece en el to-do list del destino.
8. Desde ⋮ puede **rechazar** lo que sigue en tránsito y no recibió, previa confirmación de que no se puede deshacer. Esa cantidad retorna al origen en Inventario y vuelve a quedar por enviar, salvo que la solicitud esté *Trunca*. El origen no puede anular lo enviado.
9. También puede recibir o rechazar desde FDM. En la pestaña "En tránsito", lo de la solicitud aparece como cualquier artículo en tránsito, una fila por CU. Su detalle muestra arriba el aviso "Pertenece a la SEN · Ir a SEN" y dos tarjetas:
   - **Recibir:** cantidad en unidades de inventario y ubicación, sin escanear;
   - **Rechazar:** "Rechazar todo" devuelve la fila completa.
   Ambas tienen el mismo efecto que hacerlo desde la solicitud.
10. Cuando se recibe el 100 % de lo solicitado de cada artículo, la solicitud pasa a *Recibida* y su tarjeta sale del to-do list. No se envían correos ([Estados de negocio](conceptual_engineering.md#estados-de-negocio)).

### **Campos involucrados en el trabajo**

| Campo | Tipo de dato | Fuente | Validación/Notas |
| --- | --- | --- | --- |
| Origen, solicitante y fecha requerida | Datos de la solicitud | Solicitud | Solo lectura. |
| Cantidad en tránsito y recibida por artículo | Cantidad en la unidad de solicitud y en unidades de inventario | Solicitud y envíos, derivado | Solo lectura. Lo en tránsito es lo enviado menos lo recibido y lo rechazado, sumando todos los envíos. |
| CU escaneado | Código único | Usuario de destino; Inventario | Debe estar en tránsito en esta solicitud y no estar ya recibido ni escaneado. Se recibe completo. |
| SKU escaneado y cantidad recibida | Artículo; número entero en unidades de inventario | Usuario de destino | El SKU debe estar en tránsito en esta solicitud. Cantidad mayor que cero y no mayor que la que sigue en tránsito. |
| Ubicación de recepción | Ubicación | Usuario de destino; Inventario | Obligatoria para confirmar. Se indica en cualquier momento y debe pertenecer al almacén de destino. |
| Tipo de material | Tipo de material del SKU | Maestro de bienes y servicios (MBS) | Define si es observable (film (MP, PP y MP fab), resina o tinta/barniz): lo observable ingresa a inventario y lo demás se consume al recibirse. |
| Estado de la solicitud y de los envíos | Estado de negocio | Sistema | Solicitud: Recibida cuando se recibe el 100 %. Envíos: Parcialmente recibido o Cerrado. |

### **Criterios de aceptación**

*Ver lo que hay por recibir*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-01 | Existe una solicitud Pendiente o Enviada hacia un almacén de la zona del usuario | Ve su tarjeta en el to-do list, también con solo "Por recibir" marcado en el filtro; si aún no hay envíos, muestra solo "Por enviar". Un usuario sin ese almacén en su zona no la ve como por recibir. |
| AC-02 | La zona del usuario incluye el origen y el destino, hay algo por recibir y no hay nada por enviar | La tarjeta abre Recibir directamente. |
| AC-03 | Abre la solicitud para recibir | Ve el origen, el solicitante, la fecha y todo lo que está en tránsito, agrupado por artículo en bloques cerrados y sin distinguir envíos. |

*Recibir desde la solicitud*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-04 | Escanea un CU en tránsito de la solicitud | Pasa de 🕒 a ✓ en su artículo. |
| AC-05 | Escanea el SKU de un artículo sin CU | Indica la cantidad recibida en unidades de inventario. |
| AC-06 | Escanea algo que no está en tránsito en esta solicitud o un CU ya recibido o escaneado, indica una cantidad igual a 0, con decimales o mayor que la que sigue en tránsito, o una ubicación de otro almacén | Se rechaza con su motivo. |
| AC-07 | Indica la ubicación antes o después de escanear y confirma | Lo escaneado observable ingresa en esa ubicación, lo no observable se consume con aviso, y todo se registra en Inventario. |
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

*Escaneo y búsqueda*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-19 | Escanea un CU o SKU en tránsito de la solicitud | Se agrega directamente, sin tocar nada antes. |
| AC-20 | Toca la lupa | Ve a pantalla completa todo lo que está en tránsito de la solicitud, con "De origen → destino", sin escribir nada; al escribir, la lista se filtra por código o nombre. |
| AC-21 | Toca un resultado ya agregado | Aparece "Ya agregaste este artículo" y nada cambia. |

*Ingreso o consumo*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-22 | Recibe un artículo cuyo tipo de material es film (MP, PP o MP fab), resina o tinta/barniz | Ingresa a inventario en la ubicación indicada del almacén de destino. |
| AC-23 | Recibe un artículo de otro tipo de material (por ejemplo, rasquetas) | Se registra como consumido, no queda en inventario del destino y el sistema avisa qué se consumió. |
| AC-24 | Recibe desde el detalle de FDM | Se aplica la misma regla de observable. |
