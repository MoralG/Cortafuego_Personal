# Ejercicio1: Implementación de un cortafuego personal

###### Si necesitamos borrar alguna regla utilizamos lo siguiente

~~~
sudo iptables -F
sudo iptables -t nat -F
sudo iptables -Z
sudo iptables -t nat -Z
sudo iptables -A INPUT -s 172.22.0.0/16 -p tcp --dport 22 -j ACCEPT
sudo iptables -A OUTPUT -d 172.22.0.0/16 -p tcp --sport 22 -j ACCEPT
sudo iptables -P INPUT DROP
sudo iptables -P OUTPUT DROP
sudo iptables -A INPUT -i lo -p icmp -j ACCEPT
sudo iptables -A OUTPUT -o lo -p icmp -j ACCEPT
sudo iptables -A INPUT -s 172.22.0.0/16 -p tcp --dport 22 -j ACCEPT
sudo iptables -A OUTPUT -d 172.22.0.0/16 -p tcp --sport 22 -j ACCEPT
sudo iptables -A OUTPUT -o eth0 -p icmp -j ACCEPT
sudo iptables -A INPUT -i eth0 -p icmp -j ACCEPT
sudo iptables -A OUTPUT -o eth0 -p udp --dport 53 -j ACCEPT
sudo iptables -A INPUT -i eth0 -p udp --sport 53 -j ACCEPT
sudo iptables -A OUTPUT -o eth0 -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -i eth0 -p tcp --sport 80 -j ACCEPT
sudo iptables -A OUTPUT -o eth0 -p tcp --dport 443 -j ACCEPT
sudo iptables -A INPUT -i eth0 -p tcp --sport 443 -j ACCEPT
sudo iptables -A OUTPUT -o eth0 -p tcp --sport 80 -j ACCEPT
sudo iptables -A INPUT -i eth0 -p tcp --dport 80 -j ACCEPT
~~~

~~~
sudo iptables -L -nv --line-numbers
~~~

1. Permite poder hacer conexiones ssh al exterior.

~~~
sudo iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --sport 22 -j ACCEPT
~~~

2. Deniega el acceso a tu servidor web desde una ip concreta.

~~~
sudo iptables -I INPUT -s 172.22.2.43 -p tcp --sport 80 -j DROP
sudo iptables -I OUTPUT -d 172.22.2.43 -p tcp --dport 80 -j DROP
~~~

~~~
sudo iptables -I INPUT -s 172.22.2.43 -p tcp --sport 443 -j DROP
sudo iptables -I OUTPUT -d 172.22.2.43 -p tcp --dport 443 -j DROP
~~~

3. Permite hacer consultas DNS sólo al servidor 192.168.202.2 Comprueba que no puedes hacer un dig @1.1.1.1.


###### Eliminamos la regla DNS anterior que daba acceso a todo:

~~~
sudo iptables -L -nv --line-numbers
sudo iptables -D INPUT <number>
sudo iptables -D OUTPUT <number>
~~~


~~~
sudo iptables -A INPUT -i eth0 -p udp --sport 53 -s 192.168.202.2 -j ACCEPT
sudo iptables -A OUTPUT -o eth0 -p udp --dport 53 -d 192.168.202.2 -j ACCEPT
~~~

###### Comprobación

~~~
dig @192.168.202.2

   ; <<>> DiG 9.11.5-P4-5.1-Debian <<>> @192.168.202.2
   ; (1 server found)
   ;; global options: +cmd
   ;; Got answer:
   ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24337
   ;; flags: qr rd ra ad; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 27
   
   ;; OPT PSEUDOSECTION:
   ; EDNS: version: 0, flags:; udp: 4096
   ; COOKIE: 9faa55396bb13860e8bfcf115ddbb5eb569eb7e5c5e25703 (good)
   ;; QUESTION SECTION:
   ;.				IN	NS
   
   ;; ANSWER SECTION:
   .			510654	IN	NS	d.root-servers.net.
   .			510654	IN	NS	b.root-servers.net.
   .			510654	IN	NS	l.root-servers.net.
   .			510654	IN	NS	i.root-servers.net.
   .			510654	IN	NS	e.root-servers.net.
   .			510654	IN	NS	a.root-servers.net.
   .			510654	IN	NS	h.root-servers.net.
   .			510654	IN	NS	m.root-servers.net.
   .			510654	IN	NS	k.root-servers.net.
   .			510654	IN	NS	j.root-servers.net.
   .			510654	IN	NS	g.root-servers.net.
   .			510654	IN	NS	f.root-servers.net.
   .			510654	IN	NS	c.root-servers.net.
   
   ;; ADDITIONAL SECTION:
   a.root-servers.net.	597054	IN	A	198.41.0.4
   b.root-servers.net.	597054	IN	A	199.9.14.201
   c.root-servers.net.	597054	IN	A	192.33.4.12
   d.root-servers.net.	597054	IN	A	199.7.91.13
   e.root-servers.net.	597054	IN	A	192.203.230.10
   f.root-servers.net.	597054	IN	A	192.5.5.241
   g.root-servers.net.	597054	IN	A	192.112.36.4
   h.root-servers.net.	597054	IN	A	198.97.190.53
   i.root-servers.net.	597054	IN	A	192.36.148.17
   j.root-servers.net.	597054	IN	A	192.58.128.30
   k.root-servers.net.	597054	IN	A	193.0.14.129
   l.root-servers.net.	597054	IN	A	199.7.83.42
   m.root-servers.net.	597054	IN	A	202.12.27.33
   a.root-servers.net.	597054	IN	AAAA	2001:503:ba3e::2:30
   b.root-servers.net.	597054	IN	AAAA	2001:500:200::b
   c.root-servers.net.	597054	IN	AAAA	2001:500:2::c
   d.root-servers.net.	597054	IN	AAAA	2001:500:2d::d
   e.root-servers.net.	597054	IN	AAAA	2001:500:a8::e
   f.root-servers.net.	597054	IN	AAAA	2001:500:2f::f
   g.root-servers.net.	597054	IN	AAAA	2001:500:12::d0d
   h.root-servers.net.	597054	IN	AAAA	2001:500:1::53
   i.root-servers.net.	597054	IN	AAAA	2001:7fe::53
   j.root-servers.net.	597054	IN	AAAA	2001:503:c27::2:30
   k.root-servers.net.	597054	IN	AAAA	2001:7fd::1
   l.root-servers.net.	597054	IN	AAAA	2001:500:9f::42
   m.root-servers.net.	597054	IN	AAAA	2001:dc3::35
   
   ;; Query time: 1 msec
   ;; SERVER: 192.168.202.2#53(192.168.202.2)
   ;; WHEN: Mon Nov 25 11:07:23 UTC 2019
   ;; MSG SIZE  rcvd: 839


dig @1.1.1.1

   ; <<>> DiG 9.11.5-P4-5.1-Debian <<>> @1.1.1.1
   ; (1 server found)
   ;; global options: +cmd
   ;; connection timed out; no servers could be reached
~~~

4. No permitir el acceso al servidor web de www.josedomingo.org (Tienes que utilizar la ip). ¿Puedes acceder a fp.josedomingo.org?

###### A la dirección ftp.josedomingo.org si se puede acceder

~~~
sudo iptables -I INPUT -i eth0 -p tcp --sport 80 -s 137.74.161.90 -j DROP
sudo iptables -I OUTPUT -o eth0 -p tcp --dport 80 -d 137.74.161.90 -j DROP
~~~

~~~
sudo iptables -I INPUT -i eth0 -p tcp --sport 443 -s 137.74.161.90 -j DROP
sudo iptables -I OUTPUT -o eth0 -p tcp --dport 443 -d 137.74.161.90 -j DROP
~~~

5. Permite mandar un correo usando nuestro servidor de correo: babuino-smtp. Para probarlo ejecuta un telnet babuino-smtp.gonzalonazareno.org 25.

~~~
sudo iptables -A OUTPUT -o eth0 -p tcp --dport 25 -d 192.168.203.3 -j ACCEPT
sudo iptables -A INPUT -i eth0 -p tcp --sport 25 -s 192.168.203.3 -j ACCEPT
~~~

###### Comprobación

~~~
telnet babuino-smtp.gonzalonazareno.org 25
   Trying 192.168.203.3...
   Connected to babuino-smtp.gonzalonazareno.org.
   Escape character is '^]'.
   220 babuino-smtp.gonzalonazareno.org ESMTP Postfix (Debian/GNU)
~~~

6. Instala un servidor mariadb, y permite los accesos desde la ip de tu cliente.

