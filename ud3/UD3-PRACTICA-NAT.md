# PRÁCTICA DE ENRUTAMIENTO (IP FORWARDING) Y NAT CON IPTABLES

**Curso Académico:** 2026-27  
**Profesor:** Paco ldarias  
**Fecha:** 2-11-2026  
**Módulo:** Servicios de Red e Internet (SRI) - 2º ASIR  

---

## PARTE 1: ENUNCIADO DE LA PRÁCTICA

### 1. Objetivos

* Configurar las tarjetas de red de un servidor Linux Ubuntu para interconectar dos redes diferentemente segmentadas (WAN y LAN).
* Habilitar el reenvío de paquetes en el Kernel del sistema operativo (`ip_forward`).
* Crear y verificar reglas de enmascaramiento de red (NAT) mediante `iptables`.
* Garantizar la persistencia de la configuración tras el reinicio de las máquinas.
* Verificar la conectividad desde un equipo cliente hacia la red externa.

---

### 2. Escenario de Red

Se utilizarán dos máquinas virtuales en VirtualBox:

1. **`Router-Ubuntu` (Servidor/Router):**
   * **Tarjeta 1 (`enp0s3`):** Conectada a la red **NAT** de VirtualBox (Simula la conexión WAN / Internet). Obtendrá IP mediante DHCP de VirtualBox.
   * **Tarjeta 2 (`enp0s8`):** Conectada a la **Red Interna** (`red-lan`). IP estática asignada: `192.168.20.1/24`.

2. **`Cliente-Linux` (Equipo de la LAN):**
   * **Tarjeta 1 (`enp0s3`):** Conectada a la **Red Interna** (`red-lan`). IP estática asignada: `192.168.20.10/24`.

---

### 3. Tareas a Realizar

#### Tarea 1: Configuración de Direccionamiento de Red
1. En el servidor `Router-Ubuntu`, configura la interfaz `enp0s8` para que tenga la dirección fija `192.168.20.1/24` mediante Netplan.
2. En el cliente `Cliente-Linux`, configura la interfaz de red con la IP `192.168.20.10/24`, definiendo como puerta de enlace (Gateway) la IP `192.168.20.1` y servidor DNS `8.8.8.8`.

#### Tarea 2: Habilitar IP Forwarding
1. Comprueba si el servidor tiene activado el reenviador de paquetes.
2. Actívalo de forma permanente editando el archivo `/etc/sysctl.conf`.

#### Tarea 3: Configuración de NAT con IPTables
1. Añade la regla necesaria en `iptables` en la tabla `nat` y cadena `POSTROUTING` para realizar enmascaramiento (`MASQUERADE`) de los paquetes que salgan por la interfaz WAN (`enp0s3`).
2. Instala la herramienta `iptables-persistent` para guardar la regla y asegurar que permanezca tras un reinicio del sistema.

#### Tarea 4: Comprobación y Verificación
1. Desde el cliente `Cliente-Linux`, realiza un `ping` a la puerta de enlace (`192.168.20.1`).
2. Realiza un `ping` a la IP pública `8.8.8.8` para verificar el enrutamiento y la salida por NAT.
3. Realiza una traza de ruta con `traceroute 8.8.8.8` para comprobar que el primer salto es el servidor `Router-Ubuntu`.

---

## PARTE 2: GUÍA DE SOLUCIÓN PASO A PASO

### Solución Tarea 1: Configuración de Interfaces de Red

#### En el Servidor (`Router-Ubuntu`):
Edita el archivo de red `/etc/netplan/01-netcfg.yaml`:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Añade o edita el siguiente contenido:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.20.1/24
```

Aplica los cambios:

```bash
sudo netplan apply
```

#### En el Cliente (`Cliente-Linux`):
Edita el archivo de red `/etc/netplan/01-netcfg.yaml`:

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Añade el siguiente contenido:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.20.10/24
      routes:
        - to: default
          via: 192.168.20.1
      nameservers:
        addresses:
          - 8.8.8.8
```

Aplica los cambios:

```bash
sudo netplan apply
```

---

### Solución Tarea 2: Activar IP Forwarding en el Servidor

1. Edita el archivo de parámetros del kernel:

```bash
sudo nano /etc/sysctl.conf
```

2. Busca y descomenta (o añade al final) la siguiente línea:

```text
net.ipv4.ip_forward=1
```

3. Recarga la configuración del kernel de forma inmediata:

```bash
sudo sysctl -p
```

4. Comprueba que el valor retorne `1`:

```bash
cat /proc/sys/net/ipv4/ip_forward
```

---

### Solución Tarea 3: Reglas de NAT y Persistencia

1. Aplica la regla de enmascaramiento con `iptables`:

```bash
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
```

2. Verifica que la regla se haya añadido correctamente a la tabla `nat`:

```bash
sudo iptables -t nat -L -n -v
```

3. Instala `iptables-persistent` para guardar las reglas:

```bash
sudo apt update
sudo apt install iptables-persistent -y
```
*(Acepta con **SÍ** cuando el instalador solicite guardar las reglas IPv4 actuales).*

4. Si editas las reglas en el futuro, puedes actualizarlas manualmente usando:

```bash
sudo netfilter-persistent save
```

---

### Solución Tarea 4: Pruebas de Verificación desde el Cliente

Ejecuta los siguientes comandos desde la terminal del equipo **`Cliente-Linux`**:

1. **Ping a la puerta de enlace (Servidor):**
   ```bash
   ping -c 4 192.168.20.1
   ```
   *(Respuesta esperada: 0% de paquetes perdidos).*

2. **Ping a IP externa pública (Prueba de NAT e IP Forwarding):**
   ```bash
   ping -c 4 8.8.8.8
   ```
   *(Respuesta esperada: Respuesta correcta desde la IP de Google).*

3. **Comprobar ruta del tráfico (Traza de saltos):**
   ```bash
   traceroute 8.8.8.8
   ```
   *(Respuesta esperada: El Salto 1 debe mostrar la IP `192.168.20.1`).*