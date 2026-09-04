# D — Diseñar el Servicio (RemoteSchooly)

## 1. Tipo de arquitectura
Para **RemoteSchooly** se propone una arquitectura híbrida basada en **3-Tier + Event-Driven + Servicio Independiente de IA**.

La arquitectura **3-Tier** será utilizada como estructura principal de la plataforma para la gestión del sistema core, mientras que el **Servicio de IA** se desacopla como un microservicio/worker independiente para evitar saturar el sistema principal durante picos de uso simultáneo por parte de los profesores.

```text
┌─────────────────────────────────────────────────────────────┐
│                    Capa de Presentación                     │
│    Web / Aplicación para profesores, alumnos y admins       │
└──────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────┐
│                API Gateway / Router de Tráfico               │
└──────────────┬───────────────────────────────┬──────────────┘
                │                               │
                ▼                                ▼
┌──────────────────────────────┐ ┌────────────────────────────┐
│ Capa de Aplicación Principal │ │ Servicio de IA Independiente│
│ (Core 3-Tier)                │ │ (Worker desacoplado)       │
│ - Cursos, Usuarios, Notas    │ │ - Procesamiento de Tokens  │
│ - Paquetes de materiales     │ │ - Conexión con LLM/Prompt  │
│ - Control de cuotas de IA    │ │ - Caché de respuestas IA   │
└──────────────┬───────────────┘ └──────────────┬─────────────┘
                │                               │
                ▼                               ▼
┌─────────────────────────────────────────────────────────────┐
│                         Capa de Datos                       │
│     SQL + Object Storage + Cache (Redis) + Event Bus        │
└─────────────────────────────────────────────────────────────┘
```

Sobre esta estructura se incorpora un mecanismo **Event-Driven** para:
1. La distribución y sincronización territorial de materiales educativos sin bloquear la aplicación.
2. El procesamiento asíncrono de solicitudes pesadas de IA mediante colas de trabajo.

```text
Profesor
   │
   ▼
Publica material
   │
   ▼
Servicio de cursos
   │
   ▼
Evento: MaterialPublicado
   │
   ▼
Cola / sistema de eventos
   │
   ├──────────────► Hub Provincial
   │                     │
   │                     ▼
   │              Evento: MaterialRecibido
   │                     │
   │                     ▼
   │              Hub Distrital
   │                     │
   │                     ▼
   │              Nodo Municipal
   │                     │
   │                     ▼
   │                  Colegio
   │
   └──────────────► Registro de estado
```

### Justificación
- **No un monolito simple:** Un monolito expondría la plataforma a caídas globales si las peticiones de IA (que consumen alta latencia) saturan la capa web. Además, RemoteSchooly necesita distribuir contenido asíncronamente a través de múltiples niveles territoriales con conectividad intermitente.
- **Servicio de IA desacoplado:** Las consultas a la IA generan cargas de procesamiento y red variables e impredecibles. Aislar la IA garantiza que aunque miles de profesores generen rúbricas al mismo tiempo, los estudiantes y administradores puedan seguir usando el sistema core (ver cursos, consultar materiales) con baja latencia y sin interrupciones.
- **Uso de Event-Driven:** Permite desacoplar físicamente la publicación de contenido de su distribución hacia los colegios y absorber los picos de procesamiento de la IA.

---

## 2. Uso de 3-Tier (Core del Sistema)

### Capa de Presentación
Será utilizada por:
- Profesor creador
- Profesor remoto
- Estudiante
- Administrador del Gobierno

Permitirá realizar operaciones como:
- Crear cursos y estructurar contenidos.
- Publicar materiales y generar paquetes.
- Consultar el estado de sincronización territorial.
- Solicitar asistencia de IA para la creación de contenidos.

### Capa de Aplicación (Core)
Contendrá la lógica de negocio principal del sistema:

```text
┌────────────────────────────────────────────┐
│         Capa de Aplicación Principal       │
├────────────────────────────────────────────┤
│ - Gestión de usuarios, roles y permisos    │
│ - Gestión de cursos y estructura académica │
│ - Empaquetado y versión de materiales      │
│ - Orquestación de sincronización           │
│ - Validación y autorización de cuotas IA   │
│ - Monitoreo, auditoría y métricas          │
└────────────────────────────────────────────┘
```

Esta capa será responsable de validar las operaciones antes de acceder a los datos o generar eventos.

---

### Capa de Datos

Se utilizarán diferentes mecanismos de persistencia según el tipo de información.

**SQL**

Para:

* usuarios;
* roles;
* cursos;
* asignaciones;
* versiones;
* estados de sincronización;
* métricas;
* registros de IA.

**Object Storage**

Para:

* PDF;
* imágenes;
* audio;
* video;
* paquetes de contenido.

**Almacenamiento local**

Para mantener los materiales disponibles en los nodos territoriales y colegios.

---

# 3. Uso de Event-Driven

El enfoque Event-Driven se utilizará principalmente para la **distribución de materiales**.

Cuando un profesor publique un paquete semanal, el sistema no intentará realizar toda la distribución dentro de la misma solicitud.

En lugar de ello:

```text
Publicar paquete
      ↓
Guardar metadatos
      ↓
Generar evento
      ↓
Colocar evento en cola
      ↓
Distribución asíncrona
```

Por ejemplo:

```text
MaterialPublicado
MaterialAsignado
PaqueteDisponible
SincronizacionSolicitada
MaterialRecibido
SincronizacionCompletada
```

Los nodos territoriales reaccionarán a los eventos correspondientes y realizarán la transferencia cuando tengan conectividad disponible.

Esto permite que una interrupción de Internet en un nodo no bloquee la publicación del material en Lima ni la distribución hacia otros nodos.

---

# 4. Distribución territorial

La distribución utilizará la jerarquía definida en la fase E:

```text
                    LIMA
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
   HUB PROVINCIAL           HUB PROVINCIAL
          │
          ▼
   HUB DISTRITAL
          │
          ▼
   NODO MUNICIPAL
          │
          ├──────────► COLEGIO 1
          ├──────────► COLEGIO 2
          ├──────────► COLEGIO 3
          ├──────────► COLEGIO 4
          └──────────► COLEGIO 5
```

La E plantea aproximadamente:

* 24 hubs provinciales;
* 120 hubs distritales;
* 600 nodos municipales/locales;
* 3 000 colegios.

Además, plantea un fan-out aproximado de 1:5 entre cada nivel territorial.

Esto permite que un material descargado por un nodo pueda ser reutilizado para los siguientes nodos en lugar de solicitar nuevamente el contenido a Lima.

---

# 5. Sincronización asíncrona

La sincronización será realizada mediante eventos y procesos asíncronos.

```text
Lima
 │
 │ MaterialPublicado
 ▼
Hub Provincial
 │
 │ MaterialDisponible
 ▼
Hub Distrital
 │
 │ MaterialDisponible
 ▼
Nodo Municipal
 │
 │ MaterialDisponible
 ▼
Colegio
```

Cada nodo deberá registrar:

* versión requerida;
* versión disponible;
* segmentos recibidos;
* segmentos pendientes;
* estado de sincronización.

Si la conexión se interrumpe:

```text
Transferencia
     ↓
  Corte de red
     ↓
Guardar progreso
     ↓
Esperar conectividad
     ↓
Reanudar transferencia
     ↓
Validar contenido
     ↓
Actualizar versión
```

De esta manera se cumple el requerimiento de conservar el progreso y continuar la transferencia cuando vuelva la conectividad.

---

# 6. Diseño de la API

La capa de aplicación expondrá una API para las operaciones síncronas del sistema.

### Usuarios

```text
POST /users
GET  /users/{id}
PUT  /users/{id}
```

### Cursos

```text
POST /courses
GET  /courses/{id}
PUT  /courses/{id}
```

### Materiales

```text
POST /courses/{id}/materials
GET  /courses/{id}/materials
GET  /materials/{id}
```

### Publicación

```text
POST /courses/{id}/packages
POST /courses/{id}/packages/{week}/publish
```

La publicación generará posteriormente los eventos necesarios para la distribución.

### Sincronización

```text
GET  /nodes/{id}/content
POST /sync
GET  /sync/{id}/status
POST /sync/{id}/resume
```

### IA

```text
POST /ai/requests
GET  /ai/requests/{id}
GET  /ai/usage
```

### Administración

```text
GET /admin/sync/status
GET /admin/ai/usage
GET /admin/audit
```

---

# 7. Servicio de IA

El servicio de IA también estará ubicado en la capa de aplicación, pero funcionará de manera independiente de la distribución de materiales.

El flujo será:

```text
Profesor
   ↓
Solicitud IA
   ↓
Validación académica
   ↓
Clasificación de complejidad
   ↓
¿Información reutilizable?
   │
   ├── Sí → reutilizar información
   │
   └── No → seleccionar modelo/mecanismo
                    ↓
                Procesamiento
                    ↓
                 Respuesta
                    ↓
             Registrar consumo
```

Las solicitudes se clasificarán en simples, medias, complejas de texto y multimedia.

Esta decisión se basa en la estimación de **4 000 solicitudes de IA por día** y una línea base de **18 millones de tokens diarios**. La estrategia optimizada estima aproximadamente **8.02 millones de tokens diarios**, logrando una reducción aproximada del **55.5 %**, por encima del mínimo requerido del 40 %.

---

# 8. Resumen de la arquitectura

La solución propuesta queda definida de la siguiente manera:

```text
                    REMOTESCHOOLY
                          │
             ┌────────────┴────────────┐
             │                         │
             ▼                         ▼
        ARQUITECTURA 3-TIER       EVENT-DRIVEN
             │                         │
      ┌──────┼──────┐                  │
      │      │      │                  │
      ▼      ▼      ▼                  ▼
 Present. Aplic. Datos          Distribución
                │                sincronización
                │                  asíncrona
                │
        ┌───────┼────────┐
        ▼       ▼        ▼
       SQL   Object    Storage
             Storage   Local
```

Por lo tanto, **3-Tier será la arquitectura base de RemoteSchooly**, mientras que **Event-Driven será un patrón complementario utilizado principalmente para la distribución y sincronización de materiales**.

Esta combinación permite mantener una estructura sencilla para la aplicación y, al mismo tiempo, desacoplar las operaciones de distribución que deben funcionar correctamente incluso cuando existen problemas de conectividad.
