# Vista de despliegue (adicional — microservicios)

[ Indice](../README.md) | [ Vista conceptual](vista-conceptual.md)

Esta vista adicional muestra cómo se distribuyen físicamente los microservicios en un clúster Kubernetes, evidenciando el escalado independiente por servicio.

```mermaid
flowchart TB
    subgraph "Clúster Kubernetes"
        GW2[API Gateway<br/>3 réplicas]
        subgraph "Namespace: core-services"
            TS2[Servico-de-trafico<br/>autoscaling 3-10]
            TLS2[Servicio-de-GestionSemaforos<br/>3 réplicas]
            RS2[Servicio-de-rutas<br/>autoscaling 3-8]
            ALS2[Servicio-de-alertasurbanas<br/>3 réplicas]
        end
        subgraph "Espacio: servicios-de-datos"
            SIS2[Servicio-de-GestionSensores<br/>autoscaling 5-30]
            AP2[ servicio-predictivo<br/>2 réplicas]
        end
        subgraph "Espacio: Servicios-de-Soporte"
            AS2[Administracion-del-servicio<br/>2 réplicas]
            IS2[Servicio-de-integracion<br/>2 réplicas]
            NS2[Servicio-de-notificaciones<br/>3 réplicas]
        end
        BUS2(("Kafka Cluster"))
    end

    Internet[App Móvil / Centro de Control] --> GW2
    GW2 --> TS2 & TLS2 & RS2 & ALS2 & AS2
    SIS2 --> BUS2
    BUS2 --> TS2 & TLS2 & ALS2 & AP2 & NS2
    IS2 -.-> ExtAPI[[APIs Externas]]
```

Esta vista evidencia por qué microservicios responde bien al requisito RNF06: `Servicio-de-GestionSensores` escala hasta 30 réplicas por ser el punto de mayor carga, mientras que `Administracion-del-servicio` se mantiene estable en 2 réplicas — algo imposible de lograr con granularidad fina en un monolito.

---

[ Indice](../README.md) | [Siguiente](../docs/fase4-analisis-arquitectonico.md)
