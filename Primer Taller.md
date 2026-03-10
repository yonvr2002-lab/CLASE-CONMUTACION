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

PUNTO 3

Escenario
Se utilizará la misma red y contenedores creados en el ejercicio anterior:

Red Docker: red_arp
Contenedores: contenedor1 y contenedor2

Ambos deben poder comunicarse entre sí mediante el comando ping.
Preparación de Wireshark
1. Abrir Wireshark con permisos de administrador.
2. Seleccionar la interfaz llamada docker0.
3. Aplicar el filtro de captura:

icmp

Esto permitirá observar únicamente los paquetes ICMP generados por el comando ping.
Procedimiento sin perturbación
1. Iniciar la captura en Wireshark.
2. Desde contenedor1 ejecutar el siguiente comando:

ping -c 10 172.17.0.3

3. Este comando envía 10 paquetes ICMP al contenedor2.
4. Cada solicitud enviada se llama Echo Request y cada respuesta recibida Echo Reply.
5. Detener la captura después de que finalicen los 10 paquetes.
Análisis de paquetes ICMP
En Wireshark se pueden observar los paquetes ICMP capturados.

Cada paquete tiene un número de secuencia y un tiempo de respuesta. 
El tiempo entre el Echo Request y el Echo Reply corresponde al RTT (Round Trip Time).

Esta información se puede observar directamente en la columna llamada "Time" dentro de Wireshark.

A partir de estos valores se puede calcular el RTT promedio y verificar si existe variación entre los tiempos de respuesta.
Introducción de jitter (perturbación)
Para simular variaciones en la red se introduce un retraso artificial usando la herramienta tc.

En el host ejecutar:

sudo tc qdisc add dev docker0 root netem delay 50ms 20ms distribution normal

Este comando añade:
- Un retardo promedio de 50 ms
- Una variación de 20 ms (jitter) en el tráfico que pasa por la interfaz docker0.
Prueba con perturbación
Después de aplicar la perturbación se vuelve a ejecutar la prueba de conectividad.

Desde contenedor1 ejecutar:

ping -c 20 172.17.0.3

Mientras se ejecuta el ping se debe iniciar nuevamente la captura en Wireshark para observar los nuevos paquetes ICMP con variación en los tiempos de respuesta.
Eliminar la configuración de jitter
Después de terminar la prueba se debe eliminar la configuración aplicada con tc ejecutando:

sudo tc qdisc del dev docker0 root
Análisis y respuestas
1. Comparar los RTT de las dos capturas

En la primera captura (sin perturbación) los valores de RTT son más estables y tienen poca variación.

En la segunda captura, después de aplicar el comando tc, se observa una mayor variabilidad en los tiempos de respuesta. Esto ocurre porque se introdujo un retraso artificial con jitter, lo que provoca que cada paquete tenga un tiempo diferente.

Por lo tanto, sí se observa una mayor variabilidad en la segunda captura.

2. Cálculo del jitter

El jitter se puede calcular como la diferencia entre los tiempos RTT consecutivos.

Por ejemplo:
RTT1 = 1.2 ms
RTT2 = 1.8 ms

Jitter = |RTT2 - RTT1| = 0.6 ms

Esto permite medir cuánto varía el tiempo de respuesta entre paquetes consecutivos.

3. Explicación de latencia y jitter

Latencia:
La latencia es el tiempo que tarda un paquete en viajar desde el origen hasta el destino y regresar nuevamente. En redes se mide normalmente usando el RTT obtenido con el comando ping.

Factores que pueden causar latencia:
- Distancia entre dispositivos
- Congestión de red
- Procesamiento en routers o switches
- Calidad de la conexión

Jitter:
El jitter es la variación en el tiempo de llegada de los paquetes. Esto significa que algunos paquetes pueden tardar más o menos tiempo que otros.

El jitter es especialmente importante en aplicaciones en tiempo real como:
- VoIP (llamadas por internet)
- Videollamadas
- Juegos en línea

Si el jitter es muy alto, la comunicación puede presentar cortes, retrasos o pérdida de calidad en audio y video.




