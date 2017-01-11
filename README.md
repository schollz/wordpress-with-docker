# wordpress-with-docker

2 blogs: 360 MB memory EACH, and 3.1G used total (all files)

### `docker-compose.yml`

```yaml
version: '2'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: wordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8001:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_PASSWORD: wordpress
     volumes:
       - /path/to/some/folder/on/your/computer/wp_html:/var/www/html
volumes:
    db_data:
```

To start: `docker-compose up` (Add `-d` for daemon mode)

To stop: `docker-compose stop`

### `Caddyfile`

```
http://blogname {
    proxy / 127.0.0.1:8006 {
        transparent
    }
}
```

Make sure to goto your blog and update it accordingly to http://blogname.

For using SSL, [checkout this blog](https://www.heavymetalcoder.com/how-to-get-wordpress-working-with-https-behind-a-reverse-proxy/)

### Backup/Restore 

[(source)](https://libertyseeds.ca/2015/11/24/Backup-migration-and-recovery-with-WordPress-and-Docker-Compose/)

Backup:

```
docker exec -i wordpress_db_1 mysqldump --user=wordpress --password=wordpress wordpress > backup.sql
tar -czvf wp_html.tar.gz wp_html
```

Restore:

```
docker exec -i wordpress_db_1 mysql --user=wordpress --password=wordpress wordpress < backup.sql
tar -xvzf wp_html.tar.gz 
```

