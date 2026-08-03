# Fase 1 — Especificación de requisitos del sistema

[Indice](../README.md)



#  1.1 Módulos funcionales → Microservicios

Cada módulo funcional identificado se implementa como un microservicio independiente, dueño exclusivo de su propia base de datos (*database-per-service*):

| Microservicio | Responsabilidad | Persistencia propia |
|---|---|---|
|Servico-de-trafico | Monitoreo y análisis del tráfico en tiempo real | Bases de datos de series temporales (InfluxDB) |
| Servicio-de-GestionSemaforos | Gestión e informacion adaptativa de semáforos | Base relacional (PostgreSQL) |
| Servicio-de-rutas| Cálculo de rutas óptimas para los usuarios  | Base de grafos (Neo4j) + cache (Redis) |
| Servicio-de-GestionSensores | Recepción y validación de datos de sensores urbanos | Buffer / stream (Kafka topics) |
| Servicio-de-alertasurbanas | Detección de incidentes  y generación de alertas del sistema | Base documental (MongoDB) |
| Administracion-del-servicio | Administración de usuarios, roles y configuración | Base relacional (PostgreSQL) |
| Servicio-de-integracion | Conectar con  servicios externos (mapas, clima) | Sin persistencia propia (cache corta) |
| Servicio-de-notificaciones | Envío de notificaciones directas a la app móvil | Cola de mensajes |



# 1.2 Especificación de requisitos funcionales

Servico-de-trafico
- RF01. El servicio deberá monitorear el tráfico en tiempo real.
- RF02. El servicio deberá detectar congestiones y publicar el evento `congestion.detected`.
- RF03. El servicio deberá clasificar el nivel de tráfico por zona (fluido, moderado, congestionado).

Servicio-de-GestionSemaforos
- RF04. El servicio deberá ajustar automáticamente los tiempos de los semáforos según eventos de tráfico recibidos.
- RF05. El servicio deberá permitir al centro de control operar manualmente un semáforo ante una emergencia.
- RF06. El servicio deberá registrar el histórico de cambios de estado de cada semáforo.

Servicio-de-rutas
- RF07. El servicio deberá calcular rutas óptimas consumiendo eventos de tráfico y datos de integration-service.
- RF08. El servicio deberá actualizar las rutas en tiempo real ante cambios en las condiciones del tráfico.
- RF09. El servicio deberá exponer rutas alternativas ante congestión.

Servicio-de-GestionSensores
- RF10. El servicio deberá recolectar datos de sensores urbanos (cámaras, lazos, IoT).
- RF11. El servicio deberá validar la integridad de los datos recibidos antes de publicarlos como evento.
- RF12. El servicio deberá geolocalizar cada sensor emisor dentro del mapa de la ciudad.

Servicio-de-alertasurbanas
- RF13. El servicio deberá generar alertas de congestión al consumir el evento `congestion.detected`.
- RF14. El servicio deberá generar alertas de incidentes (accidentes, obstrucciones viales).
- RF15. El servicio deberá publicar el evento `alert.created` para que notification-service lo procese.

Administracion-del-servicio
- RF16. El servicio deberá gestionar roles y permisos de usuarios del centro de control.
- RF17. El servicio deberá permitir la configuración de parámetros operativos (umbrales de congestión, tiempos base de semáforos).
- RF18. El servicio deberá exponer reportes operativos consumiendo datos agregados de los demás servicios.

Servicio-de-integracion
- RF19. El servicio deberá exponer una API unificada hacia proveedores externos de mapas y clima.
- RF20. El servicio deberá aplicar *circuit breaker* ante fallos de los proveedores externos.



# 1.3 Requisitos no funcionales

- RNF01. Cada microservicio deberá tener una disponibilidad individual del 99,9 %, sin comprometer la disponibilidad global del sistema ante el fallo de otro servicio.
- RNF02. El tiempo de respuesta de las consultas síncronas (Gateway) no deberá superar los dos segundos.
- RNF03. Toda la comunicación entre servicios y con el exterior deberá realizarse mediante protocolos seguros (HTTPS/TLS, mTLS entre servicios internos).
- RNF04. Cada microservicio deberá poder escalar horizontalmente de forma independiente según su propia demanda.
- RNF05. El sistema deberá garantizar tolerancia a fallos mediante *circuit breakers*, reintentos y colas con *dead-letter queue*.
- RNF06. El sistema deberá soportar el procesamiento concurrente de eventos provenientes de miles de sensores mediante un bus de mensajería (Kafka/RabbitMQ).
- RNF07. El sistema deberá mantener trazabilidad distribuida (logging y *tracing* centralizados, ej. OpenTelemetry) para auditar transacciones que atraviesan varios servicios.



[Indice](../README.md) | [Siguiente](fase2-analisis.md)
