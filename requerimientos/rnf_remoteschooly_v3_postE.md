# Requerimientos No Funcionales — RemoteSchooly v3
## Actualizados con objetivos cuantitativos derivados de E — Estimar

> **Importante:** los valores de capacidad siguientes corresponden al **escenario de referencia de planificación** definido en la fase E. No son todavía cifras oficiales del Gobierno del Perú.
>
> La E dimensionó un escenario de 24 hubs provinciales, 120 distritales, 600 nodos municipales/locales, 3 000 colegios, 8 GB/semana por colegio y una ventana normal de sincronización de 12 horas.

---

1. **RNF-001 — Operación con conectividad intermitente**
   **Usuarios afectados:** Profesor remoto / Estudiante
   * Mantener materiales descargados ante pérdida total de conectividad.
   * Conservar progreso de descargas interrumpidas.
   * Continuar procesos al recuperar conectividad.
   * No reiniciar desde cero una transferencia cuyos segmentos sigan siendo válidos.

2. **RNF-002 — Integridad de materiales**
   **Usuarios afectados:** Profesor creador / Profesor remoto / Estudiante
   * Detectar transferencias incompletas o alteradas.
   * No marcar como vigente contenido cuya validación falle.
   * Mantener identificable versión publicada y versión validada.
   * Aplicar la misma validación a contenido proveniente de Lima, nodos padres o nodos hermanos.

3. **RNF-003 — Capacidad territorial inicial**
   **Usuarios beneficiados:** Gobierno / Administrador
   El sistema deberá soportar como mínimo el escenario de referencia:
   * **24 hubs provinciales**.
   * **120 hubs distritales**.
   * **600 nodos municipales/locales**.
   * **3 000 colegios remotos**.

4. **RNF-004 — Fan-out territorial objetivo**
   **Usuarios beneficiados:** Gobierno / Administrador
   El modelo deberá poder operar aproximadamente con:
   * 1 hub provincial por **5 hubs distritales**.
   * 1 hub distrital por **5 nodos municipales**.
   * 1 nodo municipal por **5 colegios**.
   * Permitir variaciones de esta relación por localidad mediante configuración.

5. **RNF-005 — Volumen semanal de contenido**
   **Usuarios afectados:** Nodos territoriales / Colegios
   El sistema deberá soportar como referencia:
   * **24 GB/semana por hub provincial**.
   * **16 GB/semana por hub distrital**.
   * **10 GB/semana por nodo municipal**.
   * **8 GB/semana por colegio**.

6. **RNF-006 — Capacidad de tráfico agregado**
   **Usuarios beneficiados:** Gobierno / Administrador
   El sistema deberá gestionar aproximadamente:
   * **0.576 TB/semana** Lima → provincial.
   * **1.92 TB/semana** provincial → distrital.
   * **6.00 TB/semana** distrital → municipal.
   * **24.00 TB/semana** municipal → colegios.
   * **32.50 TB/semana** de tráfico agregado territorial.

7. **RNF-007 — Eficiencia del enlace central**
   **Usuarios beneficiados:** Gobierno / Administrador
   * Mantener el tráfico directo desde Lima alrededor de **0.576 TB/semana** para la carga de referencia.
   * Utilizar **24 TB/semana** como comparación de un modelo centralizado equivalente.
   * Mantener como objetivo de planificación una reducción aproximada de **97.6 %** del tráfico directo desde Lima.

8. **RNF-008 — Retención local**
   **Usuarios afectados:** Nodos territoriales / Colegios
   * Mantener inicialmente **8 semanas** de contenido.
   * Conservar la última versión válida durante el período de retención.

9. **RNF-009 — Capacidad de almacenamiento por nodo**
   **Usuarios beneficiados:** Gobierno / Administrador
   Con 8 semanas de retención y 30 % de margen:
   * Hub provincial: **≈ 250 GB útiles**.
   * Hub distrital: **≈ 170 GB útiles**.
   * Nodo municipal: **≈ 110 GB útiles**.
   * Colegio: **≈ 85 GB útiles**.
   Estos valores son objetivos de capacidad, no especificaciones de hardware.

10. **RNF-010 — Almacenamiento distribuido agregado**
    **Usuarios beneficiados:** Gobierno / Administrador
    * Almacenamiento base de referencia: **≈ 259.97 TB**.
    * Con 30 % de margen: **≈ 337.96 TB**.

11. **RNF-011 — Ventana normal de sincronización**
    **Usuarios afectados:** Nodos territoriales / Colegios
    La sincronización semanal deberá completarse dentro de **12 horas** bajo las siguientes condiciones promedio de referencia:
    * Lima → provincial: 24 GB con **≈ 4.44 Mbps**.
    * Provincial → distrital: 16 GB con **≈ 2.96 Mbps**.
    * Distrital → municipal: 10 GB con **≈ 1.85 Mbps**.
    * Municipal → colegio: 8 GB con **≈ 1.48 Mbps**.

12. **RNF-012 — Capacidad agregada del enlace de Lima**
    **Usuarios beneficiados:** Gobierno / Administrador
    Para 576 GB en 12 horas:
    * Demanda media: **≈ 106.7 Mbps agregados**.
    * Capacidad objetivo de planificación con margen: **≈ 160 Mbps agregados**.

13. **RNF-013 — Sincronización extendida para zonas críticas**
    **Usuarios afectados:** Profesor remoto / Estudiante
    * Permitir una ventana de hasta **24 horas**.
    * 8 GB hacia un colegio: **≈ 0.74 Mbps promedio**.
    * 10 GB hacia un nodo municipal: **≈ 0.93 Mbps promedio**.
    * El contenido deberá estar disponible antes del período académico correspondiente.

14. **RNF-014 — Eficiencia de reanudación**
    **Usuarios afectados:** Nodos territoriales / Colegio
    * Evitar retransmitir segmentos validados.
    * Registrar volumen retransmitido.
    * Caso de referencia: si una descarga de 8 GB se corta al 75 %, deberán quedar aproximadamente **2 GB** por transferir, salvo corrupción o cambio de versión.

15. **RNF-015 — Carga de transferencias segmentadas**
    **Usuarios beneficiados:** Gobierno / Administrador
    Con segmentos de referencia de 64 MB:
    * 8 GB ≈ **128 segmentos**.
    * 3 000 colegios ≈ **384 000 transferencias de segmento/semana**.
    * En 12 horas ≈ **8.9 solicitudes de segmento/segundo**.

16. **RNF-016 — Disponibilidad local de contenido**
    **Usuarios afectados:** Profesor remoto / Estudiante
    * Permitir abrir materiales validados sin depender del enlace con Lima.
    * Mantener última versión válida durante actualizaciones.
    * No requerir comunicación remota por cada apertura.

17. **RNF-017 — Consistencia de versiones**
    **Usuarios afectados:** Todos los usuarios
    * Identificar inequívocamente la versión vigente.
    * No sustituir una versión válida hasta validar la nueva.
    * Evitar mezclar segmentos de versiones distintas.

18. **RNF-018 — Autenticación**
    **Usuarios afectados:** Todos los usuarios
    * Exigir autenticación para funcionalidades privadas.
    * Identificar de forma única al usuario.
    * Impedir acceso de usuarios desactivados.
    * Registrar eventos relevantes.

19. **RNF-019 — Autorización basada en roles**
    **Usuarios afectados:** Todos los usuarios
    * Diferenciar Profesor creador, Profesor remoto, Estudiante y Administrador.
    * Aplicar permisos por operación.
    * Denegar por defecto operaciones no autorizadas.

20. **RNF-020 — Seguridad entre nodos territoriales**
    **Usuarios beneficiados:** Gobierno / Administrador
    * Autenticar cada nodo antes del intercambio.
    * Autorizar solo relaciones territoriales configuradas o nodos hermanos permitidos.
    * Impedir solicitudes de contenido fuera del ámbito del nodo.
    * Registrar origen y destino.
    * Aplicar verificación de integridad sin importar la fuente.

21. **RNF-021 — Aislamiento de información académica**
    **Usuarios afectados:** Profesor remoto / Estudiante
    * Impedir acceso a cursos no asignados.
    * Aplicar restricciones a consulta, descarga, sincronización e IA.
    * Mantener privados los apuntes.

22. **RNF-022 — Trazabilidad**
    **Usuarios beneficiados:** Administrador del Gobierno
    Registrar como mínimo usuario o nodo, rol/tipo de nodo, fecha/hora, operación, recurso, origen/destino cuando aplique y resultado.

23. **RNF-023 — Respaldo de información central**
    **Usuarios beneficiados:** Gobierno / Profesor creador
    * Respaldar cursos, metadatos, versiones, usuarios, asignaciones y métricas de IA.
    * Registrar el resultado.
    * Permitir comprobar recuperación.
    * Frecuencia, RPO y RTO siguen fuera del alcance actual por no requerirse reliability avanzada.

24. **RNF-024 — Uso exclusivamente académico de IA**
    **Usuario afectado:** Profesor creador
    * Permitir solo solicitudes con finalidad educativa.
    * Rechazar asistencia personal, entretenimiento, planificación privada y otros usos no académicos.
    * Registrar rechazos.

25. **RNF-025 — Capacidad de usuarios de IA**
    **Usuarios beneficiados:** Gobierno / Administrador
    El escenario deberá soportar:
    * **200 profesores creadores**.
    * **20 interacciones IA por profesor/día** promedio.
    * **≈ 4 000 solicitudes IA/día**.

26. **RNF-026 — Distribución de carga de IA de referencia**
    **Usuarios beneficiados:** Gobierno / Administrador
    Sobre solicitudes académicas aceptadas:
    * **65 % simples**.
    * **25 % medias**.
    * **8 % complejas de texto**.
    * **2 % multimedia**.
    El sistema deberá registrar la distribución real observada.

27. **RNF-027 — Presupuesto máximo para solicitudes simples**
    **Usuario afectado:** Profesor creador
    * Máximo **2 000 tokens de entrada + salida**.
    * Consumo esperado de referencia: **≈ 1 200 tokens**.

28. **RNF-028 — Presupuesto máximo para solicitudes medias**
    **Usuario afectado:** Profesor creador
    * Máximo **5 000 tokens de entrada + salida**.
    * Consumo esperado de referencia: **≈ 3 200 tokens**.

29. **RNF-029 — Presupuesto máximo para solicitudes complejas de texto**
    **Usuario afectado:** Profesor creador
    * Máximo **8 000 tokens de entrada + salida**.
    * Consumo esperado de referencia: **≈ 6 000 tokens**.

30. **RNF-030 — Presupuesto independiente para multimedia**
    **Usuarios beneficiados:** Gobierno / Profesor creador
    * Medir multimedia separadamente de tokens de texto.
    * Registrar tipo de operación y unidad/costo del proveedor.
    * Escenario E: **≈ 72 solicitudes multimedia/día**.
    * El límite monetario queda pendiente de precios reales.

31. **RNF-031 — Línea base de consumo de IA**
    **Usuarios beneficiados:** Gobierno / Administrador
    Mientras no exista medición productiva real, utilizar como línea base de planificación:
    * **4 000 solicitudes/día**.
    * **4 500 tokens promedio/interacción**.
    * **18.00 millones de tokens/día**.
    El sistema deberá permitir reemplazar esta línea base conservando el histórico.

32. **RNF-032 — Reducción mínima del consumo de IA**
    **Usuarios beneficiados:** Gobierno / Administrador
    * Objetivo contractual: **reducción ≥ 40 %**.
    * Consumo textual optimizado proyectado: **≈ 8.02 millones de tokens/día**.
    * Reducción proyectada por la E: **≈ 55.5 %**.
    * El 55.5 % deberá validarse con medición real; no sustituye el requisito mínimo del 40 %.

33. **RNF-033 — Observabilidad de IA**
    **Usuarios beneficiados:** Gobierno / Administrador / Profesor creador
    * Registrar usuario, fecha, curso, categoría, mecanismo, tokens de entrada/salida y total.
    * Registrar multimedia separadamente.
    * Diferenciar solicitudes completadas, rechazadas y fallidas.
    * Permitir agregación por usuario, curso, categoría y período.

34. **RNF-034 — Eficiencia de selección del procesamiento**
    **Usuarios beneficiados:** Gobierno / Profesor creador
    * Preferir el mecanismo autorizado de menor consumo capaz de resolver la solicitud.
    * Registrar mecanismo y categoría.
    * Justificar el uso de mecanismos de mayor costo.

35. **RNF-035 — Protección de información utilizada por IA**
    **Usuarios afectados:** Todos los usuarios
    * Utilizar únicamente información académica necesaria y autorizada.
    * No incorporar datos de otros cursos o usuarios sin permiso.
    * Evitar credenciales y datos sensibles en prompts, respuestas o logs.

36. **RNF-036 — Control administrativo del uso indebido de IA**
    **Usuarios beneficiados:** Gobierno / Administrador
    * Mantener historial de solicitudes rechazadas.
    * Permitir identificar reincidencia.
    * Registrar suspensiones y reactivaciones.
    * Impedir uso de IA mientras exista una suspensión vigente.

37. **RNF-037 — Auditabilidad de publicaciones de emergencia**
    **Usuarios beneficiados:** Gobierno / Profesor creador
    Toda publicación administrativa deberá registrar responsable, fecha, motivo, curso y material; conservar versión previa y cumplir reglas de integridad/versionamiento.

38. **RNF-038 — Escalabilidad del escenario inicial**
    **Usuarios beneficiados:** Gobierno / Administrador
    La plataforma deberá soportar al menos:
    * 24 hubs provinciales.
    * 120 hubs distritales.
    * 600 nodos municipales.
    * 3 000 colegios.
    * 32.50 TB/semana de tráfico territorial.
    * 337.96 TB de almacenamiento distribuido con margen.
    * 384 000 transferencias de segmento/semana.
    * 4 000 solicitudes IA/día.

39. **RNF-039 — Alcance de disponibilidad y confiabilidad**
    **Usuarios afectados:** Todos los usuarios
    * Priorizar integridad y disponibilidad local.
    * No considerar incumplimiento una indisponibilidad temporal de Lima si el contenido local válido continúa disponible.
    * No exigir en esta etapa failover avanzado, multi-región activa ni otros mecanismos de reliability no solicitados.
