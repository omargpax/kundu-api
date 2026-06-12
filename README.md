# Kundu Project - Backend 🚀

[![SkillIcons](https://skillicons.dev/icons?i=androidstudio,spring,mysql,docker,sentry,kotlin,java,git)](https://skillicons.dev)

REST API robusta construida con **Spring Boot**, **Spring Security** y **JPA/Hibernate** para la gestión de eventos estudiantiles y control de asistencia gamificado. El diseño del sistema está inspirado en dinámicas de gamificación (estilo Duolingo) que incluyen recompensas, niveles de grupos, experiencia (XP) y estadísticas de competencia. 

El backend cuenta con arquitectura limpia, servicios bidireccionales en tiempo real y automatización de tareas por lotes, optimizado para soportar una carga estimada de **1000 a 1500 usuarios**.

---

## 📋 Documentación Adicional (Docs)

- 📊 [Database Diagram](https://github.com/omargpx/kundu-api/blob/main/src/main/resources/.github/kundu_diagram-v2.png) - Modelo entidad-relación del sistema.
- 🎨 [Kundu Avatars](https://ibb.co/album/dWqKW6) - Álbum de recursos visuales del proyecto.

---

## ⚡ Módulos y Arquitectura del Sistema

El núcleo de la aplicación está organizado en componentes especializados dentro del paquete `modules`, garantizando una alta cohesión y bajo acoplamiento:

### 🔄 1. Motor de Tiempo Real (`SocketSpaceService`)
Implementación de comunicación bidireccional basada en **Socket.IO** para potenciar la interacción comunitaria dentro de la app:
* **Gestión de Salas (Spaces):** Permite a los usuarios unirse o salir de canales de comunicación específicos de su grupo.
* **Control de Presencia Activa:** Mapeo dinámico en memoria (`room_listeners`) para registrar y transmitir en tiempo real quién está conectado.
* **Mensajería e Interacción:** Difusión selectiva de mensajes (`sms_channel`/`sms_space`) y notificaciones globales de actividad en la sala (`space_door`).

### ⏱️ 2. Ciclo de Vida Automático (`TaskScheduler`)
Automatización de la lógica de negocio y progresión de los estudiantes mediante tareas programadas de Spring:
* **Rutina Semanal (Cron):** Ejecución automatizada todos los **sábados a la 1:00 AM** (`0 0 1 ? * SAT`).
* **Flujo de Progresión:** Analiza el estado de las sesiones de cada grupo de manera transaccional (`@Transactional`). Cierra de forma segura la sesión activa y desbloquea el acceso a la siguiente de manera secuencial.
* **Suscripción Dinámica de Contenido:** Al finalizar la última sesión disponible, limpia el histórico del ciclo y suscribe automáticamente al grupo a la nueva fase utilizando el servicio centralizado de la librería (`ThemesContentService`).

### 🛡️ 3. Transaccionalidad y Manejo Global de Excepciones
* **Monitoreo con Sentry:** Rastreo integrado de errores en producción directamente en los flujos críticos.
* **Arquitectura de Excepciones:** Centralizada en controladores de anomalías (`AuthExceptionHandler`, `KunduException`) para responder con formatos estandarizados (`ApiError`).

---

## 🛠️ Instrucciones de Ejecución

Sigue estos pasos para clonar y levantar la API en tu entorno local:

### 1. Clonar el repositorio
```bash
git clone [https://github.com/citseOfficial/Kundu-api.git](https://github.com/citseOfficial/Kundu-api.git)
cd Kundu-api
```
### 2. Configuración de Base de Datos
> 💡 **Nota importante:** Antes de levantar el proyecto, asegúrate de configurar las credenciales de tu base de datos local (MySQL o compatible) en el archivo:
> `src/main/resources/application.yml`

Para la creación del esquema y las tablas, puedes optar por dos caminos:
* **Automático:** El sistema generará las tablas automáticamente al iniciar la aplicación.
* **Manual:** Puedes ejecutar los scripts SQL contenidos en la carpeta `src/main/resources/sql/`.

### 3. Levantar aplicación
Ejecuta el siguiente comando en tu terminal (en Windows puedes usar PowerShell):
```
.\gradlew bootRun
```

## 📝 Nomenclatura de Commits

Para mantener el historial del repositorio limpio y estandarizado, seguimos las siguientes convenciones:

| Prefijo | Descripción |
| :--- | :--- |
| `feat:` | Añadir nuevas funcionalidades al sistema. |
| `fix:` | Corregir errores o bugs. |
| `sec:` | Actualizaciones relacionadas con la seguridad del sistema. |
| `ref:` | Refactorización de código o mejoras de algoritmos sin cambiar funcionalidad. |
| `test:` | Añadir o modificar características bajo entorno de pruebas (tests). |
| `chore:` | Cambios en dependencias, herramientas de construcción o tareas repetitivas. |
