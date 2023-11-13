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
        ipv4_address: 172.22.5.1
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
  cliente:
    container_name: clienteEx_asir2
    image: alpine
    
    tty: true
    stdin_open: true

    networks:
      bind9_subnet:
        ipv4_address: 172.22.5.2
    
networks:
  bind9_subnet:
    external: true

# ¿Qué hay que añadir al fichero anterior para que un contenedor tenga la IP fija?

Para esto como podemos ver en la anterior pregunta dentro de networks usamos ipv4_adress: (La ip fija que queremos que tenga).

networks:
      bind9_subnet:
        ipv4_address: 172.22.5.2

# ¿Que comando de consola puedo usar para saber las ips de los contenedores anteriores? Filtra todo lo que puedas la salida.

- 

# ¿Cual es la funcionalidad del apartado "ports" en docker compose?


# ¿Para que sirve el registro CNAME? Pon un ejemplo


# ¿Como puedo hacer para que la configuración de un contenedor DNS no se borre si creo otro contenedor?


# Añade una zona tiendadeelectronica.int en tu docker DNS que tenga

- www a la IP 172.16.0.1

- owncloud sea un CNAME de www

- un registro de texto con el contenido "1234ASDF"

- Comprueba que todo funciona con el comando "dig"

- Muestra en los logs que el servicio arranca correctamente


# Realiza el apartado 9 en la máquina virtual con DNS