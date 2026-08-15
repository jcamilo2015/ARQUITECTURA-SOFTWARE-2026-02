## 1. Introducción
El presente documento corresponde a la Especificación Arquitectónica de Software (Software Architecture Document — SAD) de MedicalCenterCo, un sistema hospitalario digital diseñado para apoyar la gestión de pacientes, historias clínicas electrónicas, agendamiento de citas y resultados de laboratorio, teniendo como prioridad la protección y seguridad de la información clínica.

La elaboración del documento se realizó tomando como referencia los lineamientos del Rational Unified Process (RUP), integrando de manera progresiva los principales elementos obtenidos durante el desarrollo del proyecto, como la visión del sistema, los requisitos, el modelo conceptual del dominio y las decisiones relacionadas con la arquitectura. Para organizar y presentar la arquitectura se utilizó el modelo 4+1 de Kruchten, complementándolo con una vista de contexto y una vista conceptual que permiten comprender mejor el funcionamiento general del sistema y la relación entre sus principales elementos.

Debido a que MedicalCenterCo maneja información sensible relacionada con la atención de los pacientes, las decisiones arquitectónicas consideran especialmente aspectos como la seguridad, confidencialidad, trazabilidad y disponibilidad. Estos atributos son fundamentales para garantizar que la información clínica solo pueda ser consultada o modificada por usuarios autorizados, que las acciones realizadas dentro del sistema puedan ser registradas y que los servicios estén disponibles cuando sean necesarios.

En este sentido, la arquitectura propuesta busca no solo definir cómo estarán organizados los componentes de MedicalCenterCo, sino también establecer una base que permita que el sistema sea seguro, confiable y pueda mantenerse y evolucionar de acuerdo con las necesidades de una institución hospitalaria.


---
