# Administrador del Gobierno — RemoteSchooly

## Descripción del usuario

El **Administrador del Gobierno** es responsable de supervisar y administrar la operación de RemoteSchooly. Gestiona usuarios y roles, controla el uso adecuado de la IA, puede intervenir ante situaciones extraordinarias de publicación y monitorea la correcta distribución de materiales hacia las localidades remotas.

A diferencia de un administrador puramente técnico, también tiene responsabilidades operativas vinculadas con el cumplimiento del objetivo educativo del sistema.

## Necesidades del Administrador del Gobierno

### 1. Gestión de usuarios y roles

Necesita controlar quién puede acceder a RemoteSchooly y qué funciones puede realizar.

Debe poder:

* Crear usuarios.
* Activar y desactivar usuarios.
* Asignar los roles Profesor creador, Profesor remoto, Estudiante y Administrador.
* Modificar roles.
* Consultar el estado de los usuarios.
* Impedir el acceso de usuarios desactivados.
* Mantener trazabilidad de cambios de rol y estado.

### 2. Gestión de asignaciones académicas y territoriales

Necesita controlar cómo los cursos y materiales se relacionan con colegios y localidades.

Debe poder:

* Asociar cursos a colegios, regiones, provincias, distritos o municipios.
* Consultar qué contenido corresponde a cada localidad.
* Verificar qué usuarios pertenecen a cada curso o colegio.
* Corregir asignaciones cuando exista un error administrativo.

### 3. Monitoreo de distribución territorial

Necesita saber si los materiales están llegando correctamente desde Lima hacia los distintos niveles territoriales.

Debe poder consultar:

* Estado de sincronización de hubs provinciales.
* Estado de hubs distritales.
* Estado de nodos municipales/locales.
* Estado de colegios.
* Última sincronización.
* Versión disponible en cada nodo.
* Volumen transferido.
* Materiales pendientes.
* Nodos con errores o transferencias incompletas.

### 4. Supervisión del uso de IA

Necesita controlar que la IA sea utilizada únicamente con finalidad académica y dentro de los límites establecidos.

Debe poder consultar:

* Solicitudes procesadas.
* Solicitudes rechazadas.
* Profesor responsable.
* Categoría de complejidad.
* Tokens de entrada.
* Tokens de salida.
* Consumo total.
* Uso por curso y período.
* Reincidencia de solicitudes no académicas.

### 5. Suspensión por uso indebido de IA

Necesita actuar cuando un profesor utilice repetidamente la IA para fines no permitidos.

Debe poder:

* Consultar el historial de incidencias de un profesor.
* Suspender temporalmente su acceso a IA.
* Mantener activas las demás funciones del profesor cuando corresponda.
* Registrar el motivo de la suspensión.
* Definir la duración.
* Reactivar posteriormente el acceso.
* Mantener historial de suspensión y reactivación.

### 6. Seguimiento del objetivo de reducción de consumo de IA

Necesita verificar que las optimizaciones realmente reduzcan el consumo de IA.

Debe poder consultar:

* Línea base de consumo.
* Consumo actual.
* Tokens promedio por categoría.
* Solicitudes rechazadas antes de generación.
* Distribución entre solicitudes simples, medias, complejas y multimedia.
* Porcentaje de reducción conseguido.
* Comparación con el objetivo mínimo de 40 %.

### 7. Publicación administrativa de emergencia

Necesita intervenir cuando exista una urgencia, fallo operativo o imposibilidad del profesor responsable para publicar material.

Debe poder:

* Seleccionar el curso afectado.
* Cargar o publicar el material necesario.
* Registrar el motivo de la intervención.
* Mantener la versión anterior.
* Aplicar las mismas validaciones de integridad y versionamiento.
* Identificar claramente la publicación como administrativa y extraordinaria.

### 8. Auditoría y trazabilidad

Necesita poder reconstruir las operaciones relevantes realizadas en RemoteSchooly.

Debe poder conocer:

* Qué usuario o nodo realizó una acción.
* Fecha y hora.
* Rol o tipo de nodo.
* Recurso afectado.
* Nodo origen y destino cuando corresponda.
* Resultado.
* Cambios de usuarios y roles.
* Publicaciones y versiones.
* Transferencias territoriales.
* Uso de IA.
* Suspensiones y reactivaciones.
* Publicaciones administrativas de emergencia.

### 9. Supervisión de capacidad del escenario inicial

Necesita conocer si la plataforma está operando dentro de los objetivos obtenidos durante la estimación.

El escenario inicial de planificación contempla:

* 24 hubs provinciales.
* 120 hubs distritales.
* 600 nodos municipales/locales.
* 3 000 colegios.
* Aproximadamente 32.50 TB/semana de tráfico territorial agregado.
* Aproximadamente 337.96 TB de almacenamiento distribuido con margen.
* Aproximadamente 4 000 solicitudes de IA por día.

Debe poder identificar cuando la operación se acerque o supere estos valores para que el crecimiento sea evaluado posteriormente.
