# Docker con mysql y Sprig boot

![plot](./img/maxresdefault.jpg) 


#### Instalacion de mysql en docler

Descargamos la imagen de mysql

```sh
docker pull mysql
```

Agregamos un contenerdor  

- Le agregamos la contraseña

```sh
docker run -d -p 13306:3306 --name mysql_container -e MYSQL_ROOT_PASSWORD=secret mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

Ejecutamos el contenedor

```sh
docker exec -it mysql_container mysql -uroot -p
```
Agregamos un usuario con todo lo permisos

```sh
mysql>  create user 'eddi' identified by 'secret';
```

Le damos todos lo permisos al usuario

```sh
mysql> GRANT ALL PRIVILEGES ON *.* TO 'eddi'@'%';
```

Creamos la base de datos 

```sh
mysql> CREATE DATABASE users_db;
mysql> USE users_db;
```

Creamos la base de datos
```sh
mysql> 
CREATE TABLE users 
( 
  ID DECIMAL(10, 0) NOT NULL 
, FIRST_NAME VARCHAR(25) NOT NULL 
, LAST_NAME VARCHAR(25) NOT NULL 
, EMAIL_ADDRESS VARCHAR(50) NOT NULL 
, CREATED_AT DATE NOT NULL 
, CREATED_BY VARCHAR(25) NOT NULL 
, UPDATED_AT DATE NOT NULL 
, UPDATED_BY VARCHAR(25) NOT NULL 
, CONSTRAINT PRIMARY KEY ( ID ) 
);

```

Generamos en el backend hecho en spring un .jar

```sh
mvn package
```

luego miramos si funciona bien 
```sh
java -jar ./target/employee-0.0.1-SNAPSHOT.jar
```

Luego generamos una imagen en docker

```sh
docker build -t "spring-boot-docker" .
```

luego agreamos la image en un contenerdor 

```sh 
docker run spring-boot-docker -p 8080:8080 spring-boot-docker:latest
```




