---
layout: post
author: Quino
title: Vacaciones , DOCKERLABS
tags:
  - cft
  - hacking
  - etico
---

Este es un reto clasificado como de dificultad easymuy facil de la plataforma [Dockerlabs](https://dockerlabs.es "dockerlabs").

Nos encontramos con las siguientes características y dificultades , en el tendremos que localizar la entrada al sistema , explotacion de ssh por fuerza bruta , movimiento lateral entre usuarios y escalada de privelegios.

## HERRAMIENTAS Y RECURSOS USADOS

- nmap
- hydra
- searchbins

## Enumeracion

Como al arrancar la maquina ya nos dice la ip , nos olvidamos de arp-scan , comprobamos la conexion con un ping.

```bash

ping -c1 172.17.0.2

```

Ahora realizamos un nmap como de costumbre sin preocuparnos del ruido pues estamos en entorno de pruebas.

```bash
sudo nmap -p- -sS -sC -sV --min-rate=5000 -n -vvv -Pn 172.17.0.2 -oX puertos
```

[Reporte](r3p0rt3s/vacaciones_nmap.html "Reporte nmap")

Observamos el resultado y vemos que solo tiene el 22 y el 80 abiertos .

Empecemos por revisar la web y nos encontramos con una web totalmente en blanco asi que directos a ver la fuente.

![web](assets/vacaciones/web.png)

Empezamos por un ataque de fuerza bruta contra el usario camilo porque contra camilo pq entiendo que el receptor del mail es un usario seguro de la maquina.

```bash
hydra -l camilo -P rockyou.txt -vV 172.17.02 ssh
```

![hydra](assets/vacaciones/hydra.png)

## Infiltración

Ahora que ya tenemos todo listo pasamos a conectarnos a la maquina con el usario y la contraseña proporcionada por hydra.

![login_ssh](assets/vacaciones/logincamilo.png)

## Escalada de privilegios
  
Una vez dentro listamos directorios importantes y tambien lo primero es ejecutar como de costumbre find y sudo que nos buscaran ficheros con permisos erroneos.

```bash
find / -perm -4000 2>/dev/null
sudo -l
```

![salida_sudo](assets/vacaciones/sudonova.png)


Como vemos ninguno de los dos comandos nos arroja luz pero recordemos lo que tenemos hasta ahora la web y los users y la web nos hablaba de juan y un correo a camilo.
Momento de buscar el mail.


```bash
cat /var/mail/camilo.txt
```

![mail](assets/vacaciones/mail.png)

Ahi tenemos en texto plano la pass de juan , accedamos

```bash
su juan
```
Una vez como juan volvemos a usar sudo y find y esta vez tenemos positivo con ruby.

```bash
sudo -l
```

![root](assets/vacaciones/root.png)


Pobre pedro no pasamos por su cuenta , no nos va a dar acceso o si ya eres root.

Gracias por leer

