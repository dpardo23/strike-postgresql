# Strike · PostgreSQL + JDBC

Aplicación de escritorio en **Java + JavaFX** para gestionar estadísticas de fútbol: países, ligas,
equipos, jugadores y partidos. Proyecto del **Taller de Base de Datos** de la Universidad Mayor de
San Simón (UMSS), semestre II/2025.

Strike se implementó cuatro veces: la misma aplicación sobre cuatro tecnologías de datos distintas.

| Repositorio | Tecnología de datos | Enfoque |
|---|---|---|
| **strike-postgresql** (este) | PostgreSQL + JDBC | la lógica de negocio vive en funciones y procedimientos almacenados |
| [srtike-hibernate](https://github.com/dpardo23/srtike-hibernate) | PostgreSQL + Hibernate/JPA | mapeo objeto-relacional sobre el mismo esquema |
| [strike-neo4j](https://github.com/dpardo23/strike-neo4j) | Neo4j | el dominio como grafo, consultado en Cypher |
| [strike-redis](https://github.com/dpardo23/strike-redis) | Redis + PostgreSQL | sesiones en caché que se refrescan con `LISTEN`/`NOTIFY` |

![Panel de administración de Strike](img.png)

## La lógica vive en la base de datos

La aplicación casi no arma SQL propio: invoca por JDBC funciones y procedimientos de PostgreSQL
(`SELECT * FROM funcion(...)` y `{CALL procedimiento(...)}`).

| Operación | Función o procedimiento |
|---|---|
| Inicio de sesión y registro de la sesión | `autenticar_usuario_y_registrar_sesion` |
| Pantallas que puede abrir cada usuario | `obtener_uis_por_usuario` |
| Monitoreo de sesiones activas | `obtener_sesiones_activas_detalladas` |
| Altas | `insertar_pais`, `insertar_liga`, `insertar_equipo`, `insertar_jugador`, `insertar_partido` |
| Edición y bajas | `actualizar_nombre_pais`, `eliminar_pais`, `eliminar_jugador_por_nombre` |
| Datos para los formularios | `obtener_codigos_paises`, `obtener_paises_fifa_y_nombre`, `obtener_todos_los_paises`, `obtener_ligas_para_combo`, `obtener_equipos_para_combo` |

La conexión se identifica con `application_name=StrikeApp`, así que sus sesiones se distinguen
del resto de conexiones del servidor.

## Roles

Hay tres roles, cada uno con su propia interfaz. Qué pantallas ve cada usuario lo decide la base
de datos (`obtener_uis_por_usuario`), no el código:

- **Lectura** (`read_only`): consulta de la información.
- **Carga de datos** (`data_writer`): formularios de alta, edición y baja de países, ligas,
  equipos, jugadores y partidos.
- **Superadministrador** (`super_user`): tabla de sesiones activas con usuario, correo, rol,
  interfaz, dirección IP, puerto y PID del proceso de PostgreSQL.

## Tecnologías

Java 24 · JavaFX 25 · PostgreSQL (driver JDBC 42.7.3) · Maven

## Ejecución

1. Tener PostgreSQL con la base `strike` y sus funciones y procedimientos.
2. Configurar URL, usuario y contraseña en
   `src/main/java/com/dpardo/strike/repository/DatabaseConnection.java`.
3. Compilar con Maven (`./mvnw clean package`) y ejecutar la clase `com.dpardo.strike.Launcher`.
