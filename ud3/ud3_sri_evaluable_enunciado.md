# ACTIVIDAD EVALUABLE - UD3: SERVIDORES WEB (APACHE2, DNS, DHCP & HTTPS)
**Módulo:** Servicios de Red e Internet (SRI)  
**Curso:** 2º Ciclo Formativo de Grado Superior en Administración de Sistemas Informáticos en Red (ASIR)  
**Contexto:** Ecosistema Dragon Ball (`sri.dragonball.local`)  
**Curso Académico:** 2026-27  
**Profesor:** Paco Aldarias  
**Fecha:** 2-11-2026  
---

## ENUNCIADO DE LA ACTIVIDAD

### 1. Introducción y Objetivos
En esta práctica se configurará desde cero la infraestructura de red y servicios para dar soporte a dos sitios web temáticos sobre Dragon Ball:
1. `dbs.sri.dragonball.local` (Dragon Ball Super)
2. `dbd.sri.dragonball.local` (Dragon Ball Daima)

El alumnado desplegará los servicios esenciales de red (DHCP y DNS), configurará el servidor web **Apache2** para servir ambos dominios mediante Virtual Hosts, y asegurará la transmisión a través de **HTTPS** con certificados SSL/TLS autofirmados y redirección obligatoria desde HTTP (puerto 80) hacia HTTPS (puerto 443).

### 2. Escenario y Requisitos Técnicos
* **Servidor (`DragonBall-Server`):**
  * SO: Ubuntu Server / Debian.
  * Tarjeta de Red 1 (WAN / NAT): Salida a Internet (DHCP externo).
  * Tarjeta de Red 2 (LAN / Red Interna): IP Estática `192.168.10.1/24`.
  * Servicios a instalar y configurar: Routing (IP Forwarding + IPTables NAT), isc-dhcp-server (o dnsmasq), Bind9 (o dnsmasq), Apache2 y OpenSSL.
* **Cliente (`ClienteGuerreroZ`):**
  * SO: Ubuntu Desktop / Debian GUI.
  * Tarjeta de Red: Red Interna.
  * Obtención de red: DHCP automático servido por el servidor.

---

### Tarea 1: Infraestructura Base de Red, Routing, DHCP y DNS (3 puntos)
1. **Configuración de Red y Enrutamiento:**
   * Configura la interfaz LAN del servidor con la IP fija `192.168.10.1/24`.
   * Habilita el reenvío de paquetes IP (`net.ipv4.ip_forward = 1`) y configura las reglas de `iptables` en la cadena POSTROUTING para permitir el enrutamiento y enmascaramiento (NAT) del cliente hacia Internet.
2. **Servicio DHCP:**
   * Configura el servidor DHCP para repartir direccionamiento en el rango `192.168.10.100` a `192.168.10.200`.
   * Define como puerta de enlace predeterminada (`routers`) y servidor DNS principal (`domain-name-servers`) la IP del servidor (`192.168.10.1`).
3. **Servicio DNS:**
   * Configura la zona directa de resolución para el dominio `sri.dragonball.local`.
   * Agrega los registros tipo A necesarios para resolver:
     * `server.sri.dragonball.local` -> `192.168.10.1`
     * `dbs.sri.dragonball.local` -> `192.168.10.1`
     * `dbd.sri.dragonball.local` -> `192.168.10.1`
   * Configura reenviadores (forwarders) hacia `8.8.8.8` para permitir navegación externa.
   * Verifica la resolución desde el cliente con `dig` o `nslookup`.

---

### Tarea 2: Despliegue del Servidor Web HTTP con Apache2 (3 puntos)
1. Instala el servidor web Apache2 en el servidor.
2. Crea los directorios de publicación web y un archivo `index.html` básico diferenciado para cada sitio:
   * Sitio 1: `/var/www/dbs/index.html`
   * Sitio 2: `/var/www/dbd/index.html`
3. Configura los archivos de VirtualHost en `/etc/apache2/sites-available/`:
   * `dbs.sri.dragonball.local.conf`
   * `dbd.sri.dragonball.local.conf`
4. Habilita los nuevos sitios web, deshabilita el sitio por defecto (`000-default.conf`) y recarga Apache.
5. Comprueba desde el navegador del cliente que ambos nombres de dominio muestran sus contenidos correspondientes sobre HTTP.

---

### Tarea 3: Seguridad Web HTTPS con SSL/TLS (3 puntos)
1. Habilita el módulo SSL en Apache (`a2enmod ssl`) y el módulo de reescritura (`a2enmod rewrite`).
2. Genera un certificado SSL autofirmado mediante `openssl` válido por 365 días para la entidad organizativa `CapsuleCorp`:
   * Clave privada: `/etc/ssl/private/dragonball.key`
   * Certificado: `/etc/ssl/certs/dragonball.crt`
3. Configura los VirtualHosts del puerto 443 para `dbs.sri.dragonball.local` y `dbd.sri.dragonball.local`, enlazando los archivos de certificado y clave privada.
4. Configura una redirección permanente (HTTP 301) o regla de Rewrite en los VirtualHosts del puerto 80 para forzar a todo el tráfico HTTP a redirigirse automáticamente a HTTPS.
5. Realiza las comprobaciones en el cliente confirmando el acceso seguro y la advertencia de certificado autofirmado.

---

### Tarea 4: Documentación y Verificación (1 punto)
Presenta un informe técnico estructurado que incluya:
* Esquema lógico de red.
* Comandos utilizados e imágenes/capturas de las configuraciones esenciales.
* Capturas de pantalla de la verificación desde el cliente (`ip a`, `dig`/`nslookup`, `curl -I`, navegación con Firefox en HTTP/HTTPS).


