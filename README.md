# Implementación de un cortafuego personal

![Cortafuegos](image/Cortafuegos.jpg)

> [Tareas a realizar]()
### Vamos a realizar los primeros pasos para implementar un cortafuegos en un nodo de una red, aquel que se ejecuta en el propio equipo que trata de proteger, lo que a veces se denomina un cortafuegos personal.

## Ejercicio1: Implementación de un cortafuego personal
#### Vamos a realizar los primeros pasos para implementar un cortafuegos en un nodo de una red, aquel que se ejecuta en el propio equipo que trata de proteger, lo que a veces se denomina un cortafuegos personal.

### Esquema de red
#### Vamos a utilizar una máquina en openstack, que vamos a crear con la receta heat: escenario1.yaml. La receta heat ha deshabilitado el cortafuego que nos ofrece openstack (todos los puertos de todos los protocolos están abiertos). La máquina creada tendrá un servidor web instalado. Vamos a trabajar con la red de las ips flotantes: 172.22.0.0/16.

### Limpieza de las reglas previas
------------------------------------------------------
~~~
iptables -F
iptables -t nat -F
iptables -Z
iptables -t nat -Z
~~~

### Vamos a permitir ssh
------------------------------------------------------
###### Cómo estamos conectado a la máquina por ssh, vamos a permitir la conexión ssh desde la red 172.22.0.0/16, antes de cambiar las políticas por defecto a DROP, para no perder la conexión:
~~~
iptables -A INPUT -s 172.22.0.0/16 -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -d 172.22.0.0/16 -p tcp --sport 22 -j ACCEPT
~~~

### Política por defecto
------------------------------------------------------

~~~
iptables -P INPUT DROP
iptables -P OUTPUT DROP
~~~

###### Comprobamos que el equipo no puede acceder a ningún servicio ni de Internet ni de la red local, ya que la política lo impide.

### Permitimos tráfico para la interfaz loopback
------------------------------------------------------
~~~
iptables -A INPUT -i lo -p icmp -j ACCEPT
iptables -A OUTPUT -o lo -p icmp -j ACCEPT
~~~

### Peticiones y respuestas protocolo ICMP
------------------------------------------------------
~~~
iptables -A INPUT -i eth0 -p icmp -j ACCEPT
iptables -A OUTPUT -o eth0 -p icmp -j ACCEPT
~~~

### Comprobamos su funcionamiento haciendo ping a una IP pública:
------------------------------------------------------
~~~
ping -c 1 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=64 time=0.485 ms

--- 1.1.1.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.485/0.485/0.485/0.000 ms
~~~

### Consultas y respuestas DNS
------------------------------------------------------
~~~
iptables -A OUTPUT -o eth0 -p udp --dport 53 -j ACCEPT
iptables -A INPUT -i eth0 -p udp --sport 53 -j ACCEPT
~~~

###### Comprobamos su funcionamiento con una consulta DNS:
~~~
dig @1.1.1.1 www.josedomingo.org
Tráfico http (que la máquina pueda navegar)Permalink
iptables -A OUTPUT -o eth0 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 80 -j ACCEPT
~~~

###### Comprobamos que funciona accediendo a un servicio http (! no https)
~~~
curl http://portquiz.net:80
Port 80 test successful!
Your IP: x.x.x.x
~~~

### Tráfico https
------------------------------------------------------
~~~
iptables -A OUTPUT -o eth0 -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --sport 443 -j ACCEPT
~~~

###### Comprobamos que funciona abriendo un navegador y accediendo a cualquier sitio web (hoy en día la mayoría son https).

### Permitimos el acceso a nuestro servidor web
------------------------------------------------------
~~~
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -j ACCEPT
iptables -A INPUT -i eth0 -p tcp --dport 80 -j ACCEPT
~~~

## Tareas a realizar:

[Tarea 1](https://github.com/MoralG/Cortafuego_personal/blob/master/Cortafuegos_personal.md#1-permite-poder-hacer-conexiones-ssh-al-exterior). Permite poder hacer conexiones ssh al exterior

[Tarea 2](https://github.com/MoralG/Cortafuego_personal/blob/master/Cortafuegos_personal.md#2-deniega-el-acceso-a-tu-servidor-web-desde-una-ip-concreta). Deniega el acceso a tu servidor web desde una ip concreta

[Tarea 3](https://github.com/MoralG/Cortafuego_personal/blob/master/Cortafuegos_personal.md#3-permite-hacer-consultas-dns-s%C3%B3lo-al-servidor-1921682022-comprueba-que-no-puedes-hacer-un-dig-1111). Permite hacer consultas DNS sólo al servidor 192.168.202.2 Comprueba que no puedes hacer un dig @1.1.1.1

[Tarea 4](https://github.com/MoralG/Cortafuego_personal/blob/master/Cortafuegos_personal.md#4-no-permitir-el-acceso-al-servidor-web-de-wwwjosedomingoorg-tienes-que-utilizar-la-ip-puedes-acceder-a-fpjosedomingoorg). No permitir el acceso al servidor web de www.josedomingo.org (Tienes que utilizar la ip). ¿Puedes acceder a fp.josedomingo.org?

[Tarea 5](https://github.com/MoralG/Cortafuego_personal/blob/master/Cortafuegos_personal.md#5-permite-mandar-un-correo-usando-nuestro-servidor-de-correo-babuino-smtp-para-probarlo-ejecuta-un-telnet-babuino-smtpgonzalonazarenoorg-25). Permite mandar un correo usando nuestro servidor de correo: babuino-smtp. Para probarlo ejecuta un telnet babuino-smtp.gonzalonazareno.org 25.

[Tarea 6](https://github.com/MoralG/Cortafuego_personal/blob/master/Cortafuegos_personal.md#6-instala-un-servidor-mariadb-y-permite-los-accesos-desde-la-ip-de-tu-cliente). Instala un servidor mariadb, y permite los accesos desde la ip de tu cliente
