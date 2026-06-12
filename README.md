# Kundu Project - Backend 🚀

[![SkillIcons](https://skillicons.dev/icons?i=androidstudio,spring,mysql,docker,sentry,kotlin,java,git)](https://skillicons.dev)

REST API robusta construida con **Spring Boot**, **Spring Security** y **JPA/Hibernate** para la gestión de eventos estudiantiles y control de asistencia gamificado. El diseño del sistema está inspirado en dinámicas de gamificación (estilo Duolingo) que incluyen recompensas, niveles de grupos, experiencia (XP) y estadísticas de competencia. 

El backend cuenta con arquitectura limpia, servicios bidireccionales en tiempo real y automatización de tareas por lotes, optimizado para soportar una carga estimada de **1000 a 1500 usuarios**.

> More details: [Deepwiki view](https://deepwiki.com/omargpax/kundu-api)
---

## 📋 Documentación Adicional (Docs)

- 📊 [Database Diagram](https://github.com/omargpx/kundu-api/blob/main/src/main/resources/.github/kundu_diagram-v2.png) - Modelo entidad-relación completo del sistema.
- 🎨 [Kundu Avatars](https://ibb.co/album/dWqKW6) - Álbum de recursos visuales del proyecto.

---

## 📐 Modelo de Datos y Arquitectura de Entidades

La persistencia del sistema está diseñada de forma modular bajo un esquema relacional optimizado, dividido en cuatro dominios clave que DeepWiki e Hibernate mapean semánticamente:

### 1. Dominio de Seguridad y Accesos (`tsg_`)
Controla el ciclo de vida de las sesiones y la protección de recursos mediante OAuth2/JWT:
* `tsg_users`: Almacena de forma aislada las credenciales críticas, estados de conexión (`is_connect`) y roles del sistema.
* `tsg_token`: Gestiona el estado transaccional de los tokens (expiración y revocación) garantizando un cierre de sesión seguro.

### 2. Dominio Core de Usuario y Gamificación (`tma_` / `tax_`)
Abstrae la información del usuario separando la identidad del motor de juego:
* `tma_people`: Extensión del usuario que concentra las mecánicas de gamificación, destacando el control de experiencia acumulada (`nu_experience`).
* `tax_follows`: Relación autovectorial N:M que permite flujos sociales de seguimiento (seguidores/seguidos) entre estudiantes.

### 3. Dominio de Estructura Organizacional y Grupos
Soporta la lógica de clanes y niveles competitivos inspirados en Duolingo:
* `tma_entities`: Tabla jerárquica autorreferenciada (`fk_father_id`) encargada de estructurar los niveles institucionales o facultades.
* `tma_groups`: Define los grupos competitivos incorporando atributos dinámicos de gamificación como fases (`nu_phase`), niveles de liga (`nu_tier`), puntajes de grupo (`nu_points`), lemas y recursos multimedia personalizados.
* `tmv_members`: Tabla intermedia que gestiona la afiliación histórica de los estudiantes a los respectivos grupos.

### 4. Dominio de Eventos, Sesiones y Asistencia (`tar_`)
Mapea el core de negocio del aplicativo móvil controlando la participación estudiantil:
* `tma_events`: Registra la planeación de eventos institucionales vinculados a las entidades organizadoras.
* `tax_sessions`: Representa las lecciones o sesiones específicas calendarizadas para cada grupo de competencia.
* `tar_assists`: Registro transaccional que audita en tiempo real la asistencia física/virtual (`attendance`) y valida si el estudiante completó exitosamente los cuestionarios evaluativos (`quiz_realized`) vinculados a la sesión.

---

## ⚡ Módulos y Arquitectura del Sistema

El núcleo de la aplicación está organizado en componentes especializados dentro del paquete `modules`, garantizando una alta cohesión y bajo acoplamiento:

### 🔄 1. Motor de Tiempo Real (`SocketSpaceService`)
Implementación de comunicación bidireccional basada en **Netty-SocketIO** para potenciar la interacción comunitaria dentro de la app:
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
