# Explica métodos para 'abrir' una consola/shell a un contenedor que se está ejecutando

   - Para abrir la consola/shell a un contenedor que se está ejecutando lo que podemos hacer es usar el comando exec:

    docker exec -it contenedor bash

    - Aunque si estamos en el code y este está ejecutado, llendo a la zona de docker y haciendo clic derecho sobre el y seleccionando la opción de attach shell, ya estaria.

# En el contenedor anterior con que opciones tiene que haber sido arrancado para poder interactuar con las entradas y salidas del contenedor

- Para poder interactuar con el deberíamos haberlo arrancado con la opción (-it).

# ¿Cómo sería un fichero docker-compose para que dos contenedores se comuniquen entre si en una red solo de ellos?

- Para ello debemos crear primero el docker compose y una vez creado debemos crear nuestra propia red privada, ya con todo esto creado para añadir los dos contenedores en la misma red, en el docker compose ponemos la opción 
(networks) y debajo añadimos los contenedores.

services:
  asir_bind9:
    container_name: examen_asir2
   
    image: ubuntu/bind9
  
    ports:
      - "53:53/tcp"
      - "53:53/udp"
    networks:
      bind9_subnet:
        ipv4_address: 172.16.0.2
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
  cliente:
    container_name: clienteExamen_asir2
    image: alpine
    
    tty: true
    stdin_open: true

    networks:
      bind9_subnet:
        ipv4_address: 172.16.0.2
    
networks:
  bind9_subnet:
    external: true

# ¿Qué hay que añadir al fichero anterior para que un contenedor tenga la IP fija?

Para esto como podemos ver en la anterior pregunta dentro de networks usamos ipv4_adress: (La ip fija que queremos que tenga).

networks:
      bind9_subnet:
        ipv4_address: 172.22.5.2

# ¿Que comando de consola puedo usar para saber las ips de los contenedores anteriores? Filtra todo lo que puedas la salida.

- docker network inspect bind9_subnet

# ¿Cual es la funcionalidad del apartado "ports" en docker compose?

- Mapeo de puertos con los del equipo.

# ¿Para que sirve el registro CNAME? Pon un ejemplo

- Por decirlo de una forma es como un nombre corto para el nombre de un dominio, como un alias de un dominio a otro dominio.
- Si tenemos una dirección www.ejemplo.com. El CNAME es de www.ejemplo.com es ejemplo.com.

- con lo cual esto es lo que tendríamos:

ejemplo.com A 1.1.1.1

- Y lo buascariamos de esta forma:

dig CNAME @1.1.1.1 nombre_dominio.nombre_base_de_datos.int

# ¿Como puedo hacer para que la configuración de un contenedor DNS no se borre si creo otro contenedor?

- Para eso se utilizarán volumenes los cuales alamacenan la información aunqeu este se elimine. Con lo cual si creamos uno con el mismo nombre de seguro no nos dejara diciendo que ya esta creado.

 ports:
      - "53:53/tcp"
      - "53:53/udp"
    networks:
      bind9_subnet:
        ipv4_address: 172.22.5.1
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind

# Añade una zona tiendadeelectronica.int en tu docker DNS que tenga

- www a la IP 172.16.0.1

- owncloud sea un CNAME de www

- un registro de texto con el contenido "1234ASDF"

- Comprueba que todo funciona con el comando "dig"

dig @172.16.0.1 www.tiendaelectronica.int

; <<>> DiG 9.18.19-1~deb12u1-Debian <<>> @172.16.0.1 www.tiendaelectronica.int
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 26555
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.tiendaelectronica.int.     IN      A

;; Query time: 4 msec
;; SERVER: 172.16.0.1#53(172.16.0.1) (UDP)
;; WHEN: Mon Nov 13 15:52:13 UTC 2023
;; MSG SIZE  rcvd: 54

- Muestra en los logs que el servicio arranca correctamente
    /var/log# cat auth.log
- - con cat dpkg.log vemos esto:
 2023-11-13 15:38:10 configure dnsutils:all 1:9.18.19-1~deb12u1 <none>
2023-11-13 15:38:10 status unpacked dnsutils:all 1:9.18.19-1~deb12u1
2023-11-13 15:38:10 status half-configured dnsutils:all 1:9.18.19-1~deb12u1
2023-11-13 15:38:10 status installed dnsutils:all 1:9.18.19-1~deb12u1
2023-11-13 15:38:10 trigproc libc-bin:amd64 2.36-9+deb12u3 <none>
2023-11-13 15:38:10 status half-configured libc-bin:amd64 2.36-9+deb12u3
2023-11-13 15:38:10 status installed libc-bin:amd64 2.36-9+deb12u3


"$TTL 38400	; 10 hours 40 minutes
@		IN SOA	www.tiendaelectronica.int. some.email.address. (
				10000002   ; serial
				10800      ; refresh (3 hours)
				3600       ; retry (1 hour)
				604800     ; expire (1 week)
				38400      ; minimum (10 hours 40 minutes)
				)
@		IN NS	www.tiendaelectronica.int.
ns		IN A		172.16.0.2
test	IN A		172.16.0.4
www		IN A		172.16.0.1
OWNCLOUD IN CNAME	www
alias	IN CNAME	test
texto	IN TXT		"123456ASD""

- Y en zona tambien cambiamos:

zone "tiendaelectronica.int" {
	type master;
	file "/var/lib/bind/db.tiendadeelectronica.int";
	allow-query {
		any;
		};
	};

- docker compose up -d
[+] Running 2/0
 ✔ Container examen_asir2         Created                               0.0s 
 ✔ Container clienteExamen_asir2  Cr...                                 0.0s 
Error response from daemon: Invalid address 127.0.0.1: It does not belong to any of this network's subnets


# Realiza el apartado 9 en la máquina virtual con DNS

- sudo apt-get install bind9
 - Leyendo lista de paquetes... Hecho
 - Creando árbol de dependencias... Hecho
 - Leyendo la información de estado... Hecho

- sudo apt-get install net-tools

-sudo apt-get install dnsutils

Antes de nada debemos configurar los named.conf
haciendo copia y pega de los hechos anteriormente con la tiendaelectronica para que se conecten y poner la maquina en adaptador puente.
- Zona:
    - zone "tiendaelectronica.int" {
	type master;
	file "/var/lib/bind/db.tiendadeelectronica.int";
	allow-query {
		any;
		};
	};

- named.conf:
    - include "/etc/bind/named.conf.options";
    - include "/etc/bind/named.conf.local";

- named.conf.options:
    options {
	directory "/var/cache/bind";

	forwarders {
	 	8.8.8.8;
		1.1.1.1;
	 };
	 forward only;

	listen-on { any; };
	listen-on-v6 { any; };

	allow-query {
		any;
	};
};

 - dig SOA:

; <<>> DiG 9.18.18-0ubuntu0.22.04.1-Ubuntu <<>> SOA
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15806
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;.				IN	NS

;; ANSWER SECTION:
.			7781	IN	NS	c.root-servers.net.
.			7781	IN	NS	i.root-servers.net.
.			7781	IN	NS	j.root-servers.net.
.			7781	IN	NS	f.root-servers.net.
.			7781	IN	NS	l.root-servers.net.
.			7781	IN	NS	b.root-servers.net.
.			7781	IN	NS	g.root-servers.net.
.			7781	IN	NS	m.root-servers.net.
.			7781	IN	NS	d.root-servers.net.
.			7781	IN	NS	k.root-servers.net.
.			7781	IN	NS	h.root-servers.net.
.			7781	IN	NS	a.root-servers.net.
.			7781	IN	NS	e.root-servers.net.

;; Query time: 8 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Mon Nov 13 17:31:55 CET 2023
;; MSG SIZE  rcvd: 239

- dig @172.16.0.1 www.tiendaelectronica.int:

; <<>> DiG 9.18.18-0ubuntu0.22.04.1-Ubuntu <<>> @172.16.0.1 www.tiendaelectronica.int
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 31475
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1220
; COOKIE: 06228e9fb49b2db856e9c2be65524fdeb239ca1cf2e0eb96 (good)
;; QUESTION SECTION:
;www.tiendaelectronica.int.	IN	A

;; AUTHORITY SECTION:
int.			5	IN	SOA	sns.dns.icann.org. noc.dns.icann.org. 2023110905 3600 1800 604800 3600

;; Query time: 4 msec
;; SERVER: 172.16.0.1#53(172.16.0.1) (UDP)
;; WHEN: Mon Nov 13 17:33:34 CET 2023
;; MSG SIZE  rcvd: 139

- systemctl status bind9:
● named.service - BIND Domain Name Server
     Loaded: loaded (/lib/systemd/system/named.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2023-11-13 17:01:51 CET; 38min ago
       Docs: man:named(8)
    Process: 5362 ExecStart=/usr/sbin/named $OPTIONS (code=exited, status=0/SUCCESS)
   Main PID: 5363 (named)
      Tasks: 6 (limit: 9217)
     Memory: 5.6M
        CPU: 65ms
     CGroup: /system.slice/named.service
             └─5363 /usr/sbin/named -u bind

nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './DNSKEY/IN': 2001>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './NS/IN': 2001:500>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './DNSKEY/IN': 2001>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './NS/IN': 2001:500>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './DNSKEY/IN': 2001>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './NS/IN': 2001:500>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './DNSKEY/IN': 2001>
nov 13 17:01:51 javier-VirtualBox named[5363]: network unreachable resolving './NS/IN': 2001:503>
nov 13 17:01:51 javier-VirtualBox named[5363]: managed-keys-zone: Key 20326 for zone . is now tr>
nov 13 17:01:51 javier-VirtualBox named[5363]: resolver priming query complete: success
