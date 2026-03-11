# Título de su Informe

**Autores:** Valentina Diaz, Yon Valdez y Oscar Rada

**Fecha:** 5 de marzo de 2026

**Materia / Proyecto:** Comnutacion y Teletrafico

---
<img width="921" height="274" alt="image" src="https://github.com/user-attachments/assets/a8266609-62ed-46aa-ba74-d11c866d6e41" />
<img width="921" height="508" alt="image" src="https://github.com/user-attachments/assets/ea218fc3-97b2-4a47-a6fc-bcce0c641396" />
<img width="921" height="503" alt="image" src="https://github.com/user-attachments/assets/c6b14b8f-5934-49a2-8788-42cd056a36a2" />
<img width="921" height="506" alt="image" src="https://github.com/user-attachments/assets/4e88af04-0811-4731-aec9-2c04969bbd53" />
<img width="1360" height="404" alt="image" src="https://github.com/user-attachments/assets/a457bbda-a5ee-44a4-8636-26c192e399e7" />
<img width="1365" height="721" alt="image" src="https://github.com/user-attachments/assets/3dca498a-df41-46d1-b7cf-4403ffd3dead" />
<img width="1341" height="681" alt="image" src="https://github.com/user-attachments/assets/752974f1-9094-4473-be19-a6cae1178af4" />
<img width="1186" height="657" alt="image" src="https://github.com/user-attachments/assets/4a807a0f-cf75-415f-8217-136f57338efe" />
<img width="1305" height="406" alt="image" src="https://github.com/user-attachments/assets/76e305ac-e029-495d-bb72-de1febf5c4c6" />

Gif 1 punto 


https://github.com/user-attachments/assets/867a8301-464d-4861-a8fd-558c16ec2516



# PUNTO 2

---

##  1. ¿Qué vamos a demostrar?

Cuando un dispositivo quiere comunicarse con otro en la misma red:

1️. Conoce la **IP** del otro dispositivo

2️. Pero **no conoce su dirección MAC**

3️. Entonces usa **ARP (Address Resolution Protocol)** para preguntarle a la red:

> “¿Quién tiene esta IP?”

El dispositivo con esa IP responde:

> “Yo tengo esa IP, mi MAC es esta”.

Esto es exactamente lo que vamos a **ver en Wireshark**.

---

## 2. Antes de empezar

Se Debe tener instalado:

* **Docker**
* **Wireshark**
* **Linux o WSL**

Verifica Docker:

```bash
docker --version
```

Verifica Wireshark:

```bash
wireshark
```

---

## 3. Crear la red Docker

Primero crearemos una red virtual donde estarán los contenedores.

Ejecuta en la terminal:

```bash
docker network create --driver bridge red_arp
```
<img width="921" height="946" alt="image" src="https://github.com/user-attachments/assets/3749bfad-b649-4105-aa05-b17cd7f173c4" />


### ¿Qué hace esto?

* crea una red virtual
* tipo **bridge**
* llamada **red_arp**

Docker conectará los contenedores a esta red.

---

## 4. Crear los contenedores

Ahora crearemos **dos computadores virtuales**.

## Contenedor 1

```bash
docker run -it --name contenedor1 --network red_arp alpine sh
```

Esto hace:

| Parte              | Significado           |
| ------------------ | --------------------- |
| docker run         | crear contenedor      |
| -it                | interactivo           |
| --name contenedor1 | nombre                |
| --network red_arp  | conectarlo a la red   |
| alpine             | sistema Linux pequeño |
| sh                 | abrir terminal        |

Si no tienes Alpine:

**Docker lo descargará automáticamente.**

---

## Contenedor 2

Abre **otra terminal nueva** y ejecuta:

```bash
docker run -it --name contenedor2 --network red_arp alpine sh
```

<img width="921" height="733" alt="image" src="https://github.com/user-attachments/assets/e0ec4a24-4f1b-46b3-9dff-5a769105cd30" />


Ahora tendrás **dos terminales abiertas**:

Terminal 1 → contenedor1
Terminal 2 → contenedor2

---

## 5. Instalar herramientas dentro de los contenedores

Dentro de **cada contenedor** instala herramientas.

Ejecuta:

```bash
apk add arping
```

y luego:

```bash
apk add iputils
```

<img width="921" height="946" alt="image" src="https://github.com/user-attachments/assets/2d78b7eb-e43e-4ae2-b8c8-88a3728b85f0" />


Esto instala:

| herramienta | para qué sirve      |
| ----------- | ------------------- |
| arping      | enviar paquetes ARP |
| ping        | probar conectividad |

Hazlo **en ambos contenedores**.

---

## 6. Ver la IP de cada contenedor

En **contenedor1** escribe:

```bash
ip addr show
```

Verás algo como:

```
inet 172.18.0.2/16
```

---

Ahora en **contenedor2**:

```bash
ip addr show
```

Verás algo como:

```
inet 172.18.0.3/16
```

Ejemplo típico:

| contenedor  | IP         |
| ----------- | ---------- |
| contenedor1 | 172.18.0.2 |
| contenedor2 | 172.18.0.3 |

---

## 7. Limpiar la caché ARP

Antes de probar debemos **borrar la caché ARP** para forzar la consulta.

En el **host (tu computadora)** ejecuta:

```bash
sudo ip neigh flush all
```

Esto elimina registros ARP guardados.

---

## 8. Preparar Wireshark

Abre Wireshark como administrador:

```bash
sudo wireshark
```

<img width="921" height="481" alt="image" src="https://github.com/user-attachments/assets/b37ad1eb-7d3a-41ff-83c4-b02a0ce9a0de" />


## ¿Quién envía la trama ARP?

La envía **contenedor1**, porque es quien necesita conocer la MAC.

Ejemplo:

| campo       | valor             |
| ----------- | ----------------- |
| IP origen   | 172.18.0.2        |
| MAC origen  | MAC contenedor1   |
| MAC destino | ff:ff:ff:ff:ff:ff |

---

## ¿A qué MAC va dirigido el ARP Request?

Va dirigido a:

```
ff:ff:ff:ff:ff:ff
```

Esto es **broadcast**.

Significa que **todos los dispositivos de la red reciben el mensaje**.

---

## ¿El ARP Reply es broadcast o unicast?

El **ARP Reply es unicast**.

Porque contenedor2 responde **solo al contenedor1**.

Destino:

```
MAC de contenedor1
```

---

## ¿Por qué es necesario ARP?

Porque en una red:

* los paquetes IP necesitan enviarse usando **direcciones MAC**
* pero las aplicaciones solo conocen **direcciones IP**

Entonces ARP hace la traducción:

```
IP  →  MAC
```

---

## ¿Qué se guarda en la caché ARP?

Después del intercambio se guarda una tabla como esta:

| IP         | MAC               |
| ---------- | ----------------- |
| 172.18.0.3 | 02:42:ac:12:00:03 |

Esto permite que en el futuro **no tenga que preguntar otra vez**.

---

Puedes verla con:

```bash
arp -a
```

o

```bash
ip neigh
```

---

## Resumen punto 2 
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

# PUNTO 3

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




