# Taller Docker Compose - Gestor de Incidencias

Despliegue multicontenedor compuesto por frontend web, API backend y base de datos PostgreSQL aislados mediante redes personalizadas y almacenamiento persistente.

## Respuestas a las cuestiones del taller

1. **¿Por qué el frontend no necesita conectarse a PostgreSQL?**
   Por seguridad y separación de capas: el frontend solo entrega la interfaz al navegador y envía peticiones HTTP a la API. Si tuviera acceso directo a PostgreSQL, cualquier vulnerabilidad en la capa web expondría toda la base de datos.

2. **¿Por qué el backend pertenece a dos redes?**
   Porque actúa como intermediario entre capas: necesita estar en la red `frontend` para recibir peticiones de la web y en la red `datos` para comunicarse con PostgreSQL, manteniendo ambas redes aisladas entre sí.

3. **¿Qué servicio necesita publicar un puerto en el anfitrión?**
   Únicamente el servicio `frontend`, ya que es el único punto de entrada que los usuarios externos necesitan abrir desde su navegador. Tanto el backend como PostgreSQL operan exclusivamente de forma interna.

4. **¿Por qué publicar PostgreSQL hacia fuera no es la solución adecuada para un fallo de comunicación interno?**
   Porque exponer el puerto `5432` en el host abre la base de datos al exterior aumentando la superficie de ataque, rompe el aislamiento de red y obliga a depender de la IP del anfitrión en lugar de resolver el servicio internamente por nombre.

5. **¿Qué ventaja ofrece Docker Compose respecto a ejecutar los tres contenedores manualmente?**
   Permite definir toda la infraestructura de forma declarativa en un único archivo (`compose.yaml`), gestionando automáticamente el orden de arranque (`depends_on` y `healthcheck`), las variables de entorno, los volúmenes y las redes con un solo comando (`docker compose up -d`).

6. **¿Por qué hemos dividido la arquitectura en dos redes?**
   Para aplicar el principio de mínimo privilegio y segmentación de red: `frontend` y `db` quedan completamente incomunicados entre sí, reduciendo el impacto ante un posible compromiso del servidor web.

7. **¿Por qué el backend utiliza `db` como dirección de PostgreSQL?**
   Porque Docker Compose incorpora un servidor DNS interno en las redes definidas por el usuario que resuelve automáticamente el nombre de cada servicio (`db`) a la IP interna dinámica asignada al contenedor.

8. **¿Qué diferencia existe entre detener un proyecto y eliminar sus volúmenes?**
   Detener o bajar el proyecto (`docker compose stop` o `docker compose down`) para o elimina los contenedores y redes pero conserva los volúmenes con nombre (`postgres_data`), manteniendo los datos intactos. Eliminar los volúmenes (`docker compose down -v`) borra también el almacenamiento persistente de la base de datos.

9. **¿Qué consecuencias tendría para la empresa perder el volumen de PostgreSQL?**
   La pérdida total e irreversible de todos los registros e historial de incidencias almacenados en la base de datos (salvo que exista una copia de seguridad externa previa), provocando caída operativa y pérdida de trazabilidad.
