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


Creamos una conexion desde docker con network 

```sh
sudo docker network create --driver bridge my-net
```

Miramos la conexiones en docker 

```sh 
sudo  docker network ls
```

Desconectamos el network con contenedor de mysql_container

```sh
sudo docker network disconnect bridge mysql_container
```

Agregamos la red de my-net a contectamos al contendor de mysql_container

```sh
sudo docker network connect my-net  mysql_container
```

Miramos la conexion de my-net mysql_container

```sh
  sudo docker network inspect my-net

  [
    {
        "Name": "my-net",
        "Id": "1f6ce77b80f942b6c0c7a67968496a36d1382d2ab212b305c333bf7ef562dbee",
        "Created": "2021-03-24T12:27:31.441272119-05:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "68a89ef10edaad2584de2a93c86e51bc1fa85f29356a002598e46a6a57bd2cc4": {
                "Name": "mysql_container",
                "EndpointID": "4d3aaf468983a4f76040634597d9704bcf5f5ab2a3b1c3ac27dce34e5691fe9d",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

```

La conexion de nuentra aplicaion

```sh
spring.datasource.url=jdbc:mysql://localhost::13306/borisbd?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
```

La cambiamos por la conexion del contenedor donde esta base de datos
```sh
spring.datasource.url=jdbc:mysql://mysql_container/borisbd?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
```

Generamos en el backend hecho en spring en un .jar

```sh
mvn package
```
Generamos en el backend hecho en spring en un .jar sin la pruebas unitarias

```sh
mvn clean install -Dmaven.test.skip=true
```

Luego miramos si funciona bien 
```sh
java -jar ./target/employee-0.0.1-SNAPSHOT.jar
```

Luego generamos una imagen en docker donde nuestro proyecto de spring y en es mismo lugar debe de estar un archivo Dockerfile

```sh
sudo docker build -t user/users_service:V1 .
```

luego agreamos la image en un contenerdor 

```sh 
sudo docker run --network my-net -d -p 8080:8080 --name (nombre_contenedor)users_container  (IdUser)b15b8672a0f6
```
Ejemplo de los videos
https://www.youtube.com/watch?v=ux1uzPAMRzk&t=532s&ab_channel=JulioCSanchez-DevOps