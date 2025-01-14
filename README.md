# Tarea 10 SXE

<details>

  <summary>Creación del archivo DockerFile</summary>
<br>

Se crea el archivo docker con lo necesario para levantar los servicios de Odoo, Postgres y PgAdmin:

```bash
services:
  odoo:
    image: odoo:17
    restart: always
    ports:
      - "8069:8069"
    depends_on:
      - db
    environment:
      - USER=odoo
      - PASSWORD=odoo
    volumes:
      - ./config:/etc/odoo
      - ./extra-addons:/mnt/extra-addons
  db:
    image: postgres:latest
    restart: always
    environment:
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo
      - POSTGRES_DB=postgres
    volumes:  
      - local_pgdata:/var/lib/postgresql/data
  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin4_container
    restart: always
    depends_on:
      - db
    ports:
      - "5050:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: ssanromangiraldez@danielcastelao.org
      PGADMIN_DEFAULT_PASSWORD: admin
    volumes:
      - pgadmin_data:/var/lib/pgadmin
volumes:
  local_pgdata:
  pgadmin_data:
  ```

</details>

<details>
  
  <summary>Comprobación y configuración de Odoo</summary>
<br>
Para ver si todo funcionó bien se accede en el navegador a nuestra IP con el puerto configurado para el contenedor de Odoo:

```bash
http://localhost:8069
```
Si todo salió bien dbería salir la página para la configuración de nuestra cuenta de Odoo con la creación de la base de datos:

![setup](https://github.com/user-attachments/assets/9aeb44f3-57c1-474c-9c65-6927e1e44547)

Después de rellenar los campos con tus datos necesitaremos iniciar sesión en Odoo con los datos recién puestos.

![loginodoo](https://github.com/user-attachments/assets/793aacfd-0a45-4c19-a9ef-9ed80dc83666)

Una vez dentro de Odoo comprobamos que todo va bien y ya podremos empezar a utilizarlo añadiendo los servicios que se requieran:

![odooFinal](https://github.com/user-attachments/assets/44253a5b-d4fc-434d-922e-bc34e105c189)

</details>

<details>
  
  <summary>Comprobación y configuración de la base de datos con PgAdmin</summary>
<br>
Para ver si el contenedor de PgAdmin está levantado se accede a su puerto establecido:

```bash
http://localhost:5050
```
Si todo salió bien debería salir la página para iniciar sesión:

![pg_adminLogin](https://github.com/user-attachments/assets/720ee277-67a3-4d95-9cbc-a11f11fa142b)

Después de rellenar los campos con los datos establecidos en el docker file podremos acceder a PgAdmin.
En mi caso al entrar en la página no me aparecía ninguna base de datos ni tabla, para arreglarlo tuve que introducir los datos de mi contenedor de postgres manualmente desde la página de PgAdmin.

Para hacer esto, en la terminal escribimos `docker ps -a` para ver la ID de nuestro contenedor de postgres, después escribimos `docker inspect <ID contenedor>`, de esta manera podemos ver la IP asociada a este contenedor.

La IP la añadimos al servidor de PgAdmin en el campo de *Host name/Address*, y los campos de *nombre de usuario* y *contraseña* se rellenan con los datos puestos anteriormente en el docker file:

![host](https://github.com/user-attachments/assets/db4330b6-b23c-4dcc-b541-262caaf3ec61)

Después de cubrir estos campos y darle a *Salvar* ya se actualiza todo y tendremos la base de datos con todas las tablas de la configuración de Odoo, deberían salir alrededor de 114 tablas como en la siguiente imagen:

![pgadmintablas](https://github.com/user-attachments/assets/8b489343-dcb7-4ec3-b09d-8b77fcdf29ee)

Una vez que tenemos esto ya tendríamos completada la instalación y configuración tanto de Odoo como de la base de datos.

</details>

<details>
  
  <summary>Preguntas</summary>
<br>

  **¿Que ocurre si en el ordenador local el puerto 5432 está ocupado? ¿Y si lo estuviese el 8069? ¿Como puedes solucionarlo?**

  Si estuviera ocupado el puerto 5432 lo que pasaría es que no se podría levantar el contenedor de Postgres, esto se debe a que el puerto por defecto de postgres es este. Para solucionarlo se podría establecer en el docker compose otro puesto que no esté usado añadiendo lo siguiente por ejemplo:

```bash
  ports:
  - "5433:5432"
```

De esta manera asigna el puerto 5433 al 5432.

  Si estuviera ocupado el puerto 8069 no se podría levantar el contenedor de Odoo porque es el puerto establecido en el docker compose. Para solucionarlo y poder levantarlo se tendría que cambiar el puerto de 8069 a, por ejemplo, 8070 de la misma forma que hicimos ateriormente para Postgres.

  Con esto solcionado ya podríamos acceder y trabajar con nuestros servicios.

</details>
