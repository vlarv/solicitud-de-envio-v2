# JTBD-SEN-02 Atender una solicitud con envíos totales o parciales

### **Introducción**

Cuando llega una solicitud al almacén de origen, el almacenero debe despachar en un solo envío todo lo que tiene disponible de sus varios artículos, dejar lo que falte para un envío posterior, o cerrar la solicitud si no podrá atenderla.

### **¿Cuál es mi rol?**

Usuario de origen, según [Usuarios y permisos](conceptual_engineering.md#usuarios-y-permisos).

### **Cuándo**

Tiene en su to-do list de almacén la tarjeta de una solicitud *Pendiente* o *Enviada* con algo por enviar, cuyo almacén de origen está en su zona de influencia.

### **Quiero – Para**

**Quiero** despachar lo solicitado que tengo disponible, todo de una vez o en partes, o cerrar la solicitud cuando no se completará,

**Para** que el almacén de destino reciba los materiales y la necesidad del solicitante quede cubierta o resuelta.

### **Resumen (solución funcional esperada)**

1. El usuario de origen abre la tarjeta de la solicitud. Si su zona también incluye el destino, la tarjeta muestra "por recibir // por enviar" y al abrirla elige Enviar.
2. Ve en la cabecera el solicitante, la fecha requerida u OT y el destino. Por cada artículo ve un bloque plegable, cerrado por defecto, con lo enviado frente a lo solicitado en unidad de uso y una barra de avance en dos tonos (recibido y en tránsito). Al desplegarlo ve lo escaneado ahora y lo enviado antes, en tránsito o recibido. Los artículos cubiertos van al final.
3. Arma el envío escaneando lo que despacha:
   - para artículos con CU, escanea cada CU, que se agrega completo;
   - para artículos sin CU, escanea el SKU y, en "Añadir a lista", escanea o elige la ubicación e indica la cantidad en unidades de inventario (por ejemplo, sacos). El sistema propone la que cubre lo pendiente sin superar lo disponible no reservado en esa ubicación ni lo solicitado más la tolerancia de despacho.
4. El sistema valida cada escaneo y rechaza, indicando el motivo, el que:
   - no corresponde a un artículo de la solicitud;
   - no está Disponible y no reservado en el almacén de origen, o cuya ubicación no pertenece al origen;
   - haría que lo enviado del artículo supere lo solicitado más la tolerancia de despacho ([Entradas](conceptual_engineering.md#entradas));
   - repite un CU ya agregado al envío.
5. El usuario puede quitar lo escaneado antes de confirmar. Un envío no confirmado no se conserva: si el usuario sale de la atención, se descarta.
6. Al confirmar, el sistema revalida lo pendiente de cada artículo, por si otro usuario de origen envió en paralelo, y bloquea las líneas que lo superen hasta que el usuario las ajuste o quite. Si la validación pasa:
   - se crea un envío con todo lo escaneado, que queda *En tránsito*;
   - se registra en Inventario la salida del origen hacia tránsito;
   - la solicitud pasa a *Enviada*, se actualizan sus cantidades enviadas y pendientes, y la tarjeta del destino muestra lo que hay por recibir ([Estados de negocio](conceptual_engineering.md#estados-de-negocio)).
7. Un artículo queda cubierto solo cuando se envió el 100 % de lo solicitado. Mientras quede algo por enviar, la tarjeta sigue en el to-do list del origen para envíos posteriores. Lo que el destino rechaza vuelve a quedar por enviar, salvo que la solicitud esté Trunca.
8. Desde ⋮, que solo muestra las acciones disponibles, el usuario puede:
   - **truncar** una solicitud *Enviada*, previa confirmación de que se dará por finalizada con lo ya enviado y no se puede deshacer. Pasa a *Trunca*, su tarjeta sale del to-do list del origen y lo que está en tránsito sigue su ciclo. Se usa también cuando el faltante no se puede cubrir con piezas completas; el solicitante vuelve a solicitar si lo necesita.
   - **rechazar** una solicitud *Pendiente*, previa confirmación de que no se puede deshacer. Pasa a *Rechazada* y su tarjeta sale del to-do list del origen y del destino.
9. En ambos casos, el documento de la solicitud muestra quién la truncó o rechazó y cuándo. No se envían correos.

### **Campos involucrados en el trabajo**

| Campo | Tipo de dato | Fuente | Validación/Notas |
| --- | --- | --- | --- |
| Solicitante, destino, fecha requerida u OT | Datos de la solicitud | Solicitud | Solo lectura. |
| Cantidad solicitada, enviada y pendiente por artículo | Cantidad en unidad de uso | Solicitud, derivado | Lo pendiente descuenta lo enviado, salvo lo que el destino rechazó. |
| CU escaneado | Código único | Usuario de origen; Inventario | Debe pertenecer a un artículo de la solicitud, estar Disponible y no reservado en el origen y no estar ya en el envío. Se envía completo. |
| SKU y ubicación | Artículo y ubicación | Usuario de origen; Inventario | El SKU debe pertenecer a la solicitud y la ubicación, al almacén de origen. |
| Cantidad a enviar | Número entero en unidades de inventario | Usuario de origen; propuesta del sistema | Mayor que cero; no mayor que lo disponible no reservado en la ubicación; lo enviado del artículo no puede superar lo solicitado más la tolerancia de despacho. |
| Tolerancia de despacho | Porcentaje | Configuración del sistema | Variable configurable; valor inicial 5 % ([Entradas](conceptual_engineering.md#entradas)). |
| Envío | Conjunto de líneas escaneadas | Sistema | Al menos una línea para confirmar. |
| Estado de la solicitud y del envío | Estado de negocio | Sistema | Solicitud: Enviada, Trunca o Rechazada en este trabajo. Envío: En tránsito. |

### **Criterios de aceptación**

*Ver la solicitud*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-01 | Existe una solicitud Pendiente o Enviada con algo por enviar | Los usuarios cuya zona incluye el origen ven su tarjeta en el to-do list; los demás no. |
| AC-02 | La zona del usuario incluye el origen y el destino | La tarjeta muestra "por recibir // por enviar" y al abrirla le pide elegir Enviar o Recibir. |
| AC-03 | El usuario abre la solicitud para enviar | Ve el solicitante, la fecha u OT, el destino y un bloque cerrado por artículo con lo enviado frente a lo solicitado; los artículos cubiertos van al final. |

*Armar el envío*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-04 | Escanea un CU válido | Se agrega completo al envío y aparece como escaneado ahora en su artículo. |
| AC-05 | Escanea un CU que ya está en el envío, que no es de la solicitud o que no está disponible y no reservado en el origen | Se rechaza con su motivo y el envío no cambia. |
| AC-06 | Escanea el SKU de un artículo sin CU | En "Añadir a lista" elige la ubicación del origen y el sistema propone la cantidad en unidades de inventario que cubre lo pendiente, sin superar lo disponible en la ubicación ni lo solicitado más la tolerancia. |
| AC-07 | Indica una cantidad igual a 0, con decimales, mayor que lo disponible en la ubicación o que lleva lo enviado por encima de lo solicitado más la tolerancia | El sistema no la acepta. |
| AC-08 | Quita algo escaneado | Deja de formar parte del envío. |
| AC-09 | Sale sin confirmar el envío | El envío se descarta y la solicitud no cambia. |

*Confirmar el envío*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-10 | Confirma un envío válido con varios artículos | Se crea un único envío En tránsito, se registra la salida en Inventario, la solicitud queda Enviada con sus cantidades actualizadas y la tarjeta del destino muestra lo que hay por recibir. |
| AC-11 | Otro usuario envió en paralelo y el envío supera lo pendiente actual | Las líneas afectadas se bloquean hasta que se ajusten o quiten. |
| AC-12 | Queda algo por enviar después del envío | La solicitud sigue Enviada y la tarjeta permanece en el to-do list del origen. |
| AC-13 | Se envió el 100 % de todos los artículos | La tarjeta sale del to-do list del origen. |
| AC-14 | El destino rechaza algo que estaba en tránsito y la solicitud no está Trunca | Esa cantidad vuelve a quedar por enviar y la tarjeta reaparece en el to-do list del origen. |

*Truncar y rechazar*

| ID | Si… | Entonces… |
| --- | --- | --- |
| AC-15 | Trunca una solicitud Enviada y confirma | Queda Trunca, no admite nuevos envíos, su tarjeta sale del to-do list del origen, lo que está en tránsito sigue su ciclo y el documento muestra quién la truncó y cuándo. |
| AC-16 | Rechaza una solicitud Pendiente y confirma | Queda Rechazada, su tarjeta sale del to-do list del origen y del destino y el documento muestra quién la rechazó y cuándo. |
| AC-17 | Abre ⋮ | Solo ve la acción disponible: Rechazar si está Pendiente, Truncar si está Enviada. |
| AC-18 | Cancela la confirmación de truncar o rechazar | La solicitud no cambia. |
