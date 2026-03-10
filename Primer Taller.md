# Título de su Informe

**Autores:** Valentina Diaz, Yon Valdez y Oscar Rada
**Fecha:** 5 de marzo de 2026
**Materia / Proyecto:** Comnutacion y Teletrafico

---
PUNTO 2

Paso 1: Crear una red bridge personalizada en Docker
Primero se crea una red virtual tipo bridge donde se conectarán los contenedores.

Comando utilizado:
docker network create --driver bridge red_arp

Este comando crea una red llamada red_arp, permitiendo que varios contenedores se comuniquen entre sí como si estuvieran en la misma red local.

Paso 2: Crear los contenedores
Luego se crean dos contenedores interactivos utilizando la imagen Alpine Linux, la cual es una imagen muy liviana.

Comandos utilizados:

docker run -it --name contenedor1 --network red_arp alpine sh

En otra terminal:

docker run -it --name contenedor2 --network red_arp alpine sh

Con estos comandos se crean dos contenedores llamados contenedor1 y contenedor2, ambos conectados a la red red_arp.

Paso 3: Instalar herramientas necesarias
Dentro de cada contenedor se instalaron herramientas necesarias para realizar pruebas de red.

apk add arping
apk add iputils

Estas herramientas permiten generar paquetes ARP y realizar pruebas de conectividad con ping.

Paso 4: Preparación de Wireshark
1. Abrir Wireshark con permisos de administrador:
sudo wireshark

2. Seleccionar la interfaz docker0 o la interfaz bridge principal de Docker.

3. Aplicar el filtro:
arp

Esto permitirá observar únicamente los paquetes ARP que circulan por la red.

Paso 5: Obtener las direcciones IP de los contenedores
Dentro de cada contenedor ejecutar:

ip addr show

Ejemplo de direcciones obtenidas:

Contenedor1: 172.17.0.2
Contenedor2: 172.17.0.3

Paso 6: Limpiar la caché ARP
Antes de iniciar la prueba se limpia la caché ARP del host para forzar una nueva resolución.

sudo ip neigh flush all

Paso 7: Iniciar captura en Wireshark
En Wireshark iniciar la captura con el filtro:

arp

De esta forma solo se capturarán los paquetes relacionados con el protocolo ARP.

Paso 8: Realizar comunicación entre contenedores
Desde contenedor1 ejecutar:

ping -c 3 172.17.0.3

Cuando se ejecuta este comando:

1. Contenedor1 necesita conocer la MAC de contenedor2.
2. Envía una solicitud ARP (ARP Request).
3. Contenedor2 responde con un ARP Reply indicando su dirección MAC.
Análisis de los paquetes ARP
En Wireshark se observan dos tipos de mensajes:

ARP Request:
Mensaje enviado en broadcast preguntando quién tiene la IP 172.17.0.3.

ARP Reply:
Respuesta enviada por contenedor2 indicando su dirección MAC.

Análisis – Protocolo ARP (Respuestas)

1. ¿Quién envía la trama ARP? (Dirección MAC de origen y destino)
La trama ARP es enviada por contenedor1, ya que es el dispositivo que necesita conocer la dirección MAC del otro contenedor para poder comunicarse.

MAC de origen: corresponde a la dirección MAC de contenedor1.
MAC de destino: ff:ff:ff:ff:ff:ff, que es una dirección broadcast. Esto significa que el mensaje se envía a todos los dispositivos de la red para preguntar quién tiene la IP solicitada.

2. Solicitud ARP: “Who has 172.17.0.3? Tell 172.17.0.2”. ¿A qué dirección MAC va dirigido?
La solicitud ARP se envía a la dirección MAC:

ff:ff:ff:ff:ff:ff

Esta es una dirección broadcast, por lo que el mensaje es recibido por todos los dispositivos de la red. El dispositivo que tenga la dirección IP 172.17.0.3 será el encargado de responder.

3. Respuesta ARP: “172.17.0.3 is at <MAC_del_contenedor2>”. ¿Es broadcast o unicast?
La respuesta ARP es un mensaje unicast.

Esto significa que la respuesta se envía directamente al dispositivo que realizó la solicitud, en este caso contenedor1.

Origen: contenedor2
Destino: contenedor1

4. ¿Por qué es necesario ARP? ¿Qué información se almacena en la caché ARP?
El protocolo ARP es necesario porque permite relacionar una dirección IP con una dirección MAC dentro de una red local.

Cuando un dispositivo quiere comunicarse con otro dentro de la misma red, necesita conocer su dirección MAC para poder enviar los paquetes correctamente.

Después de realizar el intercambio ARP, se guarda en la caché ARP una tabla con la relación entre:

• Dirección IP
• Dirección MAC

Por ejemplo:

IP: 172.17.0.3
MAC: MAC del contenedor2

Esto permite que futuras comunicaciones se realicen más rápido sin necesidad de volver a enviar una solicitud ARP.



