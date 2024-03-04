**Clonar Odoo 17 en una carpeta fuera del proyecto:** `git clone git@github.com:odoo/odoo.git -b 17.0 --depth 1 --single-branch odoo17`

**Clonar odoo-stubs en una carpeta fuera del proyecto:** `git clone git@github.com:odoo-ide/odoo-stubs.git -b 17.0`

### A) PASOS PARA HABILITAR AMBIENTE DE DESARROLLO
1. Instalar docker (Para tu S.O.)

2. crear contenedor en docker (utilizando el archivo docker-compose.yaml)
docker-compose up -d

3. validar inicio de Odoo y crear base de datos local 
**master-password: xxxxxxxxxxxx** (depende de tu instalación docker)

[Localhost](http://127.0.0.1:8200/)

4. agregar la siguiente linea al archivo creado *config/odoo.conf* (luego reiniciar contenedor)

`addons_path=/usr/lib/python3/dist-packages/odoo/addons,/mnt/extra-addons,/var/lib/odoo/.local/share/Odoo/addons/17.0`

### B) COMANDOS ÚTILES DE DOCKER
- listar contenedores:
docker container ls
- entrar dentro del contenedor  (nombre_contenedor=odoo)
docker exec -it [nombre-contenedor] /bin/bash
- entrar como root (odoo) para instalar modulos o para acceder a Postgres
docker exec -u root -t -i odoo /bin/bash
- crear modulos con scaffold 
odoo scaffold [nombre_modulo] /mnt/extra-addons
- acceder a psql (postgres) estando dentro del docker db como root
psql -U odoo -d [nombre_bd_odoo]

---

### C) contenido de archivo docker-compose.yaml

```
version: '3.3'

services:

  odoo:
    image: odoo:17.0
    container_name: odoo
    restart: unless-stopped
    links:
      - db:db    
    depends_on:
      - db
    ports:
      - "8200:8069"
    volumes:
      - odoo-data:/var/lib/odoo
      - ./config:/etc/odoo
      - ./addons:/mnt/extra-addons

  db:
    image: postgres:16.0
    container_name: db
    restart: unless-stopped       
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_PASSWORD=odoo
      - POSTGRES_USER=odoo
      - PGDATA=/var/lib/postgresql/data/pgdata
    ports:
      - "5432:5432"
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  odoo-data:
  db-data:

```