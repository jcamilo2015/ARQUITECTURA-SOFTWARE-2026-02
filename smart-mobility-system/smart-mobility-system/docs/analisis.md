# Fase 2 — Análisis del sistema

[ Volver al índice](../README.md) | [Requisitos](fase1-requisitos.md)



# 2.1 Actores

| Actor | Descripción |
|---|---|
| Usuario (conductor o peatón)| Consulta rutas, recibe alertas, usa la aplicación móvil |
| Sistema de sensores| Emite datos de tráfico en tiempo real hacia sensor-ingestion-service |
| Centro de control | Supervisa y opera el sistema mediante admin-service y traffic-light-service |
| Aplicación móvil| Cliente que consume el sistema exclusivamente a través del API Gateway |
| Servicios externos (mapas, clima)| Consumidos por integration-service |



# 2.2 Funcionalidades principales

- Monitoreo continuo y desacoplado del estado del tráfico por zona.
- Control adaptativo de semáforos reactivo a eventos de tráfico.
- Cálculo dinámico de rutas óptimas combinando varios servicios.
- Ingesta masiva y validación de datos de sensores urbanos.
- Emisión de alertas tempranas mediante arquitectura orientada a eventos.
- Integración resiliente con mapas y datos climáticos externos.


# 2.3 Problemas que resuelve el sistema

- Falta de información en tiempo real sobre el estado del tráfico.
- Semáforos con tiempos fijos que no responden a la demanda real.
- Ausencia de rutas alternativas ante congestión o incidentes.
- Efecto cascada de fallos: en un sistema monolítico, un error en el cálculo de rutas podría tumbar todo el sistema; en microservicios, el resto de procesos continúa operando.
- Dificultad para escalar de forma selectiva los componentes con mayor carga (sensores) sin sobredimensionar los demás.



## 2.4 Restricciones

- El sistema debera operar en tiempo real o cercano a tiempo real (latencia menor a 2 segundos en las rutas síncronas).
- Debe garantizar alta disponibilidad* por servicio, con degradación controlada (ej. si el Servicio-de-integracion falla, route-service puede seguir operando con datos de tráfico y una ruta menos optimizada).
- Debe ser tolerante a fallos parciales, aislando el impacto de cada microservicio.
- Debe escalar horizontalmente por servicio, no como una unidad monolítica.
- Debe garantizar la seguridad en las comunicaciones internas (servicio-a-servicio) y externas (API Gateway).
- Debera manejar consistencia eventual entre servicios, dado que no existe una única base de datos transaccional compartida.



[Indice](../README.md) | [Siguiente](../diagrams/casos-de-uso.md)
