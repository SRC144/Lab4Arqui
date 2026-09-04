# Requerimientos Funcionales — RemoteSchooly v3
## Actualizados con objetivos derivados de la fase E — Estimar

> **Alcance:** fase **R — Requerimientos** de REDALE, refinada con los resultados cuantitativos de la fase **E — Estimar**.
>
> Los valores de capacidad incorporados en este documento corresponden al **escenario de referencia de planificación** de la E. No son cifras oficiales del Gobierno y deberán reemplazarse si posteriormente se obtienen datos reales.
>
> Este documento describe **qué debe hacer el sistema**. Las decisiones concretas de tecnología, proveedores, modelos LLM, CDN, RAG, colas, protocolos o productos se reservan para D/L.

## Actores

- **Profesor creador de contenido (Lima):** crea, actualiza, consulta y publica cursos y materiales; utiliza IA con finalidad académica.
- **Profesor remoto:** consulta, descarga y utiliza materiales en localidades con conectividad limitada; registra apuntes y reporta contenido.
- **Estudiante:** consulta y descarga materiales asignados, accede al contenido local, registra apuntes y reporta información.
- **Administrador del Gobierno:** administra usuarios y roles, supervisa IA, puede suspender acceso a IA y realizar publicaciones extraordinarias.
- **Sistema de distribución territorial:** comportamiento automático encargado de propagar, almacenar, sincronizar y validar materiales entre niveles territoriales.

---

1. **RF-001 — Gestión de cursos**
   **Usuario:** Profesor creador de contenido (Lima)

   El sistema deberá:
   * Permitir crear, consultar, actualizar y retirar cursos.
   * Requerir como mínimo: nombre, descripción, nivel/grado, área académica, responsable y estado.
   * Permitir crear y actualizar cursos únicamente a profesores creadores autorizados.
   * Impedir publicar un curso sin responsable, nivel/grado y área académica definidos.
   * Mantener el estado actual y fecha de última actualización.
   * Al retirar un curso, impedir nuevas asignaciones sin eliminar su historial.

2. **RF-002 — Gestión de materiales educativos**
   **Usuario:** Profesor creador de contenido (Lima)

   El sistema deberá:
   * Permitir crear, cargar, consultar, actualizar y retirar materiales.
   * Asociar cada material a un curso y semana/período académico.
   * Registrar título, tipo, curso, período, autor, fecha de publicación y versión.
   * Crear una nueva versión cuando se actualice un material previamente publicado.
   * Impedir publicar materiales no asociados a un curso activo.
   * Rechazar cargas inválidas o incompletas informando el motivo.

3. **RF-003 — Publicación de paquete semanal**
   **Usuario:** Profesor creador de contenido (Lima)

   El sistema deberá:
   * Permitir seleccionar materiales para conformar un paquete semanal.
   * Registrar curso, semana, fecha de publicación, versión y responsable.
   * Impedir publicar el paquete si contiene materiales incompletos o en borrador.
   * Mantener una única versión vigente por curso y semana.
   * Conservar versiones anteriores para consulta.
   * Entregar el paquete vigente al sistema de distribución territorial.

4. **RF-004 — Asignación territorial y académica**
   **Usuarios:** Administrador del Gobierno / Profesor creador

   El sistema deberá:
   * Asociar cursos a colegios, regiones, provincias, distritos, municipios o grupos académicos.
   * Asociar profesores remotos y estudiantes a los cursos correspondientes.
   * Determinar qué subconjunto de materiales corresponde a cada nodo territorial y colegio.
   * Impedir acceso a materiales de cursos no asignados.
   * Aplicar cambios de asignación sin eliminar automáticamente contenido local válido que deba conservarse.

5. **RF-005 — Distribución jerárquica de contenido**
   **Responsable principal:** Sistema de distribución territorial

   El sistema deberá:
   * Distribuir contenido siguiendo el modelo híbrido de referencia: Lima → hubs provinciales → hubs distritales → nodos municipales/locales → colegios.
   * Permitir omitir un nivel intermedio cuando la topología configurada de una localidad no lo requiera.
   * Entregar a cada nivel únicamente el contenido correspondiente a sus destinos dependientes.
   * Registrar nodo origen, nodo destino, paquete, versión y resultado de cada transferencia.
   * Evitar que cada colegio tenga que descargar individualmente el catálogo completo desde Lima.

6. **RF-006 — Almacenamiento territorial de materiales**
   **Responsable principal:** Sistema de distribución territorial

   El sistema deberá:
   * Mantener copias locales de los materiales vigentes en los nodos territoriales configurados.
   * Permitir que hubs provinciales, distritales y municipales atiendan a los nodos dependientes.
   * Conservar las versiones requeridas durante el período de retención definido.
   * Identificar qué contenido está disponible localmente y qué contenido permanece pendiente de sincronización.
   * No eliminar la última versión válida por una transferencia incompleta.

7. **RF-007 — Consulta de cursos y materiales disponibles**
   **Usuarios:** Profesor remoto / Estudiante

   El sistema deberá:
   * Mostrar únicamente cursos y materiales autorizados.
   * Indicar título, curso, semana, versión, tamaño y disponibilidad local.
   * Identificar versiones más recientes que la copia descargada.
   * Diferenciar contenido pendiente, descargado, actualizado y disponible offline.

8. **RF-008 — Descarga de materiales**
   **Usuarios:** Profesor remoto / Estudiante

   El sistema deberá:
   * Permitir descargar uno o varios materiales autorizados.
   * Preferir la copia disponible en el nodo territorial/local más cercano configurado.
   * Mostrar progreso y estado final.
   * Conservar localmente los materiales descargados correctamente.
   * No marcar un material como completo hasta validar su recepción.
   * Informar cuando una descarga no pueda completarse.

9. **RF-009 — Transferencia segmentada**
   **Responsable principal:** Sistema

   El sistema deberá:
   * Transferir materiales mediante segmentos independientes.
   * Registrar segmentos recibidos y pendientes.
   * Conservar segmentos válidos ante interrupciones.
   * Evitar retransmitir segmentos ya validados salvo cambio de versión o corrupción.
   * Permitir utilizar como unidad de referencia de planificación segmentos de aproximadamente 64 MB, sin convertir dicho valor en una dependencia tecnológica obligatoria.

10. **RF-010 — Reanudación de descarga interrumpida**
    **Responsable principal:** Sistema

    El sistema deberá:
    * Detectar descargas incompletas.
    * Continuar con los segmentos pendientes al recuperar conectividad.
    * Evitar reiniciar una descarga completa cuando los segmentos almacenados sigan siendo válidos.
    * Ante cambio de versión, obtener la versión vigente sin presentar una mezcla de versiones.
    * Volver a solicitar únicamente segmentos corruptos o incompletos.

11. **RF-011 — Consulta offline**
    **Usuarios:** Profesor remoto / Estudiante

    El sistema deberá:
    * Permitir abrir materiales descargados sin Internet.
    * Identificar qué materiales están disponibles offline.
    * Mantener disponible la última versión válida durante una actualización.
    * No bloquear contenido local por imposibilidad de comunicarse con Lima o con un nodo superior.

12. **RF-012 — Sincronización territorial de novedades**
    **Responsable principal:** Sistema de distribución territorial

    El sistema deberá:
    * Comparar versiones entre nodo origen y nodo destino.
    * Detectar contenido nuevo, modificado o retirado.
    * Transferir únicamente la información necesaria para llevar al destino a la versión vigente.
    * Mantener la última versión válida hasta completar la nueva.
    * Permitir sincronizaciones en ventanas programadas de 12 horas y, para zonas críticas, de hasta 24 horas.
    * Registrar inicio, finalización, volumen transferido y resultado.

13. **RF-013 — Verificación de integridad**
    **Responsable principal:** Sistema

    El sistema deberá:
    * Validar cada material antes de marcarlo como completo.
    * Detectar transferencias incompletas o contenido alterado.
    * Rechazar descargas cuya verificación falle.
    * Solicitar nuevamente las partes necesarias cuando sea posible.
    * Registrar la versión validada exitosamente.

14. **RF-014 — Obtención desde nodo alternativo autorizado**
    **Responsable principal:** Sistema de distribución territorial

    Cuando el enlace con el nodo padre esté temporalmente degradado, el sistema deberá:
    * Permitir consultar si un nodo hermano autorizado posee la misma versión requerida.
    * Utilizar el nodo hermano únicamente cuando la política territorial lo permita.
    * Validar integridad y versión del contenido recibido de la misma forma que desde el nodo padre.
    * Registrar que la transferencia fue atendida desde un nodo alternativo.
    * Volver a utilizar el nodo padre como fuente normal cuando su enlace esté disponible.

15. **RF-015 — Gestión de apuntes**
    **Usuarios:** Profesor remoto / Estudiante

    El sistema deberá:
    * Permitir crear, consultar, actualizar y eliminar apuntes personales.
    * Asociarlos a un curso y opcionalmente a un material.
    * Mantenerlos disponibles localmente.
    * Impedir el acceso a apuntes privados de otros usuarios.

16. **RF-016 — Reporte de contenido incorrecto o desactualizado**
    **Usuarios:** Profesor remoto / Estudiante

    El sistema deberá:
    * Permitir reportar un material indicando motivo.
    * Registrar usuario, fecha, curso, material y versión.
    * Permitir al profesor creador y Administrador consultar reportes.
    * Mantener cada reporte ligado a la versión exacta.
    * Confirmar la recepción del reporte.

17. **RF-017 — Consulta académica mediante IA**
    **Usuario:** Profesor creador de contenido (Lima)

    El sistema deberá:
    * Permitir solicitudes relacionadas con creación, explicación, revisión o desarrollo académico.
    * Asociar cada solicitud con usuario y contexto académico cuando corresponda.
    * Mostrar la respuesta generada.
    * Registrar el consumo de cada solicitud.
    * Informar cuando no pueda procesarse.

18. **RF-018 — Validación académica de solicitudes de IA**
    **Responsable principal:** Sistema

    El sistema deberá:
    * Aceptar solicitudes relacionadas con creación, preparación, explicación, revisión o desarrollo educativo.
    * Rechazar uso personal, entretenimiento, planificación privada, asistencia personal u otros objetivos no académicos.
    * Rechazar antes del mecanismo principal de generación cuando sea posible.
    * Informar el motivo general.
    * Registrar profesor, fecha, categoría y motivo del rechazo.

19. **RF-019 — Clasificación de complejidad de IA**
    **Responsable principal:** Sistema

    El sistema deberá clasificar solicitudes aceptadas en:
    * **Simple:** preguntas directas, respuestas breves, resúmenes o reformulaciones sin relaciones complejas ni multimedia.
    * **Media:** relaciones entre múltiples conceptos, documentos, comparaciones o respuestas compuestas.
    * **Compleja de texto:** tareas textuales de mayor razonamiento/contexto.
    * **Multimedia:** generación o procesamiento de imágenes, video u otros medios de costo diferenciado.

    Además deberá:
    * Registrar categoría inicial y final.
    * Permitir reclasificación justificada.
    * Mantener trazabilidad del cambio.

20. **RF-020 — Selección del mecanismo de procesamiento de IA**
    **Responsable principal:** Sistema

    El sistema deberá:
    * Seleccionar el mecanismo autorizado de menor consumo capaz de atender la solicitud.
    * Utilizar la clasificación de RF-019.
    * Escalar a un mecanismo de mayor capacidad únicamente cuando sea necesario.
    * Registrar mecanismo y consumo.
    * Aplicar las mismas reglas académicas y de permisos a todos los mecanismos.

21. **RF-021 — Control del presupuesto de IA**
    **Responsable principal:** Sistema

    El sistema deberá:
    * Aplicar los presupuestos máximos definidos para cada categoría.
    * Estimar consumo antes de ejecutar cuando sea posible.
    * Impedir una ejecución que exceda el presupuesto en las mismas condiciones.
    * Solicitar reducción, división o reformulación.
    * Registrar rechazos, ajustes y reclasificaciones por consumo.

22. **RF-022 — Reutilización de información académica**
    **Responsable principal:** Sistema

    El sistema deberá:
    * Consultar información académica autorizada ya disponible cuando sea pertinente.
    * Reutilizar resultados válidos cuando eviten una generación completa.
    * Impedir reutilización de contenido no autorizado.
    * Registrar si la solicitud fue resuelta total o parcialmente con información existente.

23. **RF-023 — Monitoreo del consumo de IA**
    **Usuarios:** Administrador del Gobierno / Profesor creador, según permisos

    El sistema deberá:
    * Registrar por solicitud: usuario, fecha, curso, categoría, mecanismo, tokens de entrada, salida y total.
    * Registrar separadamente operaciones multimedia.
    * Permitir consultar consumo por usuario, curso, categoría y período.
    * Permitir comparar consumo con la línea base de la E.
    * Identificar solicitudes rechazadas antes de generación.
    * Restringir acceso según rol.

24. **RF-024 — Gestión de usuarios y roles**
    **Usuario:** Administrador del Gobierno

    El sistema deberá:
    * Crear, activar y desactivar usuarios.
    * Asignar los roles Profesor creador, Profesor remoto, Estudiante y Administrador.
    * Impedir acceso de usuarios desactivados.
    * Registrar cambios de rol o estado.
    * Aplicar permisos definidos en cursos, materiales, IA y administración.

25. **RF-025 — Control administrativo del uso indebido de IA**
    **Usuario:** Administrador del Gobierno

    El sistema deberá:
    * Consultar historial de solicitudes rechazadas.
    * Mostrar incidencias por profesor y período.
    * Suspender temporalmente únicamente el acceso de IA sin desactivar necesariamente la cuenta.
    * Requerir motivo y duración.
    * Registrar administrador, fecha, motivo y duración.
    * Permitir reactivación posterior.
    * Mantener historial de suspensión y reactivación.

26. **RF-026 — Publicación administrativa de emergencia**
    **Usuario:** Administrador del Gobierno

    El sistema deberá:
    * Permitir publicar materiales extraordinariamente ante urgencia, fallo o ausencia del responsable.
    * Requerir curso, material y motivo.
    * Aplicar las mismas validaciones de integridad y versionamiento.
    * Registrar administrador, fecha y motivo.
    * Identificar la operación como intervención administrativa.
    * Conservar historial.

27. **RF-027 — Monitoreo de distribución territorial**
    **Usuario:** Administrador del Gobierno

    El sistema deberá:
    * Permitir consultar estado de sincronización por hub provincial, distrital, municipal y colegio.
    * Mostrar volumen transferido, versión vigente, última sincronización y transferencias pendientes.
    * Identificar nodos que no hayan completado la distribución del paquete semanal.
    * Permitir filtrar por nivel territorial y estado.
    * No permitir alterar manualmente la integridad o versión reportada sin una nueva sincronización válida.

28. **RF-028 — Trazabilidad de operaciones críticas**
    **Usuario:** Administrador del Gobierno

    El sistema deberá:
    * Registrar usuario/servicio, rol, fecha y hora, operación, entidad o nodo afectado y resultado.
    * Incluir publicaciones, cambios de asignación, cambios de roles, uso de IA, rechazos, suspensiones, publicaciones de emergencia y transferencias territoriales.
    * Permitir consulta por usuario, operación, nodo y rango de fechas.
    * Impedir modificación no autorizada de registros históricos.
