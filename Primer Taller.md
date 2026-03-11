
# 📡 Informe de Laboratorio

## Docker, ROS, ARP y Análisis de Red

👩‍💻 **Autores:** Valentina Diaz, Yon Valdez y Oscar Rada
📅 **Fecha:** 5 de marzo de 2026
📚 **Materia:** Conmutación y Teletráfico

---

# 📑 Tabla de Contenido

* Punto 1 — Simulación con Docker y ROS
* Punto 2 — Análisis del Protocolo ARP
* Punto 3 — Latencia y Jitter

---

# 🧪 PUNTO 1 — Simulación con Docker y ROS

## ▶️ Ejecución de video ASCII en Docker

```bash
docker run --rm -it wernight/funbox cvlc --no-audio -V caca /examples/countdown.mp4
```

<img width="921" height="274" alt="image" src="https://github.com/user-attachments/assets/a8266609-62ed-46aa-ba74-d11c866d6e41" />

### ¿Qué sucede?

| Parámetro               | Función                                              |
| ----------------------- | ---------------------------------------------------- |
| docker run              | Ejecuta un contenedor                                |
| --rm                    | Elimina el contenedor automáticamente cuando termina |
| -it                     | Permite usar la terminal de forma interactiva        |
| wernight/funbox         | Imagen de Docker con varias herramientas             |
| cvlc                    | Versión de VLC para línea de comandos                |
| --no-audio              | Reproduce el video sin sonido                        |
| -V caca                 | Usa el driver de video ASCII                         |
| /examples/countdown.mp4 | Video de ejemplo dentro del contenedor               |

✅ **Resultado**

En la terminal aparece un video de cuenta regresiva reproducido con caracteres ASCII (letras y símbolos), simulando una animación hecha con texto.

<img width="921" height="508" alt="image" src="https://github.com/user-attachments/assets/ea218fc3-97b2-4a47-a6fc-bcce0c641396" />

---

# 🤖 Descarga de ROS con Docker

```bash
docker pull osrf/ros:noetic-desktop-full
```

<img width="921" height="503" alt="image" src="https://github.com/user-attachments/assets/c6b14b8f-5934-49a2-8788-42cd056a36a2" />

### Explicación

| Elemento            | Descripción                                |
| ------------------- | ------------------------------------------ |
| docker pull         | Descarga una imagen desde Docker Hub       |
| osrf/ros            | Imagen oficial de ROS                      |
| noetic-desktop-full | Versión completa con herramientas gráficas |

✅ **Resultado**

Se descarga una imagen de Docker que contiene ROS Noetic con herramientas completas para robótica, incluyendo integración con Gazebo para simular robots en entornos virtuales.

---

# 🐢 Creación del Dockerfile para ROS

En este paso se crea un archivo **Dockerfile-ros** que utiliza la imagen base de **ROS Noetic**.
Este archivo instala los paquetes necesarios para **TurtleBot3**, configura el modelo **Burger** y ejecuta ROS junto con Gazebo para iniciar una simulación.

<img width="921" height="506" alt="image" src="https://github.com/user-attachments/assets/4e88af04-0811-4731-aec9-2c04969bbd53" />

<img width="1360" height="404" alt="image" src="https://github.com/user-attachments/assets/a457bbda-a5ee-44a4-8636-26c192e399e7" />

<img width="1365" height="721" alt="image" src="https://github.com/user-attachments/assets/3dca498a-df41-46d1-b7cf-4403ffd3dead" />

---

# 🏗 Construcción de la imagen Docker

```bash
docker build -t ros-gazebo -f Dockerfile-ros .
```

Este comando construye una imagen Docker usando el archivo **Dockerfile-ros** y la guarda con el nombre **ros-gazebo**.

---

# ▶️ Ejecución del contenedor

```bash
docker run -it --rm \
--env="DISPLAY" \
--volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
ros-gazebo
```

### Explicación

| Parámetro | Función                              |
| --------- | ------------------------------------ |
| DISPLAY   | Permite usar la interfaz gráfica     |
| X11       | Comparte el sistema gráfico de Linux |
| --rm      | Elimina el contenedor al cerrarse    |

<img width="1341" height="681" alt="image" src="https://github.com/user-attachments/assets/752974f1-9094-4473-be19-a6cae1178af4" />

---

# 🧠 Simulación con SLAM y LIDAR

En este paso se crea un Dockerfile que prepara un entorno de **ROS Noetic con TurtleBot3, LIDAR y SLAM**.

Se instalan paquetes como:

* turtlebot3
* turtlebot3-simulations
* slam-gmapping

Luego se configura el modelo **TurtleBot3 Burger**.

✅ **Resultado**

Cuando se ejecuta el contenedor se abre **Gazebo con el robot TurtleBot3**, permitiendo usar SLAM para generar un mapa del entorno en tiempo real.

<img width="1186" height="657" alt="image" src="https://github.com/user-attachments/assets/4a807a0f-cf75-415f-8217-136f57338efe" />

<img width="1305" height="406" alt="image" src="https://github.com/user-attachments/assets/76e305ac-e029-495d-bb72-de1febf5c4c6" />

---

# 🎮 Control del robot

En nuevas terminales del contenedor se ejecutan:

* SLAM (gmapping) para generar el mapa
* Teleoperación para controlar el robot con el teclado

✅ **Resultado**

El robot se puede mover en la simulación mientras el sistema SLAM genera un mapa del entorno.

---

# 📊 Visualización con RViz

RViz permite visualizar:

* Datos del sensor LIDAR
* Movimiento del robot
* Construcción del mapa

✅ **Resultado**

Se puede observar el mapa formándose en tiempo real mientras el robot explora el entorno.

---

🎥 **Gif demostración**

https://github.com/user-attachments/assets/867a8301-464d-4861-a8fd-558c16ec2516

---

# 🌐 PUNTO 2 — Análisis del Protocolo ARP

## ¿Qué vamos a demostrar?

Cuando un dispositivo quiere comunicarse con otro dentro de la misma red:

1. Conoce la dirección **IP** del otro dispositivo.
2. No conoce su **dirección MAC**.
3. Utiliza **ARP (Address Resolution Protocol)** para descubrirla.

El dispositivo envía un mensaje preguntando:

“¿Quién tiene esta IP?”

El dispositivo con esa IP responde indicando su dirección MAC.

Esto se observará utilizando **Wireshark**.

---

# Requisitos

Se debe tener instalado:

* Docker
* Wireshark
* Linux o WSL

Verificación:

```bash
docker --version
```

```bash
wireshark
```

---

# Crear red Docker

```bash
docker network create --driver bridge red_arp
```

<img width="921" height="946" alt="image" src="https://github.com/user-attachments/assets/3749bfad-b649-4105-aa05-b17cd7f173c4" />

Este comando crea una red virtual tipo **bridge** llamada **red_arp** donde se conectarán los contenedores.

---

# Crear contenedores

Contenedor 1:

```bash
docker run -it --name contenedor1 --network red_arp alpine sh
```

Contenedor 2:

```bash
docker run -it --name contenedor2 --network red_arp alpine sh
```

<img width="921" height="733" alt="image" src="https://github.com/user-attachments/assets/e0ec4a24-4f1b-46b3-9dff-5a769105cd30" />

Ahora se tendrán dos terminales abiertas:

Terminal 1 → contenedor1
Terminal 2 → contenedor2

---

# Instalar herramientas

Dentro de cada contenedor ejecutar:

```bash
apk add arping
```

```bash
apk add iputils
```

| Herramienta | Uso                 |
| ----------- | ------------------- |
| arping      | enviar paquetes ARP |
| ping        | probar conectividad |

---

# Obtener direcciones IP

```bash
ip addr show
```

Ejemplo:

| Contenedor  | IP         |
| ----------- | ---------- |
| contenedor1 | 172.18.0.2 |
| contenedor2 | 172.18.0.3 |

---

# Limpiar caché ARP

```bash
sudo ip neigh flush all
```

---

# Preparar Wireshark

Abrir Wireshark como administrador:

```bash
sudo wireshark
```

Aplicar el filtro:

```
arp
```

<img width="921" height="481" alt="image" src="https://github.com/user-attachments/assets/b37ad1eb-7d3a-41ff-83c4-b02a0ce9a0de" />

---

# Análisis del protocolo ARP

### ¿Quién envía la trama ARP?

La envía **contenedor1**, porque necesita conocer la MAC de contenedor2.

MAC destino:

```
ff:ff:ff:ff:ff:ff
```

Esto corresponde a **broadcast**.

---

### ¿El ARP Reply es broadcast o unicast?

El **ARP Reply es unicast**, porque contenedor2 responde directamente al contenedor1.

---

### ¿Por qué es necesario ARP?

Porque las aplicaciones usan direcciones **IP**, pero los dispositivos de red envían datos utilizando direcciones **MAC**.

ARP permite traducir:

```
IP → MAC
```

---

# Caché ARP

Después del intercambio se guarda una tabla como:

| IP         | MAC               |
| ---------- | ----------------- |
| 172.18.0.3 | 02:42:ac:12:00:03 |

Ver tabla con:

```bash
arp -a
```

o

```bash
ip neigh
```

---

# 📊 PUNTO 3 — Latencia y Jitter

## Preparación

Abrir Wireshark y aplicar filtro:

```
icmp
```

---

# Prueba sin perturbación

Desde contenedor1 ejecutar:

```bash
ping -c 10 172.17.0.3
```

Esto genera paquetes:

* Echo Request
* Echo Reply

Permitiendo medir el **RTT (Round Trip Time)**.

---

# Introducir jitter

En el host ejecutar:

```bash
sudo tc qdisc add dev docker0 root netem delay 50ms 20ms distribution normal
```

Esto añade:

* 50 ms de latencia
* 20 ms de variación en el tráfico

---

# Prueba con perturbación

```bash
ping -c 20 172.17.0.3
```

Ahora los tiempos de respuesta tendrán mayor variación.

---

# Eliminar configuración

```bash
sudo tc qdisc del dev docker0 root
```

---

# Análisis

### Comparación de RTT

| Escenario        | Resultado       |
| ---------------- | --------------- |
| Sin perturbación | RTT estable     |
| Con jitter       | mayor variación |

---

# Conceptos

### Latencia

La latencia es el tiempo que tarda un paquete en viajar desde el origen hasta el destino y regresar.

Puede ser causada por:

* distancia entre dispositivos
* congestión de red
* procesamiento en routers
* calidad de la conexión

---

### Jitter

El jitter es la variación en el tiempo de llegada de los paquetes.

Es especialmente importante en aplicaciones como:

* VoIP
* videollamadas
* juegos en línea

Si el jitter es alto puede causar cortes, retrasos o pérdida de calidad en audio y video.

---

# Conclusión

En este laboratorio se logró:

* Utilizar **Docker para simulaciones de red**
* Implementar **ROS y Gazebo para robótica**
* Analizar el **protocolo ARP con Wireshark**
* Evaluar **latencia y jitter en redes**

Estos experimentos permiten comprender mejor cómo se comunican los dispositivos dentro de una red y cómo factores como la latencia y el jitter pueden afectar el rendimiento de las aplicaciones.



