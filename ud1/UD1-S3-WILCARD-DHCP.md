# Cheat Sheet: Configuración de Red y DHCP en Linux Server
# Autor: Paco Aldarias
# Fecha: 2026-10-04
# Módulo: SRI

Guía rápida de referencia para la gestión de interfaces, clientes DHCP, servidores DHCP y herramientas de configuración de red en Linux.

---

## 1. Inspección y Verificación de Red

Comandos para consultar la configuración actual de las interfaces e IPs.

| Comando | Descripción |
| :--- | :--- |
| `ip a` | Muestra todas las interfaces de red con sus direcciones IP asignadas. |
| `ip address show <interfaz>` | Muestra la información de red de una interfaz específica (ej. `ip address show eth0`). |
| `ifconfig` | (*Herramienta heredada*) Muestra las interfaces de red activas e IP. |
| `ifconfig -a` | Muestra todas las interfaces, incluidas las inactivas. |
| `hostname -I` | Muestra rápidamente las direcciones IP asociadas al host. |

---

## 2. Gestión de Cliente DHCP (`dhclient`)

Herramienta para solicitar, liberar o renovar direcciones IP mediante DHCP.

| Comando | Descripción |
| :--- | :--- |
| `dhclient <interfaz>` | Solicita una nueva concesión de IP para la interfaz indicada. |
| `dhclient -r <interfaz>` | Libera la dirección IP actual concedida por el servidor DHCP. |
| `dhclient -v <interfaz>` | Ejecuta la solicitud DHCP mostrando información detallada (modo *verbose*). |

---

## 3. Servidor DHCP (`isc-dhcp-server`)

Comandos para la administración, prueba de sintaxis y reinicio del servicio de servidor DHCP en Debian/Ubuntu.

| Comando | Descripción |
| :--- | :--- |
| `dhcpd -t -cf /etc/dhcp/dhcpd.conf` | **Validar sintaxis:** Comprueba que no existan errores en el archivo de configuración sin arrancar el servicio. |
| `systemctl restart isc-dhcp-server` | Reinicia el servicio del servidor DHCP para aplicar los cambios de configuración. |
| `systemctl status isc-dhcp-server` | Comprueba el estado actual del servicio DHCP. |
| `systemctl stop isc-dhcp-server` | Detiene el servidor DHCP. |
| `systemctl start isc-dhcp-server` | Inicia el servidor DHCP. |

---

## 4. Reinicio de la Configuración de Red

Métodos para aplicar o reiniciar las redes según el gestor utilizado.

* **Con `systemd-networkd`:**
  ```bash
  systemctl restart systemd-networkd
  ```

* **Con `NetworkManager` (RHEL / CentOS / Fedora / Ubuntu Desktop):**
  ```bash
  systemctl restart NetworkManager
  ```

* **Sistemas tradicionales (Debian/Ubuntu antiguos):**
  ```bash
  /etc/init.d/networking restart
  ```

---

## 5. Archivos de Configuración Clave

### A. Netplan (`/etc/netplan/*.yaml`)
Sistemas Ubuntu modernos utilizan Netplan para la configuración de red estática o por DHCP.

* **Ubicación:** `/etc/netplan/50-cloud-init.yaml` o `/etc/netplan/01-netcfg.yaml`
* **Comandos esenciales de Netplan:**

| Comando | Descripción |
| :--- | :--- |
| `netplan generate` | Valida los archivos YAML y genera las configuraciones internas para el backend (`networkd` o `NetworkManager`). |
| `netplan apply` | Aplica inmediatamente la nueva configuración de red. |
| `netplan try` | Aplica la configuración de forma temporal (revierte los cambios automáticamente tras 120s si no se confirma), evitando quedarse sin acceso remoto. |

**Ejemplo de contenido Netplan (Cliente DHCP):**
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: true
```

---

### B. Configuración del Servidor DHCP (`/etc/dhcp/dhcpd.conf`)
Archivo principal de configuración del servidor `isc-dhcp-server`. Define los rangos de IP, tiempo de concesión, DNS, puerta de enlace y reservas.

**Ejemplo de contenido:**
```text
# Parámetros globales
default-lease-time 600;
max-lease-time 7200;
authoritative;

# Definición de subred
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;
  option routers 192.168.1.1;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
}

# Reserva de IP por MAC
host servidor-fijo {
  hardware ethernet 00:11:22:33:44:55;
  fixed-address 192.168.1.50;
}
```

---

### C. Selección de Interfaz para Servidor DHCP (`/etc/default/isc-dhcp-server`)
Indica en qué tarjeta(s) de red debe escuchar las peticiones el servidor DHCP.

**Ejemplo de contenido:**
```text
INTERFACESv4="eth0"
INTERFACESv6=""
```

---

### D. Interfaces Tradicionales Debian/Ubuntu (`/etc/network/interfaces`)
Utilizado en distribuciones Debian y Ubuntu previas a la llegada de Netplan.

**Ejemplo de contenido:**
```text
auto eth0
iface eth0 inet dhcp
```

---

### E. Concesiones Activas DHCP (*Leases*)
Archivos donde se registran las direcciones IP concedidas a los clientes.

* **Cliente DHCP:** `/var/lib/dhcp/dhclient.leases`
* **Servidor DHCP:** `/var/lib/dhcp/dhcpd.leases`

### F. Cómo saber qué servidor DHCP asignó la configuración a un host
Para averiguar qué servidor DHCP respondió a un equipo y le proporcionó la IP, normalmente se consulta el archivo de leases del cliente.

**Desde el propio host cliente:**
```bash
cat /var/lib/dhcp/dhclient.leases
```

Buscas la línea con el identificador del servidor DHCP:
```bash
grep "dhcp-server-identifier" /var/lib/dhcp/dhclient.leases
```

Si quieres ver la concesión concreta de una MAC o una IP, puedes filtrar:
```bash
grep -i "00:11:22:33:44:55" /var/lib/dhcp/dhclient.leases
```

**Ejemplo de salida típica:**
```text
option dhcp-server-identifier 192.168.1.1;
```

Esto indica que el servidor DHCP con IP `192.168.1.1` fue el que asignó la configuración de red al host.

**Desde el servidor DHCP:**
```bash
grep -i "00:11:22:33:44:55" /var/lib/dhcp/dhcpd.leases
```

También se puede comprobar el historial del cliente con la opción verbose:
```bash
dhclient -v eth0
```

En la salida aparecerán detalles del intercambio DHCP, incluido el servidor que respondió.

---

## Licencia

[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/)