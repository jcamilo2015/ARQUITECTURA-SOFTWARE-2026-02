## 6. Vistas arquitectónicas (Modelo 4+1)

#  6.1 Vista de contexto

```mermaid
flowchart LR
    Paciente((Paciente)) --> MediCore[Sistema MediCore]
    Medico((Personal médico)) --> MediCore
    Lab((Personal de laboratorio)) --> MediCore
    Admin((Personal administrativo)) --> MediCore
    MedicalCenterCo --> LabExterno[Laboratorio Externo HL7/FHIR]
    MedicalCenterCo  --> Notificaciones[Servicio de Notificaciones SMS/Correo]
    MedicalCenterCo --> Facturacion[Sistema de Facturación Externo]
```

- Límites del sistema: MediCore gestiona pacientes, historia clínica, citas y laboratorio; no incluye facturación ni hospitalización/quirófano.
- Actores externos: Paciente, personal médico, personal de laboratorio, personal administrativo.
- Sistemas externos: laboratorio externo (vía HL7/FHIR), servicio de notificaciones, sistema de facturación.

#  6.2 Vista conceptual

```mermaid
flowchart TB
    subgraph Dominio de Pacientes
        P[Gestión de Pacientes]
    end
    subgraph Dominio Clínico
        HC[Historia Clínica]
    end
    subgraph Dominio Administrativo
        C[Agendamiento de Citas]
    end
    subgraph Dominio de Laboratorio
        L[Gestión de Laboratorio]
    end
    subgraph Dominio Transversal
        Aud[Seguridad y Auditoría]
    end

    P --> HC
    P --> C
    HC --> L
    HC -. eventos de acceso .-> Aud
    C -. eventos de acceso .-> Aud
    L -. eventos de acceso .-> Aud
```

El dominio de Seguridad y Auditoría  no participa en el flujo funcional; observa e intercepta eventos generados por los demás dominios, reflejando su naturaleza transversal

# 6.3 Vista de casos de uso

```mermaid
flowchart LR
    Paciente((Paciente))
    Medico((Médico))
    Lab((Laboratorio))

    Paciente --> UC1[Agendar cita]
    Medico --> UC2[Consultar historia clínica]
    Medico --> UC3[Registrar nota de consulta]
    Medico --> UC4[Solicitar examen]
    Lab --> UC5[Registrar resultado de examen]
    Medico --> UC6[Ver resultado de examen]
    Admin((Administrativo)) --> UC7[Gestionar agenda médica]
```

Casos de uso arquitectónicamente significativos: 
- Consultar historia clínica : exige control de acceso estricto por rol y registro de auditoría en cada consulta (RNF-02, RNF-03, RNF-04)
- Registrar y ver resultado de examen : exige comunicación asíncrona entre laboratorio y consulta médica
- Registrar nota de consulta : exige alta disponibilidad, dado que ocurre durante la atención en tiempo real (RNF-01)

### 6.4 Vista lógica

```mermaid
flowchart TB
    Gateway[API Gateway + Control de acceso RBAC]

    subgraph MS_Pacientes[Microservicio Pacientes]
        Pa_API[API] --> Pa_Neg[Lógica de negocio] --> Pa_Dat[(BD Pacientes)]
    end
    subgraph MS_HC[Microservicio Historia Clínica]
        HC_API[API] --> HC_Neg[Lógica de negocio] --> HC_Dat[(BD Historia Clínica)]
    end
    subgraph MS_Citas[Microservicio Citas]
        C_API[API] --> C_Neg[Lógica de negocio] --> C_Dat[(BD Citas)]
    end
    subgraph MS_Lab[Microservicio Laboratorio]
        L_API[API] --> L_Neg[Lógica de negocio] --> L_Dat[(BD Laboratorio)]
    end
    subgraph MS_Auditoria[Microservicio Auditoría]
        Aud_Neg[Consumidor de eventos] --> Aud_Dat[(BD Auditoría append-only)]
    end

    Gateway --> Pa_API
    Gateway --> HC_API
    Gateway --> C_API
    Gateway --> L_API

    HC_Neg -. evento de acceso .-> Aud_Neg
    C_Neg -. evento de acceso .-> Aud_Neg
    L_Neg -. evento de acceso .-> Aud_Neg
    L_Neg -. evento resultado listo .-> HC_Neg
```

Cada microservicio conserva la estructura en capas (API, lógica de negocio, acceso a datos). El microservicio de Auditoría  no expone API de escritura a los demás servicios: solo consume eventos, reforzando la inmutabilidad de sus registros.

# 6.5 Vista de implementación (diagrama de componentes UML)

```mermaid
flowchart LR
    subgraph Frontend
        Web[Portal Web Clínico]
    end

    subgraph Backend
        GW[[API Gateway]]
        SP[Componente: Pacientes]
        SHC[Componente: Historia Clínica]
        SC[Componente: Citas]
        SL[Componente: Laboratorio]
        SAud[Componente: Auditoría]
        MB{{Bus de Eventos}}
    end

    Web --> GW
    GW --> SP
    GW --> SHC
    GW --> SC
    GW --> SL
    SHC --> MB
    SC --> MB
    SL --> MB
    MB --> SAud
```

Organización de componentes de software : el portal web clínico consume exclusivamente el API Gateway; los componentes de Historia Clínica, Citas y Laboratorio publican eventos al bus; el componente de Auditoría es el único suscriptor de esos eventos y no expone API pública de escritura.

# 6.6 Vista física (despliegue)

```mermaid
flowchart TB
    subgraph Nodo_Balanceador[Nodo: Balanceador de carga]
        LB[Load Balancer]
    end
    subgraph Nodo_Gateway[Nodo: API Gateway]
        GWn[API Gateway + RBAC]
    end
    subgraph Cluster_Contenedores[Clúster de contenedores]
        direction LR
        N1[Contenedor Pacientes]
        N2[Contenedor Historia Clínica]
        N3[Contenedor Citas]
        N4[Contenedor Laboratorio]
        N5[Contenedor Auditoría]
    end
    subgraph Nodo_Datos[Nodo: Bases de datos]
        DB1[(BD Pacientes)]
        DB2[(BD Historia Clínica cifrada)]
        DB3[(BD Citas)]
        DB4[(BD Laboratorio)]
        DB5[(BD Auditoría append-only)]
    end
    Externo[Laboratorio Externo HL7/FHIR]

    LB --> GWn
    GWn --> N1 & N2 & N3 & N4
    N1 --> DB1
    N2 --> DB2
    N3 --> DB3
    N4 --> DB4
    N4 --> Externo
    N5 --> DB5
```

- Nodos : balanceador de carga, nodo del API Gateway con control de acceso, clúster de contenedores para los microservicios, nodo de bases de datos
-  Componentes desplegados : cada microservicio en un contenedor independiente con su propia base de datos (patrón database per service); la base de datos de Historia Clínica se despliega con cifrado en reposo reforzado dado su nivel de sensibilidad
- Infraestructura tecnológica : orquestador de contenedores, balanceador de carga, bus de eventos, motores de base de datos relacional para los servicios transaccionales, almacenamiento append-only para auditoría
- Estrategia de despliegue seleccionada: réplicas activas del microservicio de Historia Clínica en más de una zona de disponibilidad, dado que es el componente más crítico frente al requisito RNF-01 (99.9% de disponibilidad)

---
