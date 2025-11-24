# DB-ODOO
Para esta tarea utilicé la base de datos de la Tarea 12, ya que el archivo docker-compose.yml ya estaba configurado y listo.
Para la creación de las tablas adicionales y la ejecución de las consultas SQL utilicé PostgreSQL, considerándolo la herramienta más adecuada para esta práctica.
```yaml
services:
  web:
    image: odoo:18.0
    depends_on:
      - db
    ports:
      - "8075:8069"
    environment:
      HOST: db
      USER: odoo
      PASSWORD: odoo
    volumes:
      - odoo_data:/var/lib/odoo
      - ./backups:/var/lib/odoo/backups

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: postgres
      POSTGRES_USER: odoo
      POSTGRES_PASSWORD: odoo
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  odoo_data:
  db_data:
```
--- 
## Pasos básicos previos
   Antes de pulsar **"Create database"**, seleccionamos la opción **"Demo data"** para tener datos de ejemplo en Odoo.  
   ![1.png](bd-odoo/1.png)

   Antes de ejecutar cualquier consulta SQL, es recomendable hacer un backup de la base de datos. Esto nos permite restaurarla si ocurre algún error.  
   - Odoo descargará un archivo `.zip` que contiene **toda la base de datos**.  
   ![3.png](bd-odoo/3.png)

   Para asegurarnos de que todo está correcto, vamos a la carpeta del proyecto (donde se encuentra el archivo `.yml`) y comprobamos que el `.zip` se encuentre dentro de la carpeta `backups`.  
   ![4.png](bd-odoo/4.png) ![5.png](bd-odoo/5.png)

Una vez que la base de datos está configurada y el backup verificado, podemos comenzar con los ejercicios prácticos de la tarea.

--- 
## Apartado 0: Ingresar a la bd desde la terminal:
Ingresar a la bd

![Captura desde 2025-11-24 20-25-58.png](bd-odoo/Captura%20desde%202025-11-24%2020-25-58.png)
--- 
## Apartado 1: Crear tabla `EmpresasFCT`
Se creó una tabla nueva con los siguientes campos:
- `idEmpresa`: autonumérico, clave primaria
- `nombre`: texto (máx. 40 caracteres)
- `quiereAlumnos`: booleano
- `numAlumnos`: número entero
- `fechaContacto`: tipo fecha


`Consulta utilizada:`


```sql
CREATE TABLE EmpresasFCT (
   idEmpresa SERIAL PRIMARY KEY,
   nombre VARCHAR(40),
   quiereAlumnos BOOLEAN,
   numAlumnos INTEGER,
   fechaContacto DATE);
```
![Captura desde 2025-11-24 20-38-25.png](bd-odoo/Captura%20desde%202025-11-24%2020-38-25.png)
---
