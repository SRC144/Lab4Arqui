# E — Estimación Formal de RemoteSchooly
## Enfoque híbrido de distribución territorial

> **Marco:** fase **E — Estimar** de REDALE.  
> El objetivo de esta etapa es convertir los requerimientos de RemoteSchooly en magnitudes de capacidad: tráfico, almacenamiento, ancho de banda, carga de solicitudes y consumo de IA.  
> Los valores siguientes son **supuestos de planificación** para poder dimensionar una primera arquitectura. No reemplazan datos oficiales del Gobierno ni constituyen todavía decisiones tecnológicas definitivas.

---

## 1. Objetivo de la estimación

RemoteSchooly debe distribuir materiales educativos desde Lima hacia localidades remotas con conectividad limitada y, paralelamente, reducir en al menos 40 % el consumo asociado a IA.

Para la distribución se evaluará un **modelo híbrido jerárquico**:

```text
Lima
  ↓
Hubs provinciales seleccionados
  ↓
Hubs distritales seleccionados
  ↓
Nodos municipales/locales
  ↓
Colegios
  ↓
Estudiantes y profesores por LAN/local
```

No todos los niveles administrativos requieren necesariamente infraestructura propia. La idea del modelo híbrido es colocar nodos de almacenamiento únicamente donde el volumen, distancia o mala conectividad justifique su costo.

Los colegios consumen el contenido desde el nodo local más cercano y los usuarios finales acceden preferentemente mediante la red local del colegio, evitando que cada estudiante descargue el mismo contenido directamente desde Lima.

---

# 2. Hipótesis de planificación

Para completar una primera estimación se usará el siguiente escenario de referencia:

| Variable | Supuesto |
|---|---:|
| Hubs provinciales | 24 |
| Hubs distritales | 120 |
| Nodos municipales/locales | 600 |
| Colegios remotos atendidos | 3000 |
| Catálogo semanal enviado a cada hub provincial | 24 GB |
| Contenido semanal promedio requerido por distrito | 16 GB |
| Contenido semanal promedio requerido por municipio | 10 GB |
| Contenido semanal promedio requerido por colegio | 8 GB |
| Retención local inicial | 8 semanas |
| Margen de almacenamiento | 30 % |
| Ventana objetivo de sincronización | 12 horas |

### Interpretación

El catálogo completo no necesariamente llega a todos los colegios. Cada nivel puede recibir únicamente el subconjunto que corresponde a los cursos, grados y materiales asignados a sus localidades.

Esto permite que:

- Lima distribuya un catálogo amplio a pocos hubs;
- cada hub filtre o replique únicamente el contenido requerido aguas abajo;
- cada colegio mantenga localmente solo los materiales que realmente utiliza.

---

# 3. Estimación de tráfico semanal

## 3.1 Lima → hubs provinciales

Fórmula:

```text
Tráfico = número de hubs × catálogo semanal
```

Cálculo:

```text
24 × 24 GB = 576 GB/semana
```

**Resultado: 0.576 TB/semana desde Lima.**

---

## 3.2 Hubs provinciales → hubs distritales

```text
120 × 16 GB = 1,920 GB/semana
```

**Resultado: 1.92 TB/semana.**

---

## 3.3 Hubs distritales → nodos municipales

```text
600 × 10 GB = 6,000 GB/semana
```

**Resultado: 6.00 TB/semana.**

---

## 3.4 Nodos municipales → colegios

```text
3000 × 8 GB = 24,000 GB/semana
```

**Resultado: 24.00 TB/semana.**

---

## 3.5 Tráfico total interno de distribución

```text
576
+ 1,920
+ 6,000
+ 24,000
= 32,496 GB/semana
```

**Resultado total aproximado: 32.50 TB/semana.**

Este valor representa todo el movimiento dentro de la red, no tráfico saliendo únicamente de Lima.

---

# 4. Comparación del enlace central

Si Lima enviara directamente el paquete promedio de 8 GB a los 3000 colegios:

```text
3000 × 8 GB = 24,000 GB
```

**Modelo centralizado: 24.0 TB/semana saliendo de Lima.**

En el modelo híbrido:

**Lima transmite aproximadamente 0.576 TB/semana.**

Reducción estimada del tráfico de larga distancia originado en Lima:

```text
1 - (576 / 24,000)
= 97.6 %
```

### Resultado

**El modelo híbrido reduce aproximadamente 97.6 % el tráfico semanal que debería salir directamente desde Lima**, bajo estos supuestos.

Esto no elimina el tráfico: lo desplaza hacia enlaces territoriales más cercanos y permite reutilizar una copia para múltiples colegios.

---

# 5. Estimación de almacenamiento distribuido

Se propone conservar inicialmente 8 semanas de materiales en cada nivel.

## 5.1 Por nodo

| Tipo de nodo | Contenido semanal | Retención | Almacenamiento base por nodo | Con 30 % de margen |
|---|---:|---:|---:|---:|
| Provincial | 24 GB | 8 semanas | 192 GB | 249.6 GB |
| Distrital | 16 GB | 8 semanas | 128 GB | 166.4 GB |
| Municipal | 10 GB | 8 semanas | 80 GB | 104.0 GB |
| Colegio | 8 GB | 8 semanas | 64 GB | 83.2 GB |

### Capacidad práctica sugerida para estimación

- Hub provincial: **≈ 250 GB útiles o más**.
- Hub distrital: **≈ 170 GB útiles o más**.
- Nodo municipal: **≈ 110 GB útiles o más**.
- Colegio: **≈ 85 GB útiles o más**.

Estas cifras son capacidad mínima aproximada para este escenario, no especificaciones de hardware.

---

## 5.2 Almacenamiento total distribuido

| Nivel | Almacenamiento base |
|---|---:|
| Provincial | 4.61 TB |
| Distrital | 15.36 TB |
| Municipal | 48.00 TB |
| Colegios | 192.00 TB |
| **Total** | **259.97 TB** |
| **Total + 30 % margen** | **337.96 TB** |

La mayor parte del almacenamiento agregado está en los colegios porque existen muchos más nodos finales. Individualmente, sin embargo, cada colegio requiere una capacidad relativamente pequeña.

---

# 6. Estimación de ancho de banda

Se utilizará una ventana de sincronización de 12 horas.

La fórmula es:

```text
Mbps ≈ GB × 8 000 / segundos disponibles
```

## 6.1 Ancho de banda promedio mínimo por enlace

| Enlace | Datos por destino | Promedio requerido en 12 h |
|---|---:|---:|
| Lima → provincial | 24 GB | 4.44 Mbps |
| Provincial → distrital | 16 GB | 2.96 Mbps |
| Distrital → municipal | 10 GB | 1.85 Mbps |
| Municipal → colegio | 8 GB | 1.48 Mbps |

### Enlace agregado de Lima

Para enviar 576 GB durante la misma ventana:

**≈ 106.7 Mbps agregados.**

Aplicando un margen operativo de 50 % para variaciones, protocolos y reintentos:

**capacidad objetivo de planificación ≈ 160 Mbps agregados desde Lima.**

---

# 7. Ventana alternativa de 24 horas para zonas críticas

En zonas de conectividad muy limitada se puede duplicar la ventana de sincronización de 12 a 24 horas.

Para un colegio:

```text
8 GB / 24 h ≈ 0.74 Mbps promedio
```

Para un nodo municipal:

```text
10 GB / 24 h ≈ 0.93 Mbps promedio
```

Esto permite que la misma arquitectura atienda localidades con enlaces más lentos sin cambiar el volumen educativo semanal, siempre que el material sea precargado antes de la semana de clases.

---

# 8. Impacto de descarga segmentada y reanudación

Para estimación de carga se puede utilizar, sin comprometer todavía el diseño, una unidad de transferencia de referencia de **64 MB**.

Un paquete escolar de 8 GB contendría aproximadamente:

```text
8 GB × 1024 / 64 MB
= 128 segmentos
```

Para 3000 colegios:

```text
3000 × 128
= 384,000 transferencias de segmento por semana
```

Si se concentran en una ventana de 12 horas:

**≈ 8.9 solicitudes de segmentos/segundo en toda la capa de colegios.**

Esto indica que el problema principal no parece ser CPU/RPS, sino **volumen de datos, almacenamiento y calidad de los enlaces**.

### Ejemplo de ahorro por reanudación

Si una descarga de 8 GB falla al 75 %:

- sin reanudación: podrían retransmitirse hasta 8 GB;
- con reanudación: quedarían aproximadamente 2 GB pendientes.

Ahorro potencial:

```text
(8 - 2) / 8 = 75 %
```

El ahorro real dependerá de la frecuencia de cortes y del punto en que ocurran.

---

# 9. Fan-out de la topología

Con los supuestos actuales:

```text
Distritos por hub provincial ≈ 5.0
Municipios por hub distrital ≈ 5.0
Colegios por nodo municipal ≈ 5.0
```

Por tanto, el fan-out promedio es aproximadamente:

- **1 provincial → 5 distritales**
- **1 distrital → 5 municipales**
- **1 municipal → 5 colegios**

Este fan-out es útil porque evita concentrar cientos o miles de conexiones en un solo nodo territorial.

---

# 10. Estrategia de nodos hermanos

La comunicación entre nodos hermanos puede considerarse una optimización secundaria.

Ejemplo:

```text
Municipio A ←→ Municipio B
```

Puede utilizarse cuando:

- el enlace con el nodo padre está temporalmente degradado;
- el municipio vecino ya posee la versión requerida;
- el enlace local entre ambos resulta más económico o estable.

Para la fase E no se asume que todo nodo tendrá esta capacidad. Se recomienda medir posteriormente:

```text
Porcentaje de contenido servido desde nodo padre
vs.
Porcentaje servido desde nodo hermano/local
```

Si el ahorro de tráfico WAN es bajo, no se justifica la complejidad adicional.

---

# 11. Estimación de usuarios y carga funcional

Para dimensionamiento inicial se separa el tráfico educativo del tráfico de control.

Supuestos iniciales:

| Población | Estimación |
|---|---:|
| Colegios | 3,000 |
| Profesores creadores en Lima | 200 |
| Nodos municipales | 600 |
| Hubs distritales | 120 |
| Hubs provinciales | 24 |

Las operaciones de metadatos —consultar versión, confirmar descarga, verificar asignación— son pequeñas frente al tráfico de materiales.

Incluso con aproximadamente 384,000 transferencias de segmento concentradas en 12 horas, la carga agregada estimada es de solo **8.9 solicitudes/s** en la capa final.

La arquitectura deberá dimensionarse principalmente por throughput de archivos y no por RPS de API.

---

# 12. Estimación de IA

## 12.1 Supuestos

| Variable | Supuesto |
|---|---:|
| Profesores creadores | 200 |
| Interacciones IA por profesor/día | 20 |
| Solicitudes IA totales/día | 4,000 |
| Solicitudes no académicas rechazadas | 10 % |
| Promedio actual de referencia | 4,500 tokens/interacción |

Distribución estimada de solicitudes aceptadas:

- 65 % simples
- 25 % medias
- 8 % complejas de texto
- 2 % multimedia

---

## 12.2 Línea base

```text
4,000 solicitudes/día × 4,500 tokens
= 18.00 M tokens/día
```

---

## 12.3 Consumo optimizado de referencia

Se utiliza un consumo promedio conservador por debajo del máximo permitido:

| Tipo | Solicitudes/día | Promedio estimado |
|---|---:|---:|
| Simple | 2,340 | 1,200 tokens |
| Media | 900 | 3,200 tokens |
| Compleja texto | 288 | 6,000 tokens |
| Multimedia | 72 | costo separado |

Adicionalmente se reserva un equivalente conservador de 150 tokens por solicitud para clasificación/validación.

Consumo textual optimizado estimado:

**≈ 8.02 M tokens/día.**

Reducción frente a la línea base:

```text
1 - (8.02 / 18.00)
= 55.5 %
```

### Resultado

**La política propuesta alcanzaría aproximadamente 55.5 % de reducción de tokens de texto bajo estos supuestos**, superando el objetivo mínimo de 40 %.

Las operaciones multimedia se medirán separadamente por costo/unidad de proveedor para evitar mezclar tokens de texto con costos de imagen o video.

---

# 13. Estimación de capacidad por tipo de nodo

A nivel de E no es necesario decidir hardware concreto. Se necesitan clases de capacidad.

| Tipo de nodo | Almacenamiento objetivo inicial | Throughput aproximado | Función dominante |
|---|---:|---:|---|
| Lima | catálogo maestro + histórico | ≥ 160 Mbps agregados de planificación | publicación y distribución |
| Provincial | ≥ 250 GB útiles | ~5 Mbps entrada + fan-out local | cache territorial |
| Distrital | ≥ 170 GB útiles | ~3 Mbps entrada + fan-out local | cache territorial |
| Municipal | ≥ 110 GB útiles | ~2 Mbps entrada + distribución local | cache de cercanía |
| Colegio | ≥ 85 GB útiles | ~1.5 Mbps durante sync de 12 h | consumo offline/LAN |

Estos números incluyen margen aproximado de almacenamiento, pero no redundancia avanzada porque el caso todavía no la exige.

---

# 14. Estimación económica relativa

Sin precios concretos de proveedores, la comparación puede expresarse como componentes de costo:

```text
Costo total ≈
  nodos provinciales
+ nodos distritales
+ nodos municipales
+ almacenamiento de colegios
+ enlaces de datos
+ operación/soporte
+ IA
```

El modelo híbrido busca minimizar:

```text
Costo WAN de larga distancia
```

sin llevar al máximo:

```text
Número de servidores administrados
```

Por eso no se propone instalar un servidor completo en cada división administrativa.

### Principio de decisión

Un nuevo nodo intermedio se justifica cuando:

```text
Ahorro de transferencia + mejora de acceso local
>
Costo de almacenamiento + operación + seguridad del nodo
```

---

# 15. Resultado consolidado de la fase E

## Distribución

- Colegios de referencia: **3,000**
- Tráfico semanal desde Lima en modelo híbrido: **0.576 TB**
- Tráfico equivalente si Lima sirviera directamente a colegios: **24.0 TB**
- Reducción estimada del tráfico directo desde Lima: **97.6 %**
- Tráfico total interno de distribución: **32.50 TB/semana**
- Almacenamiento distribuido base: **259.97 TB**
- Almacenamiento con 30 % de margen: **337.96 TB**
- Ancho de banda agregado de planificación desde Lima: **≈ 160 Mbps**
- Banda promedio municipal → colegio para sincronizar en 12 h: **≈ 1.48 Mbps**
- Banda promedio municipal → colegio para sincronizar en 24 h: **≈ 0.74 Mbps**

## IA

- Solicitudes estimadas: **4,000/día**
- Línea base: **18.00 M tokens/día**
- Consumo textual optimizado estimado: **8.02 M tokens/día**
- Reducción estimada: **55.5 %**
- Objetivo requerido: **≥ 40 %**
- Resultado de estimación: **cumple bajo los supuestos definidos**

---

# 16. Datos que deben reemplazarse cuando exista información real

Antes de considerar esta estimación como dimensionamiento definitivo, el Gobierno deberá proporcionar o validar:

1. número real de colegios incluidos en la primera etapa;
2. localidades y jerarquía territorial realmente utilizadas como hubs;
3. tamaño promedio semanal real de materiales;
4. distribución de formatos: PDF, imágenes, audio y video;
5. ancho de banda típico y mínimo de las zonas objetivo;
6. horas disponibles para sincronización;
7. semanas de contenido que deben mantenerse offline;
8. cantidad real de profesores creadores;
9. volumen real de consultas de IA;
10. línea base real de tokens y costos multimedia.

Una vez disponibles estos datos se sustituyen los supuestos sin cambiar el método de cálculo.

---

# 17. Conclusión

El enfoque híbrido es viable para continuar hacia **D — Diseñar el servicio** porque reduce fuertemente el tráfico que debe salir directamente desde Lima y desplaza la distribución hacia caches territoriales más cercanos a los colegios.

La estimación también muestra que el cuello de botella principal no sería la cantidad de requests de API, sino:

- el volumen semanal de contenido;
- la velocidad de los enlaces remotos;
- el almacenamiento distribuido;
- la coordinación de versiones;
- y el costo de operar nodos intermedios.

Para IA, los presupuestos por complejidad, el rechazo de solicitudes no académicas y la selección del mecanismo de menor costo producen, bajo el escenario de referencia, una reducción aproximada de **55.5 %**, suficiente para justificar continuar con el diseño orientado a cumplir el objetivo mínimo del 40 %.
