# Sistema Inteligente de Movilidad Urbana (Smart Mobility System)
## Taller de Modelado y Representación Arquitectónica del Sistema
# Estilo arquitectónico aplicado: Microservicios

**Asignatura:** Arquitectura del Software



# Índice del repositorio

- [`docs/requisitos.md`](docs/requisitos.md) — Especificación de requisitos del sistema
- [`docs/analisis.md`](docs/analisis.md) — Análisis del sistema 
- [`diagrams/casos-de-uso.md`](diagrams/casos-de-uso.md) — Vista de casos de uso
- [`diagrams/vista-logica.md`](diagrams/vista-logica.md) — Vista lógica (microservicios y comunicación)
- [`diagrams/vista-procesos.md`](diagrams/vista-procesos.md) — Vista de procesos (diagrama de secuencia)
- [`diagrams/vista-conceptual.md`](diagrams/vista-conceptual.md) — Vista conceptual (diagrama macro)
- [`diagrams/vista-despliegue.md`](diagrams/vista-despliegue.md) — Vista de despliegue (Kubernetes)
- [`docs/analisis-arquitectonico.md`](docs/analisis-arquitectonico.md) — Análisis arquitectónico y conclusiones


# 1.Introducción



Este repositorio presenta el diseño arquitectónico del Sistema Inteligente de Movilidad Urbana (Smart Mobility System), desarrollado bajo un estilo arquitectónico de microservicios . Esta decisión responde a una característica central del sistema: debe procesar y ejecutar de forma simultánea múltiples flujos de datos en tiempo real** (tráfico, semáforos, rutas, sensores, alertas), y ante el fallo de uno de estos procesos, los demás deben continuar operando sin verse afectados. Un estilo monolítico no garantiza este comportamiento, mientras que microservicios sí, al aislar cada capacidad de negocio en un proceso independiente, con su propio ciclo de vida, escalado y tolerancia a fallos.

Para representar la arquitectura se aplican los conceptos de la Unidad 2, construyendo el diseño desde múltiples vistas complementarias (casos de uso, lógica, de procesos, conceptual y de despliegue), todas ellas coherentes con el estilo de microservicios adoptado.



## 2.Descripción del sistema

El **Smart Mobility System** es una plataforma distribuida compuesta por servicios autónomos que se comunican entre sí mediante APIs y eventos. Los sensores urbanos (cámaras, lazos inductivos, IoT) alimentan continuamente al sistema; un conjunto de microservicios procesa esa información de forma independiente y concurrente; el centro de control opera semáforos e incidentes en tiempo real; y los ciudadanos consultan rutas y reciben alertas a través de una aplicación móvil que se comunica con el backend mediante un API Gateway.


Objetivos del sistema:

- Monitorear el tráfico en tiempo real.
- Gestionar semáforos inteligentes de forma adaptativa.
- Permitir a los usuarios consultar rutas óptimas.
- Integrarse con aplicaciones móviles.
- Procesar datos de sensores urbanos.
- Generar alertas de congestión o incidentes.
- Garantizar que el fallo de un proceso no detenga la operación del resto del sistema (criterio que motiva el estilo de microservicios).


#  3.Justificación del estilo arquitectónico

El sistema requiere el procesamiento simultáneo de múltiples procesos independientes  (ingesta de sensores, análisis de tráfico, cálculo de rutas, control de semáforos, generación de alertas), cada uno con distintos patrones de carga y distintos requisitos de escalado. Un microservicio por capacidad de negocio permite:

- Aislamiento de fallos : si el servicio de rutas falla, el monitoreo de tráfico y el control de semáforos siguen operando.
- Escalado independiente : el servicio de ingesta de sensores necesita muchas más réplicas que el de administración de usuarios; en microservicios se escalan por separado.
- Despliegue independiente: se puede actualizar el motor de alertas sin reiniciar todo el sistema.
- Tecnología adecuada por servicio : bases de datos time-series para tráfico, bases de grafos para rutas, colas de mensajes para eventos — algo difícil de lograr en un monolito.



## Herramientas utilizadas

- Mermaid.js — Diagramación de casos de uso, componentes, secuencia, conceptual y despliegue (renderizado nativo en GitHub Markdown).
- Markdown  — Documentación estructurada del repositorio.
