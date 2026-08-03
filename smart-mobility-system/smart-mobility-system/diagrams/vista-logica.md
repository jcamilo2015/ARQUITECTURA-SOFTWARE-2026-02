# Vista lógica — Microservicios y comunicación

[Indice](../README.md) | [ Vista de casos de uso](casos-de-uso.md)

```mermaid
flowchart TB
    APP[App Móvil] --> GW[API Gateway]
    WEBCC[Consola Centro de Control] --> GW

    GW --> SR[Servicio-de-rutas]
    GW --> AS[Administracion-del-servicio]
    GW --> SGS[Servicio-de-GestionSemaforos]
    GW --> SAU[Servicio-de-alertasurbanas]

    SEN[Sensores IoT] --> SGS[Servicio-de-GestionSensores]

    subgraph "Bus de eventos (Kafka / RabbitMQ)"
        BUS(("Event Bus"))
    end

    SIS -- "sensor.data.received" --> BUS
    BUS --> ST[Servico-de-trafico]
    TS -- "congestion.detected" --> BUS
    BUS --> SGS
    BUS --> SAU
    BUS --> SR

    SAU -- "alert.created" --> BUS
    BUS --> SN[Servicio-de-notificaciones]
    NS --> APP

    RS --> SI[Servicio-de-integracion]
    TS --> sp[servicio-predictivo]
    AP --> SR

    IS -.API externa.-> EXT1[[Proveedor de Mapas]]
    IS -.API externa.-> EXT2[[Proveedor de Clima]]

    TS --> DB1[(InfluxDB)]
    TLS --> DB2[(PostgreSQL)]
    RS --> DB3[(Neo4j / Redis)]
    ALS --> DB4[(MongoDB)]
    AS --> DB5[(PostgreSQL)]
```

## Principios aplicados

- API Gateway como único punto de entrada para clientes (app móvil, consola de control).
- Comunicación síncrona (REST/gRPC) para consultas puntuales (Gateway → servicios).
- Comunicación asíncrona por eventos (bus de mensajería) para todo el flujo de datos en tiempo real: `sensor.data.received`, `congestion.detected`, `alert.created`. Esto es lo que permite que si `Servicio-de-alertasurbanas` cae, `Servico-de-trafico` y `Servicio-de-GestionSemaforos` sigan funcionando — el evento simplemente queda en la cola hasta que el servicio se recupere.
- Database-per-service: ningún servicio accede directamente a la base de datos de otro.


[ Indice](../README.md) | [Siguiente](vista-procesos.md)
