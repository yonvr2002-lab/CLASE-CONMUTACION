Packet Tracer - Guía de configuración HSRP
<img width="842" height="612" alt="image" src="https://github.com/user-attachments/assets/af5becc4-3b48-475a-b8a4-e982b6596b46" />


Nota: El router I-Net está presente en la nube de Internet y no se puede acceder en esta actividad.

Objetivos
En esta actividad Packet Tracer, aprenderá a configurar Hot Standby Router Protocol (HSRP) para proporcionar dispositivos de puerta de enlace predeterminados redundantes a hosts en LAN. Después de configurar HSRP, probará la configuración para comprobar que los hosts pueden utilizar la puerta de enlace predeterminada redundante si el dispositivo de puerta de enlace principal no está disponible.

· Configurar un router activo HSRP (HSRP Active).

· Configurar un router en espera HSRP (HSRP Standby).

·         Verificar la operación HSRP.

Antecedentes/Escenario
STP proporciona redundancia sin bucles entre switches dentro de una LAN. Sin embargo, no proporciona puertas de enlace predeterminadas redundantes , para dispositivos de usuario final dentro de la red, si falla un router de puerta de enlace. Los protocolos de redundancia de primer salto (FHRP), proporcionan puertas de enlace predeterminadas redundantes para dispositivos finales sin necesidad de configuración adicional del usuario final. Al usar FHRP, dos o más routers pueden compartir la misma dirección IP virtual y dirección MAC y pueden actuar como un solo router virtual. Los hosts de la red se configuran con una dirección IP compartida como puerta de enlace predeterminada. En esta actividad Packet Tracer, configurará el Protocolo HSRP de Cisco, el cual es un FHRP.

Configurará HSRP en los routers R1 y R3, que sirven como puertas de enlace predeterminadas para los hosts en LAN 1 y LAN 2. Al configurar HSRP, creará una puerta de enlace virtual que utilice la misma dirección de puerta de enlace predeterminada para los hosts de ambas LAN. Si un router de puerta de enlace deja de estar disponible, el segundo router se hará cargo con la misma dirección de puerta de enlace predeterminada que utilizó el primer router. Dado que los hosts de las LAN están configurados con la dirección IP de la puerta de enlace virtual como puerta de enlace predeterminada, los hosts recuperarán la conectividad a las redes remotas después de que HSRP active el router restante.

Instrucciones
Parte 1: Verificar la conectividad
Paso 1: Rastree la ruta de acceso al servidor Web desde PC-A.
a.     Vaya al escritorio de PC-A y abra un símbolo del sistema.

b. Rastree la ruta de acceso desde PC-A al servidor web ejecutando el comando tracert 209.165.200.226.

<img width="868" height="385" alt="Captura de pantalla 2026-03-11 184640" src="https://github.com/user-attachments/assets/e71d9699-7807-4cdb-8e0e-ccb26fdc3dbf" />

Pregunta:
¿Qué dispositivos están en la ruta de acceso desde PC-A al servidor Web? Utilice la tabla de direcciones para determinar los nombres de dispositivos.
<img width="668" height="372" alt="image" src="https://github.com/user-attachments/assets/1e357245-10f6-4c03-913d-3e5763c327f3" />
<img width="674" height="160" alt="image" src="https://github.com/user-attachments/assets/75144aa2-9471-4045-bc10-7d9ee2aaad6c" />

Paso 2: Rastree la ruta de acceso al servidor Web desde PC-B.
Repita el proceso en el paso 1 desde PC-B.

<img width="859" height="469" alt="Captura de pantalla 2026-03-11 184619" src="https://github.com/user-attachments/assets/b4e30f94-e38d-4635-812d-47c5c08f0ac4" />

Pregunta:
¿Qué dispositivos están en la ruta de acceso desde PC-B al servidor Web?
<img width="815" height="580" alt="image" src="https://github.com/user-attachments/assets/59ce4e62-ac43-4332-9e0f-47c6b1eef544" />

Paso 3: Observe el comportamiento de la red cuando R3 deja de estar disponible.
a. Seleccione la herramienta de eliminación de la barra de herramientas Packet Tracer y elimine el vínculo entre R3 y S3.

<img width="1019" height="569" alt="image" src="https://github.com/user-attachments/assets/bcfdb9f8-dfe0-4e53-8105-e08c07adc072" />
b. Abra un símbolo del sistema en PC-B. Ejecute el comando tracert con el servidor Web como destino.

c.     Compare el resultado actual con la salida del comando del Paso 2.

<img width="842" height="591" alt="image" src="https://github.com/user-attachments/assets/776c810f-023b-4d22-96a6-7f5005ac5aa1" />
Pregunta:
¿Cuáles son los resultados?

d.     Haga clic en el icono Connections en la esquina inferior izquierda de la ventana PT . Localice y seleccione el icono Copper Straight-Through en la paleta de tipos de conexión.

e.     Haga clic en S3 y seleccione el puerto GigbitEthernet0/2. Haga clic en R3 y seleccione el puerto GigabitEthernet0/0.
<img width="870" height="445" alt="image" src="https://github.com/user-attachments/assets/85e98fa8-c405-4c4b-b4a9-aa110ad36655" />

f. Después de que las luces de vínculo en la conexión estén verdes, pruebe la conexión haciendo ping al servidor Web. El ping debería ser correcto.
<img width="645" height="217" alt="image" src="https://github.com/user-attachments/assets/b2e1267a-4474-492f-89d7-84f53523e7b5" />

Parte 2: Configurar los routeres activos y en espera HSRP
Paso 1: Configurar HSRP en R1.
a.Configure HSRP en la interfaz LAN G0/1 de R1.

Abra la ventana de configuración

R1(config)# interface g0/1

b.     Especifique el número de versión del protocolo HSRP. La versión más reciente es la versión 2.

Nota: La versión 1 solo admite direccionamiento IPv4.

R1(config-if)# standby version 2

c.     Configure la dirección IP de la puerta de enlace virtual predeterminada. Esta dirección debe configurarse en cualquier host que requiera los servicios de la puerta de enlace predeterminada. Reemplace la dirección de interfaz física del router que se ha configurado previamente en los hosts.

Se pueden configurar varias instancias de HSRP en un router. Debe especificar el número de grupo HSRP para identificar la interfaz virtual entre routers de un grupo HSRP. Este número debe ser coherente entre los routers del grupo. El número de grupo para esta configuración es 1.

R1(config-if)# standby 1 ip 192.168.1.254

d.     Designe el router activo para el grupo HSRP. Es el router que se utilizará como dispositivo de puerta de enlace a menos que falle o que la ruta de acceso se vuelva inactiva o inutilizable. Especifique la prioridad para la interfaz del router. El valor por defecto es 100. Un valor más alto determinará qué router es el router activo. Si las prioridades de los routeres en el grupo HSRP son las mismas, entonces el router con la dirección IP más alta se convertirá en el router activo.

R1(config-if)# standby 1 priority 150

R1 funcionará como el router activo y el tráfico de las dos LAN lo usará como la puerta de enlace predeterminada.

e. Si se desea que el router activo retome el control cuando vuelva a estar disponible, configúrelo para que prefiera el servicio del router en espera. El router activo se hará cargo de la función de puerta de enlace cuando vuelva a funcionar.

R1(config-if)# standby 1 preempt
<img width="862" height="785" alt="image" src="https://github.com/user-attachments/assets/28646416-8b52-4e5d-9647-1d37cac4b4ed" />

Pregunta:
¿Cuál será la prioridad HSRP de R3 cuando se agregue al grupo HSRP 1?
seria 100
<img width="639" height="68" alt="image" src="https://github.com/user-attachments/assets/c015a346-faad-475c-81b1-6992dfe6fe92" />

Paso 2: Configure HSRP en R3.
Configure R3 como el router en espera.

a.     Configure la interfaz R3 que está conectada a LAN 2.

b.     Repita solo los pasos 1b y 1c anteriores.
<img width="546" height="75" alt="image" src="https://github.com/user-attachments/assets/c7a8eed3-a039-45cb-aabf-d3a56280c057" />

Paso 3: Comprobar la configuración HSRP.
a.     Compruebe HSRP ejecutando el comando show standby en R1 y R3. Compruebe los valores del rol HSRP, grupo, dirección IP virtual de la puerta de enlace, preferencia y prioridad. Tenga en cuenta que HSRP también identifica las direcciones IP del router activo y en espera para el grupo.

R1# show standby

GigabitEthernet0/1 - Group 1 (version 2)

State is Active

4 state changes, last state change 00:00:30

Virtual IP address is 192.168.1.254

Active virtual MAC address is 0000.0C9F.F001

Local virtual MAC address is 0000.0C9F.F001 (v2 default)

Hello time 3 sec, hold time 10 sec

Next hello sent in 1.696 secs

Preemption enabled

Active router is local

Standby router is 192.168.1.3

Priority 150 (configured 150)

Group name is "hsrp-Gi0/1-1" (default)

 <img width="532" height="243" alt="image" src="https://github.com/user-attachments/assets/520d0c3b-761f-41f4-b918-b3fcff6463db" />


R3# show standby

GigabitEthernet0/0 - Group 1 (version 2)

State is Standby

4 state changes, last state change 00:02:29

Virtual IP address is 192.168.1.254

Active virtual MAC address is 0000.0C9F.F001

Local virtual MAC address is 0000.0C9F.F001 (v2 default)

Hello time 3 sec, hold time 10 sec

Next hello sent in 0.720 secs

Preemption disabled

Active router is 192.168.1.1

MAC address is d48c.b5ce.a0c1

Standby router is local

Priority 100 (default 100)

Group name is "hsrp-Gi0/0-1" (default)
<img width="515" height="245" alt="image" src="https://github.com/user-attachments/assets/7bd6a21a-934a-4a61-9b03-50bfc38c38b3" />


Utilizando el resultado que se muestra arriba, responda las siguientes preguntas:

Preguntas:
Cuál router es el router activo?
R1
¿ Cuál es la dirección MAC de la dirección IP virtual ?
Active virtual MAC address is 0000.0C9F.F001
¿ Cuál es la dirección IP y la prioridad del router en espera?
Virtual IP address is 192.168.1.254
b. Utilice el comando show standby brief en R1 y R3 para ver un resumen de estado de HSRP. A continuación, se muestra un ejemplo de resultado.

R1# show standby brief
<img width="679" height="91" alt="image" src="https://github.com/user-attachments/assets/e8edec39-9755-4399-8e5a-a7028a98f3d3" />

P indicates configured to preempt.

|

Interface Grp Pri P State Active Standby Virtual IP

Gi0/1 1 150 P Active local 192.168.1.3 192.168.1.254

 

R3# show standby brief
<img width="648" height="97" alt="image" src="https://github.com/user-attachments/assets/fe140d47-39f2-49d0-9eb7-76eb55e31673" />

P indicates configured to preempt.

|

Interface Grp Pri P State Active Standby Virtual IP

Gi0/0 1 100 Standby 192.168.1.1 local 192.168.1.254

c.     Cambie la dirección de gateway predeterminado para la PC-A, la PC-C, el S1 y el S3.

Preguntas:
¿Qué dirección debería utilizar?

Verifique la nueva configuración. Ejecute un ping desde PC-A y PC-C al servidor Web. ¿Los pings son exitosos?

Cierre la ventana de configuración

Parte 3: Observe la operación HSRP
Paso 1: Hacer que el router activo deje de estar disponible.
Abra un símbolo del sistema en PC-B e introduzca el comando tracert 209.165.200.226.

Pregunta:
¿La ruta difiere de la ruta utilizada antes de configurar HSRP?

Paso 2: Rompe el enlace a R1.
a. Seleccione la herramienta de eliminación de la barra de herramientas en Packet Tracer y elimine el cable que conecta R1 a S1.

b.Vuelva inmediatamente a PC-B y ejecute nuevamente el comando tracert 209.165.200.226. Observeel resultado del comando hasta que el comando complete la ejecución. Es posible que tenga que repetir el trace para ver la ruta completa.

Pregunta:
¿En qué se diferenció este trace del anterior?

HSRP se somete a un proceso para determinar qué router debe hacerse cargo, cuando el router activo deje de estar disponible. Este proceso lleva tiempo. Una vez finalizado el proceso, el router en espera R3 se activa y se utiliza como puerta de enlace predeterminada para los hosts de LAN 1 y LAN 2.

Paso 3: Restaurar el enlace a R1.
a.     Vuelva a conectar R1 a S1 con un cable copper straight-through.

b.     Ejecute un trace desde la PC-B al servidor web. Es posible que tenga que repetir el trace para ver la ruta completa.

Preguntas:
¿Qué ruta se utiliza para llegar al servidor web?

Si el comando preempt no se configuró para el grupo HSRP en R1, ¿los resultados habrían sido los mismos?

Fin del documento


