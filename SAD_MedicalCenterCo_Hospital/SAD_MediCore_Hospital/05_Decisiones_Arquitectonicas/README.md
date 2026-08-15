## 5. Decisiones arquitectónicas

| Decisión | Alternativas consideradas | Justificación |
|---|---|---|
| Microservicios por dominio clínico | Monolito modular, microservicios | Permite aislar la historia clínica (alta confidencialidad) de módulos de menor sensibilidad como citas, y escalar cada uno de forma independiente (RNF-06). |
| API Gateway con autenticación centralizada | Autenticación distribuida, Gateway centralizado | Centraliza el control de acceso por rol (RBAC) antes de que la solicitud llegue a cualquier microservicio (RNF-02, RNF-03). |
| Auditoría por eventos asíncronos | Auditoría síncrona embebida en cada servicio, auditoría por eventos | Desacopla el registro de auditoría de la operación clínica, evitando degradar el rendimiento de la consulta (RNF-05) sin sacrificar la trazabilidad (RNF-04); si el servicio de auditoría falla, la atención al paciente no se bloquea. |
| Notificación de resultados de laboratorio por eventos | Sondeo (polling) síncrono, eventos | El tiempo de procesamiento de un examen es variable e independiente del flujo clínico; un evento notifica al médico tan pronto el resultado está listo, sin acoplar temporalmente laboratorio y consulta médica. |
| Cifrado en tránsito y en reposo para datos clínicos | Cifrado parcial, cifrado extremo a extremo | Es un requisito no negociable dado el carácter sensible de los datos de salud (RNF-02). |
| Despliegue en contenedores orquestados | Servidores tradicionales, contenedores | Facilita el escalado ante crecimiento de sedes/pacientes y la recuperación ante fallos, relevante para RNF-01 (99.9% de disponibilidad). |
| Bitácora de auditoría inmutable (append-only) | Registro editable, registro append-only | Un registro de auditoría debe ser inalterable para tener valor probatorio y de cumplimiento normativo (RNF-04). |

---
