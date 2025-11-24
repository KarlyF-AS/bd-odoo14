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
## Apartado 2 - Insertar registros
Se insertaron 5 registros de ejemplo en `EmpresasFCT` utilizando `INSERT`.


`Consulta utilizada:`


```sql
INSERT INTO EmpresasFCT (nombre, quiereAlumnos, numAlumnos, fechaContacto)
VALUES
('Empresa1', true, 3, '2025-01-10'),
('Empresa2', false, 0, '2025-02-02'),
('Empresa3', true, 2, '2025-01-28'),
('Empresa4', true, 5, '2025-02-15'),
('Empresa5', false, 1, '2025-01-05');
```
![7.png](bd-odoo/7.png)
---
## Apartado 3 - Consultar tabla `EmpresasFCT`
Se realizó un `SELECT` mostrando todos los datos de la tabla, ordenados por `fechaContacto` de más reciente a más antiguo.


`Consulta utilizada:`


```sql
SELECT * FROM EmpresasFCT
ORDER BY fechaContacto DESC;
```
![8 part_3.png](bd-odoo/8%20part_3.png)
---
## Apartado 4 - Listado de contactos
Se listaron todos los contactos de Odoo que **no son empresas**, excluyendo ciudad `Tracy` con código postal `95304`, mostrando:
- Nombre del contacto
- Ciudad
- Código postal
- Nombre comercial de la empresa 
Ordenados alfabéticamente por el nombre comercial.


`Consulta utilizada:`


```sql
SELECT name AS nombre,
      street2 AS ciudad,
      zip AS codigo_postal,
      commercial_partner_id AS nombre_comercial_empresa
FROM res_partner
WHERE is_company = false
 AND NOT (street2='Tracy' AND zip='95304')
ORDER BY commercial_partner_id;
```
![9.1.png](bd-odoo/9.1.png)
![9.png](bd-odoo/9.png)
---
## Apartado 5 - Empresas proveedoras con reembolsos
Se obtuvieron las empresas proveedoras que han emitido algún reembolso (`move_type='in_refund'`), mostrando:
- Nombre de la empresa
- Número de factura
- Fecha de factura
- Total con impuestos
- Total sin impuestos 
Ordenadas por fecha, de más reciente a más antigua.


`Consulta utilizada:`


```sql
SELECT rp.name AS nombre_empresa,
      am.name AS numero_factura,
      am.invoice_date AS fecha_factura,
      am.amount_total AS total_con_impuestos,
      am.amount_untaxed AS total_sin_impuestos
FROM account_move am
JOIN res_partner rp ON am.partner_id = rp.id
WHERE am.move_type='in_refund'
ORDER BY am.invoice_date DESC;
```
![10.png](bd-odoo/10.png)
---
### Apartado 6 - Empresas clientes con más de 2 facturas
Se listaron las empresas clientes con más de dos facturas de venta confirmadas (`move_type='out_invoice'` y `state='posted'`), mostrando:
- Nombre de la empresa
- Número de facturas
- Total con impuestos
- Total sin impuestos


`Consulta utilizada:`


```sql
SELECT rp.name AS nombre_empresa,
      COUNT(am.id) AS numero_facturas,
      SUM(am.amount_total) AS total_con_impuestos,
      SUM(am.amount_untaxed) AS total_sin_impuestos
FROM account_move am
JOIN res_partner rp ON am.partner_id = rp.id
WHERE am.move_type='out_invoice' AND am.state='posted'
GROUP BY rp.name
HAVING COUNT(am.id) > 2;
```
![11.png](bd-odoo/11.png)
---
### Apartado 7 - Actualización de correos
Se actualizaron los correos electrónicos de los contactos cuyo dominio era `@bilbao.example.com`, cambiándolos por `@bilbao.bizkaia.neus`. 
Se verificó previamente la lista de emails afectados antes de aplicar el cambio.


`Consulta utilizada:`




`Listar emails`
```sql
SELECT name, email
FROM res_partner
WHERE email LIKE '%@bilbao.example.com';
```
1. ![12.1.png](bd-odoo/12.1.png)


`Actualizar emails:`
```sql
UPDATE res_partner
SET email = regexp_replace(email, '@bilbao\.example\.com$', '@bilbao.bizkaia.neus')
WHERE email LIKE '%@bilbao.example.com';
```
2. ![12.2.png](bd-odoo/12.2.png)


`Verificación de la actualización:`
```sql
SELECT name, email
FROM res_partner
WHERE email LIKE '%@bilbao.bizkaia.neus';
```
3. ![12.3.png](bd-odoo/12.3.png)
---
`Fin`

