# Fase 4 — Análisis arquitectónico

[Indice](../README.md) | [ Vista de despliegue](../diagrams/vista-despliegue.md)



## ¿Por qué son necesarias múltiples vistas?

Porque un sistema de microservicios agrega una dimensión adicional de complejidad frente a un monolito: ya no basta con mostrar "qué hace" el sistema (casos de uso) o "cómo se organiza el código" resulta  indispensable mostrar también cómo se distribuyen y comunican los procesos independientes** (vista de procesos orientada a eventos) y cómo se despliegan físicamente (vista de despliegue). Sin esta última, por ejemplo, no sería visible que cada servicio escala de forma distinta según su carga.

## ¿Qué representa cada vista?

Vista de casos de uso: funcionalidad visible para los actores, sin exponer que por debajo existen 8 servicios independientes.

Vista lógica: los microservicios, sus bases de datos propias, el API Gateway y el bus de eventos que los desacopla.
Vista de procesos: el comportamiento dinámico basado en coreografía de eventos, evidenciando el aislamiento de fallos entre servicios.
Vista conceptual: el panorama general, donde el "Sistema Central" ahora se entiende como un ecosistema de servicios y no como una caja única.
Vista de despliegue: cómo se distribuyen físicamente los servicios en el clúster y su política de escalado individual.

## ¿Cómo se complementan?

El requisito de negocio ("si un proceso falla, los demás deben continuar") se traduce en la vista lógica como comunicación por bus de eventos en lugar de llamadas síncronas directas; esa decisión se demuestra en la vista de procesos, donde se ve explícitamente el procesamiento paralelo e independiente entre `Servicio-de-GestionSemaforos` y `Servicio-de-alertasurbanas`; y se materializa físicamente en la vista de despliegue, donde cada servicio tiene su propia política de réplicas y puede caer sin afectar a los demás.

## ¿Qué vista resultó más compleja y por qué?

La vista de procesos fue la más compleja, porque modelar microservicios exige pasar de un pensamiento secuencial (llamada→respuesta) a un pensamiento basado en eventos y coreografía, donde no hay un único flujo lineal sino varios consumidores reaccionando de forma independiente al mismo evento. Diseñar correctamente qué eventos existen, quién los publica y quién los consume — sin crear dependencias ocultas entre servicios — esta decisión  impacta la resiliencia real del sistema.


# Conclusiones

- Adoptar microservicios responde directamente a la necesidad identificada: procesar múltiples flujos de datos en tiempo real de forma concurrente, garantizando que el fallo de un proceso (por ejemplo, alertas) no detenga a los demás (semáforos, tráfico, rutas).
- El uso de un bus de eventos como columna vertebral de la comunicación es la pieza clave que habilita el aislamiento de fallos exigido por el sistema.
- El patrón database-per-service permite usar la tecnología de persistencia más adecuada para cada dominio  como series de tiempo para tráfico
- La vista de despliegue demuestra que el escalado independiente por servicio es indispensable ante la naturaleza desigual de la carga (varios sensores vs. pocos administradores).
- El principal costo de este estilo es la complejidad operativa, que debe gestionarse con *tracing* distribuido y *circuit breakers*, como se especifica en los requisitos no funcionales.


[ Indice](../README.md)
