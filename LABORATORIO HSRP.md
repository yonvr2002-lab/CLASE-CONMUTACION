# Packet Tracer - Guía de configuración HSRP

<p align="center">
  <img src="https://img.shields.io/badge/Estado-Completado-success">
  <img src="https://img.shields.io/badge/Herramienta-Cisco%20Packet%20Tracer-blue">
  <img src="https://img.shields.io/badge/Protocolo-HSRP-orange">
  <img src="https://img.shields.io/badge/Tipo-Redes-lightgrey">
</p>

<p align="center">
  <img width="842" height="612" alt="topologia" src="https://github.com/user-attachments/assets/af5becc4-3b48-475a-b8a4-e982b6596b46" />
</p>

---

## Nota

El router **I-Net** está presente en la nube de Internet y no se puede acceder en esta actividad.

---

## Objetivos

- Configurar un router activo HSRP (HSRP Active).
- Configurar un router en espera HSRP (HSRP Standby).
- Verificar la operación HSRP.

---

## Antecedentes / Escenario

STP proporciona redundancia sin bucles entre switches dentro de una LAN. Sin embargo, no proporciona puertas de enlace predeterminadas redundantes para dispositivos finales si falla un router.

Los protocolos FHRP permiten solucionar este problema mediante:
- Direcciones IP virtuales compartidas
- Direcciones MAC virtuales
- Funcionamiento como un único router lógico

En esta práctica se implementa HSRP en los routers R1 y R3 para proporcionar alta disponibilidad en las LAN.

---

# Parte 1: Verificar la conectividad

## Paso 1: Tracert desde PC-A

```bash
tracert 209.165.200.226
```

<p align="center">
  <img width="868" src="https://github.com/user-attachments/assets/e71d9699-7807-4cdb-8e0e-ccb26fdc3dbf" />
</p>

### Pregunta
¿Qué dispositivos están en la ruta?

<p align="center">
  <img width="668" src="https://github.com/user-attachments/assets/1e357245-10f6-4c03-913d-3e5763c327f3" />
</p>

<p align="center">
  <img width="674" src="https://github.com/user-attachments/assets/75144aa2-9471-4045-bc10-7d9ee2aaad6c" />
</p>

---

## Paso 2: Tracert desde PC-B

<p align="center">
  <img width="859" src="https://github.com/user-attachments/assets/b4e30f94-e38d-4635-812d-47c5c08f0ac4" />
</p>

### Pregunta
¿Qué dispositivos están en la ruta?

<p align="center">
  <img width="815" src="https://github.com/user-attachments/assets/59ce4e62-ac43-4332-9e0f-47c6b1eef544" />
</p>

---

## Paso 3: Fallo de R3

Elimine el enlace entre R3 y S3:

<p align="center">
  <img width="1019" src="https://github.com/user-attachments/assets/bcfdb9f8-dfe0-4e53-8105-e08c07adc072" />
</p>

Ejecute:

```bash
tracert 209.165.200.226
```

<p align="center">
  <img width="842" src="https://github.com/user-attachments/assets/776c810f-023b-4d22-96a6-7f5005ac5aa1" />
</p>

### Resultado
Se observa pérdida o cambio en la conectividad.

---

### Restauración

<p align="center">
  <img width="870" src="https://github.com/user-attachments/assets/85e98fa8-c405-4c4b-b4a9-aa110ad36655" />
</p>

<p align="center">
  <img width="645" src="https://github.com/user-attachments/assets/b2e1267a-4474-492f-89d7-84f53523e7b5" />
</p>

---

# Parte 2: Configuración HSRP

## Paso 1: Configurar R1 (Activo)

```bash
interface g0/1
standby version 2
standby 1 ip 192.168.1.254
standby 1 priority 150
standby 1 preempt
```

<p align="center">
  <img width="862" src="https://github.com/user-attachments/assets/28646416-8b52-4e5d-9647-1d37cac4b4ed" />
</p>

### Pregunta
¿Cuál será la prioridad de R3?

Respuesta: 100

<p align="center">
  <img width="639" src="https://github.com/user-attachments/assets/c015a346-faad-475c-81b1-6992dfe6fe92" />
</p>

---

## Paso 2: Configurar R3 (Standby)

<p align="center">
  <img width="546" src="https://github.com/user-attachments/assets/c7a8eed3-a039-45cb-aabf-d3a56280c057" />
</p>

```bash
standby version 2
standby 1 ip 192.168.1.254
```

---

## Paso 3: Verificación

```bash
show standby
```

### R1

<p align="center">
  <img width="532" src="https://github.com/user-attachments/assets/520d0c3b-761f-41f4-b918-b3fcff6463db" />
</p>

### R3

<p align="center">
  <img width="515" src="https://github.com/user-attachments/assets/7bd6a21a-934a-4a61-9b03-50bfc38c38b3" />
</p>

---

## Resultados

| Parámetro | Valor |
|----------|------|
| Router activo | R1 |
| Router en espera | R3 |
| IP virtual | 192.168.1.254 |
| MAC virtual | 0000.0C9F.F001 |
| Prioridad standby | 100 |

---

## Resumen de estado

### R1
<p align="center">
  <img width="679" src="https://github.com/user-attachments/assets/e8edec39-9755-4399-8e5a-a7028a98f3d3" />
</p>

### R3
<p align="center">
  <img width="648" src="https://github.com/user-attachments/assets/fe140d47-39f2-49d0-9eb7-76eb55e31673" />
</p>

---

## Gateway configurado

```bash
192.168.1.254
```

Verificación:
- Ping desde PC-A y PC-C
- Resultado: exitoso

---

# Parte 3: Operación HSRP

## Paso 1

```bash
tracert 209.165.200.226
```

Pregunta:  
¿La ruta cambia respecto a la anterior?

---

## Paso 2: Falla del router activo

Se elimina el enlace R1-S1.

Resultado:
- Cambio de ruta
- R3 asume el rol activo

---

## Paso 3: Restauración

Se reconecta R1.

Resultados:
- La ruta vuelve a R1 (por preempt)

Pregunta:
¿Qué ocurriría sin preempt?

Respuesta:
R3 permanecería como router activo.

---

# Conclusión

HSRP permite garantizar alta disponibilidad en la red mediante:

- Redundancia de gateway
- Conmutación automática ante fallos
- Transparencia para el usuario final

---

# Autor

Práctica de redes realizada en Cisco Packet Tracer


