# Docker con mysql y Sprig boot

![plot](./img/maxresdefault.jpg) 


#### Instalacion de mysql en docler

Descargamos la imagen de mysql

```sh
docker pull mysql
```

Agregamos un contenerdor 

```sh
docker run -d -p 13306:3306 --name mysql_container -e MYSQL_ROOT_PASSWORD=secret mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```sh