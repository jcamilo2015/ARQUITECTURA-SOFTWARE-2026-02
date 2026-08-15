## 3. Especificación de requisitos

### 3.1 Requisitos funcionales por módulo

Gestión de pacientes
- RF-01: El sistema debe permitir el registro de un nuevo paciente con sus datos demográficos
- RF-02: El sistema debe permitir la búsqueda de pacientes por identificación o nombre
- RF-03: El sistema debe permitir la actualización de los datos de contacto del paciente

Historia clínica electrónica
- RF-04: El sistema debe permitir al personal médico registrar notas de consulta asociadas a un paciente
- RF-05: El sistema debe permitir consultar el historial clínico completo de un paciente
- RF-06: El sistema debe registrar diagnósticos codificados (p. ej. CIE-10) asociados a cada consulta

Agendamiento de citas
- RF-07: El sistema debe permitir agendar una cita médica según disponibilidad del profesional
- RF-08: El sistema debe permitir reprogramar o cancelar una cita existente
- RF-09: El sistema debe notificar al paciente sobre la confirmación o cambios de su cita

Gestión de laboratorio
- RF-10: El sistema debe permitir al médico solicitar exámenes de laboratorio para un paciente
- RF-11: El sistema debe permitir al personal de laboratorio registrar los resultados de un examen
- RF-12: El sistema debe notificar al médico tratante cuando un resultado esté disponible

Seguridad y auditoría
- RF-13: El sistema debe autenticar a todos los usuarios antes de permitir el acceso
- RF-14: El sistema debe restringir el acceso a la historia clínica según el rol del usuario
- RF-15: El sistema debe registrar en una bitácora de auditoría cada acceso a datos clínicos sensibles (usuario, fecha, acción).

### 3.2 Requisitos no funcionales (atributos de calidad)

| ID | Atributo | Descripción |
|---|---|---|
| RNF-01 | Disponibilidad | El sistema debe estar disponible el 99.9% del tiempo, dado su uso en contexto asistencial. |
| RNF-02 | Seguridad | Los datos clínicos deben cifrarse en tránsito y en reposo; el acceso debe basarse en control por roles (RBAC). |
| RNF-03 | Confidencialidad | Solo el personal autorizado y vinculado a la atención del paciente puede consultar su historia clínica. |
| RNF-04 | Trazabilidad | Todo acceso o modificación a datos clínicos debe quedar registrado de forma inmutable en la bitácora de auditoría. |
| RNF-05 | Rendimiento | La consulta de la historia clínica de un paciente debe responder en menos de 2 segundos. |
| RNF-06 | Escalabilidad | El sistema debe soportar el crecimiento en número de pacientes y sedes sin rediseño arquitectónico mayor. |
| RNF-07 | Interoperabilidad | El sistema debe poder integrarse con sistemas externos de laboratorio mediante estándares como HL7/FHIR. |

### 3.3 Restricciones técnicas y organizacionales

- Microservicios independientes: Cada módulo (citas, historias clínicas, laboratorios) debe ser autónomo
- Bases de datos aisladas: Cada microservicio gestiona su propia persistencia
- Despliegue en contenedores: Infraestructura basada en contenedores y orquestada
- Estándar HL7/FHIR: Obligatorio para integrar laboratorios externos de forma interoperable
- Seguridad perimetral: Autenticación centralizada mediante API Gateway para proteger datos médicos
- Continuidad del negocio: El despliegue y las actualizaciones no deben interrumpir la operación médica 24/7 del hospital
- Cumplimiento legal : Ajuste obligatorio a las leyes vigentes de protección de datos de salud y auditoría de historias clínicas



---
