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

# Realiza el apartado 9 en la máquina virtual con DNS

