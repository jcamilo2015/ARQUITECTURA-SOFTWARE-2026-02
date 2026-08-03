# Vista de procesos (diagrama de secuencia)

[ Indice](../README.md) | [Vista lógica](vista-logica.md)

Escenario: Detección de congestión y generación de alerta, mostrando el desacoplamiento entre servicios mediante eventos (patrón *choreography*):

```mermaid
sequenceDiagram
    participant SEN as Sensores IoT
    participant SGS as Servicio-de-GestionSensores
    participant BUS as Event Bus
    participant ST as Servico-de-trafico
    participant SGS as Servicio-de-GestionSemaforos
    participant SAU as Servicio-de-alertasurbanas
    participant SN as Servicio-de-notificaciones
    participant APP as App Móvil

    SEN->>SIS: Envía lecturas de tráfico
    SIS->>SIS: Valida y normaliza datos
    SIS->>BUS: publica "sensor.data.received"

    BUS->>TS: consume evento
    TS->>TS: Analiza nivel de congestión

    alt Congestión detectada
        ST->>BUS: publica "congestion.detected"
        par Procesamiento paralelo e independiente
            BUS->>SGS: consume evento (ajusta semáforos)
        and
            BUS->>SAU: consume evento (genera alerta)
        end
        SAU->>BUS: publica "alert.created"
        BUS->>SN: consume evento
        NS->>APP: Push notification de alerta
    else Sin congestión
        ST->>ST: Actualiza estado normal
    end

    note over SGS,SAU: Si Servicio-de-GestionSemaforos falla,<br/>Servicio-de-alertasurbanas continúa operando<br/>de forma independiente (y viceversa)
```

# Descripción del flujo

1. Los sensores envían datos continuamente a `Servicio-de-GestionSensores`, que valida y publica un evento.
2. `Servico-de-trafico` consume el evento de forma asíncrona y analiza la congestión.
3. Si detecta congestión, publica un nuevo evento que es consumido en paralelo e independiente por `Servicio-de-GestionSemaforos` y `Servicio-de-alertasurbanas`.
4. `Servicio-de-alertasurbanas` genera la alerta y publica un evento final que dispara la notificación al usuario.
5. Punto clave del estilo elegido: el fallo de cualquiera de los servicios consumidores (semáforos o alertas) no bloquea al otro, ni detiene la ingesta de nuevos datos  cada uno procesa su evento de forma independiente y, si falla, el evento permanece disponible en el bus para reintento.



[Indice](../README.md) | [Siguiente](vista-conceptual.md)
