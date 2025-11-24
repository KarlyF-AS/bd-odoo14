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

