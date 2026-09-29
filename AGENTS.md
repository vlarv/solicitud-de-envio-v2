# AGENTS.md — solicitud-de-envio-v2

Este archivo define el comportamiento de los agentes y la gobernanza de la documentación del proyecto. No contiene requisitos de producto, decisiones de arquitectura ni decisiones de diseño; esos pertenecen a los documentos indicados en [Autoridad de la documentación](#autoridad-de-la-documentación).

## Idioma

Toda la documentación del proyecto se redacta en español. Los nombres de archivo conservan su forma canónica (por ejemplo, `docs/conceptual_engineering.md`).

## Colaboración crítica y descubrimiento de una pregunta a la vez

- Evaluar las propuestas críticamente en lugar de aceptarlas automáticamente; señalar debilidades, conflictos, supuestos y compensaciones materiales.
- Explicar ventajas, desventajas y ejemplos concretos cuando una decisión sea abstracta o tenga consecuencias relevantes.
- No inferir decisiones de producto ni técnicas faltantes. Inspeccionar primero la evidencia disponible (archivos, sistemas existentes, diseños, herramientas accesibles) y no pedir al usuario lo que ya se puede establecer.
- Resolver cada ambigüedad material con descubrimiento de una sola pregunta a la vez: una pregunta concisa con 2–4 opciones mutuamente excluyentes, esperar la respuesta y resolverla antes de pasar a la siguiente.
- Distinguir claramente hechos confirmados, inferencias respaldadas por evidencia, recomendaciones, incógnitas e ideas diferidas.
- Tratar el contenido generado o extraído como propuesta; solo es autoritativo tras la aprobación del usuario y su inclusión en la especificación dueña.
- Detener el descubrimiento cuando exista información suficiente.

## Documentación autorizada y límites de creación de archivos

Solo se permiten estos archivos de documentación:

Raíz del repositorio:

- `AGENTS.md`
- `README.md`
- `roadmap.md`
- `logbook.md`

Bajo `docs/`:

- `conceptual_engineering.md`
- `technical_spec.md`
- `design.md`
- `module_spec_[module_name].md`, uno por módulo aprobado, solo en proyectos multimódulo
- `jtbd_[module_name]_[job_number].md`, uno por trabajo aprobado entregable de forma independiente

Reglas:

- La lista no autoriza crear todos los archivos. Se requiere aprobación explícita del usuario para cada archivo exacto o conjunto coherente de archivos antes de escribirlo.
- No crear documentación suplementaria (índices, registros de decisiones, resúmenes, planes, archivos de arquitectura, README anidados) ni dividir un documento autorizado sin permiso explícito.
- No crear documentos vacíos solo porque están permitidos.
- Usar nombres de módulo semánticos e identificadores estables.

## Plantillas canónicas

- Las plantillas canónicas son las incluidas en el skill `project-docs` instalado (carpeta `templates/` del skill). No se registra aquí una ruta absoluta específica de la máquina.
- Las plantillas son de solo lectura. Nunca modificarlas ni repararlas sin permiso explícito para la plantilla exacta.
- Antes de descubrir, redactar o cambiar un documento gobernado, leer su plantilla completa y vigente.
- `AGENTS.md` y `README.md` no usan plantilla.
- Si falta una plantilla, se bloquea solo el documento correspondiente. Si plantillas relacionadas se contradicen, se bloquean todos los documentos afectados hasta que el usuario resuelva el conflicto.
- Al escribir desde una plantilla, eliminar sus instrucciones y no añadir, eliminar, debilitar ni generalizar secciones requeridas. Si una sección requerida no aplica, conservar su encabezado y escribir exactamente `not relevant in this entry`.

## Proyectos de un módulo y multimódulo

- Determinar durante el descubrimiento conceptual si el proyecto es de un módulo o multimódulo; no suponerlo por el tamaño del repositorio ni por la cantidad de pantallas.
- Un módulo: `docs/conceptual_engineering.md` sirve también como especificación del módulo y contiene los estados de negocio, el inventario de pantallas y el índice de JTBD. No se crea especificación de módulo redundante.
- Multimódulo: `docs/conceptual_engineering.md` contiene la información de todo el sistema y la que cruza módulos, e indexa cada módulo. Sus secciones de estados y pantallas remiten a las especificaciones de módulo. Cada `module_spec_[module_name].md` contiene solo el límite del módulo, conceptos y reglas compartidos por sus JTBD, dependencias y exclusiones, estados de negocio, inventario de pantallas e índice de JTBD.
- Cada JTBD es un archivo separado que representa un único resultado de usuario entregable de forma independiente. El trabajo en paralelo solo se permite cuando las dependencias lo admiten.

## Autoridad de la documentación y límites de duplicación

Cada hecho vive en el documento más específico que lo posee; los demás lo referencian en lugar de copiarlo.

- `docs/conceptual_engineering.md`: problema, contexto, alcance atemporal del flujo, usuarios y permisos, entradas, salidas, estructura de módulos y, en proyectos de un módulo, estados, pantallas, capacidades visibles, acciones y límites de responsabilidad. Sin lenguaje de secuenciación de entregas.
- `docs/module_spec_[module_name].md`: información compartida por varios JTBD dentro de un módulo.
- `docs/jtbd_[module_name]_[job_number].md`: rol, situación desencadenante, progreso y resultado deseados, solución funcional esperada, campos, condiciones límite y criterios de aceptación de un trabajo.
- `docs/design.md`: sistema visual compartido, navegación, patrones de interacción, guía de componentes reutilizables, accesibilidad y comportamiento responsivo.
- `docs/technical_spec.md`: arquitectura, tecnología, dirección de dependencias, puertos de aplicación/dominio, adaptadores de persistencia, esquema autoritativo y migraciones, interfaces, infraestructura, seguridad, pruebas, despliegue y decisiones de implementación.
- `roadmap.md`: alcance de entregas o hitos, secuenciación, dependencias, estado de trabajo en paralelo, horizontes de confianza, criterios de finalización, fechas comprometidas y estado actual.
- `logbook.md`: narrativa histórica de solo anexado; nunca es autoridad vigente.
- `README.md`: punto de entrada conciso, puntos de entrada de configuración y enlaces; sin especificaciones duplicadas.
- `AGENTS.md`: solo comportamiento de agentes y gobernanza de la documentación.

Orden de dependencia por defecto: `AGENTS.md` → `docs/conceptual_engineering.md` (debe aprobarse antes de descubrir cualquier otro documento) → especificaciones de módulo (solo multimódulo) → JTBD → `docs/design.md` → `docs/technical_spec.md` → `roadmap.md` → `README.md` → `logbook.md` (solo cuando exista una entrada elegible y con aprobación separada).

Si documentos de autoridad vigente se contradicen, detenerse, explicar el conflicto exacto y pedir al usuario que resuelva la propiedad antes de continuar.

## Control de cambios de especificaciones

- Las especificaciones son la verdad vigente aprobada, no notas de trabajo.
- Discutir y refinar los cambios sin editar durante la discusión.
- Al final de cada tema coherente de descubrimiento, presentar juntos los cambios propuestos.
- Modificar especificaciones solo tras aprobación explícita del usuario.
- Actualizar juntas todas las especificaciones afectadas y eliminar o reemplazar los requisitos superados.

## Reglas de actualización del roadmap

- Actualizar `roadmap.md` cuando ocurra un cambio importante de plan o se alcance un hito, con aprobación explícita del usuario.
- Las fases, el orden de implementación, las dependencias entre hitos y los tiempos de entrega viven solo en `roadmap.md`.

## Logbook: momento y permiso separado

- Anexar a `logbook.md` solo al final de una jornada de trabajo, al superar un bloqueo importante o al alcanzar un hito.
- Pedir siempre permiso antes de cada anexado. El permiso para cambiar una especificación o el roadmap no autoriza un anexado al logbook.
- Nunca reescribir entradas existentes del logbook.

## Prohibición de implementación

- El trabajo de documentación nunca implica permiso para escribir código de la aplicación.
- No comenzar la implementación sin autorización explícita y separada del usuario.
