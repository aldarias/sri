# GUÍA TÉCNICA: ENRUTAMIENTO (IP FORWARDING) Y NAT CON IPTABLES EN UBUNTU

**Módulo:** Servicios de Red e Internet (SRI)  
**Nivel:** 2º Curso - ASIR (Administración de Sistemas Informáticos en Red)  
**Curso Académico:** 2026-27  
**Profesor:** Paco Aldarias  
**Fecha:** 2-11-2026  
---

## 1. INTRODUCCIÓN Y CONCEPTOS CLAVE

Un servidor Linux puede actuar como un **router de red** interconectando dos o más interfaces de red físicas o virtuales. Para lograr que el tráfico pase de una red interna (LAN) a una red externa (WAN / Internet), se requieren dos elementos fundamentales:

1. **IP Forwarding (Reenvío de Paquetes IP):** Capacidad del Kernel de Linux para reenviar paquetes recibidos en una interfaz de red hacia otra interfaz distinta.
2. **NAT (Network Address Translation - Enmascaramiento):** Mecanismo mediante el cual el router modifica la dirección IP de origen de los paquetes de la LAN y las sustituye por su propia IP pública/WAN antes de enviarlos a Internet.

---

## 2. ARQUITECTURA DE RED DE TRABAJO

Para esta configuración tomaremos como base el siguiente escenario típico:

```
                  +--------------------------------+
                  |    SERVIDOR / ROUTER UBUNTU    |
                  |                                |
  [ INTERNET ] ---| enp0s3 (WAN / DHCP)            |
                  |   IP: Asignada por router/NAT  |
                  |                                |
                  | enp0s8 (LAN / Estática)        |--- [ CLIENTES LAN ]
                  |   IP: 192.168.10.1/24          |    Red: 192.168.10.0/24
                  +--------------------------------+    GW:  192.168.10.1
```

---

## 3. CONFIGURACIÓN PASO A PASO

### Paso 1: Configurar las Interfaces de Red (`netplan`)

En Ubuntu, la configuración de red se gestiona a través de archivos YAML en `/etc/netplan/`.

1. Abre o edita el archivo de configuración (por ejemplo, `01-netcfg.yaml` o `50-cloud-init.yaml`):
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```

2. Configura las dos tarjetas de red (reemplaza los nombres por los de tu sistema, como `enp0s3` y `enp0s8`):
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp0s3: # Interfaz WAN (Conexión hacia Internet)
         dhcp4: true
       enp0s8: # Interfaz LAN (Conexión con los clientes)
         dhcp4: false
         addresses:
           - 192.168.10.1/24
   ```

3. Aplica los cambios de red:
   ```bash
   sudo netplan apply
   ```

4. Comprueba las IPs asignadas:
   ```bash
   ip a
   ```

---

### Paso 2: Habilitar IP Forwarding (Reenvío IP)

Por defecto, el kernel de Linux tiene desactivada la opción de reenviar paquetes entre interfaces por motivos de seguridad.

#### Habilitación Temporal (hasta el próximo reinicio):
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

#### Habilitación Permanente:
1. Edita el archivo de parámetros del Kernel:
   ```bash
   sudo nano /etc/sysctl.conf
   ```

2. Busca la siguiente línea, descoméntala (quita el carácter `#` al inicio) o añádela al final del archivo:
   ```ini
   net.ipv4.ip_forward=1
   ```

3. Aplica la configuración de inmediato:
   ```bash
   sudo sysctl -p
   ```

4. Verifica que el valor sea `1`:
   ```bash
   cat /proc/sys/net/ipv4/ip_forward
   ```

---

### Paso 3: Configurar NAT / Enmascaramiento con IPTables

`iptables` es la herramienta de espacio de usuario para configurar las tablas del cortafuegos de Linux. Para habilitar NAT utilizamos la tabla **`nat`** y la cadena **`POSTROUTING`**.

#### 1. Añadir la regla de Masquerade (Enmascaramiento):
```bash
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
```
* **`-t nat`**: Indica que vamos a trabajar en la tabla de traducción de direcciones (NAT).
* **`-A POSTROUTING`**: Añade una regla a la cadena POSTROUTING (se aplica justo antes de que los paquetes salgan de la interfaz).
* **`-o enp0s3`**: Especifica la **interfaz de salida hacia Internet** (WAN). ¡Asegúrate de poner el nombre correcto de tu interfaz!
* **`-j MASQUERADE`**: Acción que reemplaza la IP origen privada por la IP de la interfaz WAN.

#### 2. Configurar el reenvío de tráfico en la cadena FORWARD (si el Firewall estuviera bloqueando por defecto):
```bash
sudo iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
sudo iptables -A FORWARD -i enp0s3 -o enp0s8 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

#### 3. Verificar las reglas creadas:
```bash
sudo iptables -t nat -L -n -v
```

---

### Paso 4: Persistencia de Reglas IPTables

Las reglas añadidas directamente con el comando `iptables` se pierden al reiniciar el sistema. Para hacerlas permanentes utilizaremos el paquete `iptables-persistent`.

1. Instala el paquete de persistencia:
   ```bash
   sudo apt update
   sudo apt install iptables-persistent -y
   ```
   *Durante la instalación, el asistente te preguntará si deseas guardar las reglas IPv4 e IPv6 actuales. Selecciona **SÍ (YES)**.*

2. Si modificas o agregas nuevas reglas en el futuro, puedes guardar la configuración manualmente ejecutando:
   ```bash
   sudo netfilter-persistent save
   ```
   *(O alternativamente: `sudo iptables-save | sudo tee /etc/iptables/rules.v4`)*

3. Para recargar las reglas guardadas en cualquier momento:
   ```bash
   sudo netfilter-persistent reload
   ```

---

## 4. VERIFICACIÓN Y PRUEBAS DESDE EL CLIENTE

Para confirmar que el servicio de routing y NAT funciona correctamente:

1. **Configuración de red en el cliente:**
   * IP del cliente: `192.168.10.X/24` (ej. `192.168.10.100`).
   * Puerta de enlace (Gateway): `192.168.10.1` (IP del Servidor).
   * Servidor DNS: `8.8.8.8` o la IP del Servidor si tiene DNS configurado.

2. **Comprobación de conectividad ICMP (Ping):**
   * Hacer ping a la puerta de enlace (Servidor):
     ```bash
     ping 192.168.10.1
     ```
   * Hacer ping a una IP pública en Internet (comprueba que el routing y NAT funcionan):
     ```bash
     ping 8.8.8.8
     ```
   * Hacer ping a un nombre de dominio (comprueba la resolución DNS a través de la red):
     ```bash
     ping google.com
     ```

3. **Traza de rutas (`traceroute` / `tracert`):**
   ```bash
   traceroute 8.8.8.8
   ```
   *El primer salto debe ser obligatoriamente la IP LAN de tu servidor (`192.168.10.1`).*

---

## 5. COMANDOS RÁPIDOS DE REFERENCIA

| Acción | Comando |
| :--- | :--- |
| Comprobar IP Forwarding | `cat /proc/sys/net/ipv4/ip_forward` |
| Ver reglas NAT actuales | `sudo iptables -t nat -L -n -v` |
| Borrar reglas de la tabla NAT | `sudo iptables -t nat -F` |
| Guardar reglas permanentemente | `sudo netfilter-persistent save` |
| Ver estado del servicio de reglas | `sudo systemctl status netfilter-persistent` |
