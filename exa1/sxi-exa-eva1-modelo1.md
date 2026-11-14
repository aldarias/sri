# EXAMEN DE LA 1ª EVALUACIÓN: SERVICIOS DE RED E INTERNET (SRI / SXI)
**Ciclo Formativo:** 2º Curso - Grado Superior en Administración de Sistemas Informáticos en Red (ASIR)  
**Módulo:** Servicios de Red e Internet (SRI)  
**Modelo de Examen:** MODELO 1  
**Duración:** 1 hora y 30 minutos (90 minutos)  

---

### DATOS DEL ALUMNO/A
* **Nombre y Apellidos:** __________________________________________________________________
* **Fecha del Examen:** ________________________________________

---

### INSTRUCCIONES GENERALES Y CRITERIOS DE CALIFICACIÓN
1. **Normas de aula y permanencia:**
   * No se podrá abandonar el aula hasta transcurridos **15 minutos** desde el inicio del examen.
   * Al finalizar y entregar el examen, el alumno/a deberá **firmar obligatoriamente la hoja de asistencia**.
2. **Estructura de la prueba y Puntuación:**
   * **Parte 1: Preguntas Tipo Test (15 preguntas):** Puntuación total de **5 puntos**. Cada respuesta correcta suma +0,333 puntos. **Penalización:** 4 preguntas incorrectas restan 1 correcta (-0,0833 puntos por respuesta fallida). Las preguntas no contestadas o en blanco no penalizan.
   * **Parte 2: Preguntas Cortas de Desarrollo (6 preguntas):** Puntuación total de **5 puntos**. Cada pregunta de desarrollo se valora hasta 0,833 puntos.
3. **Evaluación por Resultados de Aprendizaje (RA):**
   * El examen evalúa los contenidos pertenecientes a los tres primeros Resultados de Aprendizaje de la Guía Didáctica:
     * **RA1:** Administra servicios de resolución de nombres (DNS / BIND9). *(Preguntas Test 1 a 5 | Preguntas Desarrollo D1 y D2)*
     * **RA2:** Administra servicios de configuración automática de red (DHCP / isc-dhcp-server). *(Preguntas Test 6 a 10 | Preguntas Desarrollo D3 y D4)*
     * **RA3:** Administra servidores Web (Apache2 / Nginx / HTTP / HTTPS / VirtualHosts / NAT). *(Preguntas Test 11 a 15 | Preguntas Desarrollo D5 y D6)*
   * **Calificación final:** Cada RA se evalúa de **0 a 10 puntos**. La nota final del examen se obtiene mediante la media aritmética simple de la nota de los tres Resultados de Aprendizaje:
     $$\text{Nota Final Examen} = \frac{\text{Nota RA1} + \text{Nota RA2} + \text{Nota RA3}}{3}$$

---

## HOJA DE RESPUESTAS

### PARTE 1: TABLA DE RESPUESTAS DEL TEST
*Instrucciones: Marque con una X o escriba la letra (A, B, C o D) correspondiente a la opción elegida.*

| Pregunta | RA Asignado | Respuesta (A / B / C / D) |
| :---: | :---: | :---: |
| **1** | RA1 | |
| **2** | RA1 | |
| **3** | RA1 | |
| **4** | RA1 | |
| **5** | RA1 | |
| **6** | RA2 | |
| **7** | RA2 | |
| **8** | RA2 | |
| **9** | RA2 | |
| **10** | RA2 | |
| **11** | RA3 | |
| **12** | RA3 | |
| **13** | RA3 | |
| **14** | RA3 | |
| **15** | RA3 | |

---

### PARTE 2: ESPACIO PARA RESPUESTAS DE DESARROLLO

| Pregunta | RA | Espacio Reservado para Respuesta |
| :---: | :---: | :--- |
| **D1** | RA1 | |
| **D2** | RA1 | |
| **D3** | RA2 | |
| **D4** | RA2 | |
| **D5** | RA3 | |
| **D6** | RA3 | |

---

## ENUNCIADO DEL EXAMEN

### BLOQUE I: RESULTADO DE APRENDIZAJE 1 (RA1)
*RA1: Administra servicios de resolución de nombres, analizándolos y garantizando la seguridad del servicio.*

#### Preguntas Tipo Test (RA1)

**Pregunta 1 [RA1]**  
¿Cuál es el puerto y protocolo de transporte estándar utilizado por un servidor DNS para responder a consultas habituales de resolución de nombres de los clientes?  
A) Puerto 80 TCP.  
B) Puerto 53 UDP.  
C) Puerto 67 UDP.  
D) Puerto 443 TCP.  

**Pregunta 2 [RA1]**  
En el servidor DNS BIND9 bajo Debian/Ubuntu, ¿en qué archivo de configuración se definen los reenviadores (*forwarders*) globales y las restricciones de consulta (*allow-query*)?  
A) `/etc/bind/named.conf.local`  
B) `/etc/bind/named.conf.options`  
C) `/etc/bind/db.local`  
D) `/etc/resolv.conf`  

**Pregunta 3 [RA1]**  
Dentro de la estructura de un fichero de zona de BIND9, ¿cuál es la función principal del registro SOA (*Start of Authority*)?  
A) Definir la IP pública del servidor web secundario.  
B) Asignar una dirección IPv6 a un nombre de dominio completo (FQDN).  
C) Establecer la autoridad sobre la zona y definir parámetros como el número de serie, tiempos de refresco y expiración.  
D) Traducir una dirección IP privada a un nombre de host en consultas inversas.  

**Pregunta 4 [RA1]**  
¿Qué tipo de registro DNS se utiliza específicamente en las zonas de resolución inversa (`in-addr.arpa`) para cartografiar una dirección IP a un nombre de dominio?  
A) Registro A.  
B) Registro CNAME.  
C) Registro MX.  
D) Registro PTR.  

**Pregunta 5 [RA1]**  
¿Qué comando de la suite BIND9 se debe ejecutar para comprobar la validez sintáctica de un archivo de zona directa como `/etc/bind/db.sri.local` antes de reiniciar el servicio?  
A) `named-checkconf /etc/bind/db.sri.local`  
B) `named-checkzone sri.local /etc/bind/db.sri.local`  
C) `bind9-validate /etc/bind/db.sri.local`  
D) `systemctl check bind9`  

---

#### Preguntas Cortas de Desarrollo (RA1)

**Pregunta D1 [RA1]**  
Explica el propósito de las directivas `forwarders` y `allow-query` en el archivo `/etc/bind/named.conf.options` de BIND9. Proporciona un ejemplo en sintaxis BIND donde se configure como reenviador la IP `8.8.8.8` y se permitan consultas únicamente a la red local `192.168.10.0/24` y a `localhost`.

**Pregunta D2 [RA1]**  
Diseña el contenido de un fichero de zona directa de BIND9 (`/etc/bind/db.sri.local`) para el dominio `sri.local`. El fichero debe incluir la cabecera SOA con servidor principal `server.sri.local.`, el registro NS correspondiente, un registro A que vincule `server` a `192.168.10.1`, un registro A para `web` con la IP `192.168.10.20`, y un alias CNAME para que `www` apunte a `web.sri.local.`.

---

### BLOQUE II: RESULTADO DE APRENDIZAJE 2 (RA2)
*RA2: Administra servicios de configuración automática, identificándolos y verificando la correcta asignación de los parámetros.*

#### Preguntas Tipo Test (RA2)

**Pregunta 6 [RA2]**  
Indica el orden secuencial correcto de los 4 mensajes intercambiados entre un cliente y un servidor DHCP durante el proceso inicial de obtención de dirección IP (modelo DORA):  
A) DHCPOFFER -> DHCPDISCOVER -> DHCPACK -> DHCPREQUEST.  
B) DHCPDISCOVER -> DHCPOFFER -> DHCPREQUEST -> DHCPACK.  
C) DHCPREQUEST -> DHCPACK -> DHCPDISCOVER -> DHCPOFFER.  
D) DHCPINFORM -> DHCPOFFER -> DHCPREQUEST -> DHCPRELEASE.  

**Pregunta 7 [RA2]**  
¿Qué puertos UDP utilizan el servidor DHCP y los clientes DHCP respectivamente para el intercambio de mensajes de configuración?  
A) Servidor UDP 67 y Cliente UDP 68.  
B) Servidor UDP 68 y Cliente UDP 67.  
C) Servidor UDP 53 y Cliente UDP 53.  
D) Servidor UDP 69 y Cliente UDP 69.  

**Pregunta 8 [RA2]**  
En el servicio `isc-dhcp-server` de Linux, ¿qué opción de configuración se utiliza dentro del bloque `subnet` para enviar a los clientes la IP de la puerta de enlace predeterminada?  
A) `option domain-name-servers`  
B) `option default-gateway`  
C) `option routers`  
D) `option broadcast-address`  

**Pregunta 9 [RA2]**  
¿Qué representa el parámetro *Lease Time* (tiempo de concesión) en un servidor DHCP?  
A) El tiempo máximo que tarda el servidor en responder al mensaje DHCPDISCOVER.  
B) El tiempo durante el cual un cliente mantiene como propios los datos de configuración asignados antes de tener que solicitar su renovación.  
C) El tiempo en segundos que el servidor espera para guardar el historial en disco.  
D) El intervalo de tiempo en el que se limpian automáticamente los registros DNS.  

**Pregunta 10 [RA2]**  
¿Cuál es la función principal de un Agente de Retransmisión DHCP (*DHCP Relay Agent*) en una red corporativa?  
A) Encriptar las peticiones DHCP para evitar ataques de *sniffing*.  
B) Escuchar los mensajes de broadcast DHCPDISCOVER en una subred y retransmitirlos por unicast a un servidor DHCP situado en otra subred distinta.  
C) Asignar direcciones IP secundarias cuando el servidor principal está fuera de servicio.  
D) Convertir paquetes DHCP v4 a direcciones IPv6 de forma transparente.  

---

#### Preguntas Cortas de Desarrollo (RA2)

**Pregunta D3 [RA2]**  
Redacta el bloque de configuración para la subred `192.168.10.0/24` que debe incluirse en el archivo `/etc/dhcp/dhcpd.conf` de `isc-dhcp-server`. La configuración debe especificar:  
- Rango de IPs dinámicas: `192.168.10.100` a `192.168.10.200`.  
- Puerta de enlace: `192.168.10.1`.  
- Máscara de subred: `255.255.255.0`.  
- Servidor DNS: `192.168.10.1`.  
- Tiempo de concesión por defecto (`default-lease-time`): 600 segundos y máximo (`max-lease-time`): 7200 segundos.

**Pregunta D4 [RA2]**  
Explica cómo se realiza una reserva estática de dirección IP basada en la dirección MAC del cliente en `isc-dhcp-server`. Muestra la sintaxis exacta dentro de `dhcpd.conf` para asignar de forma fija la IP `192.168.10.50` al equipo de nombre `vegeta` con la dirección MAC `42:55:F5:A5:15:E7`.

---

### BLOQUE III: RESULTADO DE APRENDIZAJE 3 (RA3)
*RA3: Administra servidores Web aplicando criterios de configuración y asegurando el funcionamiento del servicio.*

#### Preguntas Tipo Test (RA3)

**Pregunta 11 [RA3]**  
En el protocolo HTTP, ¿qué significado tiene recibir un código de estado de respuesta dentro de la familia **300-399** (como el código 301)?  
A) La petición ha finalizado correctamente sin errores.  
B) Existe un error en la sintaxis de la petición del cliente.  
C) El recurso solicitado ha sido procesado y redirigido permanentemente a una nueva localización.  
D) Ocurrió un error interno e inesperado en el servidor web.  

**Pregunta 12 [RA3]**  
¿Qué método de petición HTTP se utiliza habitualmente para enviar datos sensibles o de formularios al servidor incluidos en el cuerpo del mensaje (*body*), sin mostrarlos en la URL?  
A) Método GET.  
B) Método POST.  
C) Método HEAD.  
D) Método OPTIONS.  

**Pregunta 13 [RA3]**  
En un servidor web Apache2 sobre Ubuntu, ¿qué comandos de consola se emplean respectivamente para habilitar un nuevo sitio web (`dbs.conf`) y para activar el módulo SSL?  
A) `a2ensite dbs.conf` y `a2enmod ssl`  
B) `apache2-enable dbs.conf` y `apache2-module ssl`  
C) `systemctl start dbs.conf` y `systemctl enable ssl`  
D) `a2dissite dbs.conf` y `a2dismod ssl`  

**Pregunta 14 [RA3]**  
¿Qué directiva de configuración establece el directorio raíz del sistema de archivos donde se almacenan los documentos web a servir (como `index.html`) en Apache2 y Nginx respectivamente?  
A) Apache2: `ServerRoot` | Nginx: `www_dir`  
B) Apache2: `DocumentRoot` | Nginx: `root`  
C) Apache2: `SiteDirectory` | Nginx: `location`  
D) Apache2: `DirectoryPath` | Nginx: `path`  

**Pregunta 15 [RA3]**  
Para configurar un servidor Ubuntu como router NAT y permitir que los clientes de la LAN salgan a Internet por la interfaz WAN (`enp0s3`), ¿qué comando `iptables` se debe ejecutar en la tabla de traducción de direcciones?  
A) `sudo iptables -t nat -A PREROUTING -i enp0s3 -j ACCEPT`  
B) `sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE`  
C) `sudo iptables -t filter -A FORWARD -o enp0s3 -j DROP`  
D) `sudo iptables -A INPUT -p tcp --dport 80 -j NAT`  

---

#### Preguntas Cortas de Desarrollo (RA3)

**Pregunta D5 [RA3]**  
Describe la configuración completa del archivo de VirtualHost en Apache2 (`/etc/apache2/sites-available/dbs.sri.local.conf`) para prestar servicio web al dominio `dbs.sri.local`. El archivo debe definir:  
1. Un bloque en puerto 80 que realice una redirección permanente (HTTP 301) hacia HTTPS (`https://dbs.sri.local/`).  
2. Un bloque en puerto 443 con `DocumentRoot /var/www/dbs`, habilitando el motor SSL (`SSLEngine on`) y enlazando los certificados digital autofirmado (`/etc/ssl/certs/dragonball.crt`) y clave privada (`/etc/ssl/private/dragonball.key`).

**Pregunta D6 [RA3]**  
Explica paso a paso los 3 comandos/procedimientos indispensables en Linux Ubuntu para transformar la máquina en un router con enrutamiento de paquetes e IPTables permanente:  
1. Habilitación del reenvío de paquetes en el Kernel (`net.ipv4.ip_forward = 1` en `/etc/sysctl.conf`).  
2. Aplicación de la regla de enmascaramiento NAT con `iptables`.  
3. Guardado permanente de las reglas de cortafuegos para que persistan tras un reinicio del sistema.

---

## SOLUCIONARIO DETALLADO (MODELO 1)

### SOLUCIONES DEL BLOQUE I: RA1 (DNS / BIND9)

#### Respuestas Test RA1:
* **Pregunta 1: B) Puerto 53 UDP.**  
  *Justificación:* El servicio DNS escucha de forma estándar en el puerto 53 utilizando el protocolo de transporte UDP para consultas rápidas de resolución.  
* **Pregunta 2: B) `/etc/bind/named.conf.options`**  
  *Justificación:* Las opciones globales del servidor BIND9 (como `forwarders`, `allow-query` y `listen-on-v4`) se configuran en `named.conf.options`.  
* **Pregunta 3: C) Establecer la autoridad sobre la zona y definir parámetros como el número de serie, tiempos de refresco y expiración.**  
  *Justificación:* El registro SOA (*Start of Authority*) define el inicio de autoridad del dominio y almacena los metadatos de sincronización de la zona.  
* **Pregunta 4: D) Registro PTR.**  
  *Justificación:* Los registros Pointer (PTR) se utilizan en zonas inversas (`in-addr.arpa`) para traducir direcciones IP numéricas a nombres de dominio.  
* **Pregunta 5: B) `named-checkzone sri.local /etc/bind/db.sri.local`**  
  *Justificación:* `named-checkzone` valida la sintaxis interna y coherencia de un archivo de zona específico para un dominio dado.

#### Solución Preguntas de Desarrollo RA1:

* **Solución Pregunta D1 [RA1]:**  
  - `forwarders`: Define las direcciones IP de servidores DNS externos (como los DNS de Google `8.8.8.8`) a los cuales BIND9 reenviará las consultas relativas a dominios de los que no es autoritativo.  
  - `allow-query`: Especifica qué redes o hosts tienen permiso para realizar consultas al servidor DNS, aumentando la seguridad.  
  - *Ejemplo en `/etc/bind/named.conf.options`:*
    ```bind
    options {
            directory "/var/cache/bind";
            forwarders {
                    8.8.8.8;
            };
            allow-query {
                    127.0.0.1;
                    192.168.10.0/24;
            };
            dnssec-validation auto;
            listen-on-v4 { any; };
    };
    ```

* **Solución Pregunta D2 [RA1]:**  
  *Fichero `/etc/bind/db.sri.local`:*
  ```bind
  $TTL    604800
  @       IN      SOA     server.sri.local. root.sri.local. (
                                2026111401 ; Serial
                                    604800 ; Refresh
                                     86400 ; Retry
                                   2419200 ; Expire
                                    604800 ) ; Negative Cache TTL
  ;
  @       IN      NS      server.sri.local.
  server  IN      A       192.168.10.1
  web     IN      A       192.168.10.20
  www     IN      CNAME   web.sri.local.
  ```

---

### SOLUCIONES DEL BLOQUE II: RA2 (DHCP / isc-dhcp-server)

#### Respuestas Test RA2:
* **Pregunta 6: B) DHCPDISCOVER -> DHCPOFFER -> DHCPREQUEST -> DHCPACK.**  
  *Justificación:* Es la secuencia de cuatro vías (DORA) para la negociación de IP por broadcast/unicast.  
* **Pregunta 7: A) Servidor UDP 67 y Cliente UDP 68.**  
  *Justificación:* Los servidores DHCP escuchan peticiones en el puerto 67/UDP y los clientes reciben respuestas en el puerto 68/UDP.  
* **Pregunta 8: C) `option routers`**  
  *Justificación:* La directiva `option routers` especifica la IP del router/puerta de enlace enviada a los clientes.  
* **Pregunta 9: B) El tiempo durante el cual un cliente mantiene como propios los datos de configuración asignados antes de tener que solicitar su renovación.**  
  *Justificación:* Define la validez temporal del contrato o concesión IP.  
* **Pregunta 10: B) Escuchar los mensajes de broadcast DHCPDISCOVER en una subred y retransmitirlos por unicast a un servidor DHCP situado en otra subred distinta.**  
  *Justificación:* Permite centralizar un servidor DHCP sin necesidad de desplegar uno físico en cada subred, cruzando routers.

#### Solución Preguntas de Desarrollo RA2:

* **Solución Pregunta D3 [RA2]:**  
  *Bloque en `/etc/dhcp/dhcpd.conf`:*
  ```option
  subnet 192.168.10.0 netmask 255.255.255.0 {
    range 192.168.10.100 192.168.10.200;
    option routers 192.168.10.1;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 192.168.10.1;
    default-lease-time 600;
    max-lease-time 7200;
  }
  ```

* **Solución Pregunta D4 [RA2]:**  
  La reserva estática asocia una dirección MAC física con una dirección IP fija fuera del rango dinámico.  
  *Bloque de reserva en `/etc/dhcp/dhcpd.conf`:*
  ```option
  host vegeta {
    hardware ethernet 42:55:F5:A5:15:E7;
    fixed-address 192.168.10.50;
  }
  ```

---

### SOLUCIONES DEL BLOQUE III: RA3 (Servidores Web, HTTP/HTTPS, NAT)

#### Respuestas Test RA3:
* **Pregunta 11: C) El recurso solicitado ha sido procesado y redirigido permanentemente a una nueva localización.**  
  *Justificación:* Los códigos 3xx indican redirección; por ejemplo, 301 es *Moved Permanently*.  
* **Pregunta 12: B) Método POST.**  
  *Justificación:* El método POST envía los datos en el cuerpo de la petición HTTP, garantizando mayor capacidad y privacidad que el método GET.  
* **Pregunta 13: A) `a2ensite dbs.conf` y `a2enmod ssl`**  
  *Justificación:* Los scripts helper de Apache en Debian/Ubuntu son `a2ensite` (enable site) y `a2enmod` (enable module).  
* **Pregunta 14: B) Apache2: `DocumentRoot` | Nginx: `root`**  
  *Justificación:* `DocumentRoot` en Apache y `root` en Nginx definen el directorio del sistema donde residen las páginas HTML.  
* **Pregunta 15: B) `sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE`**  
  *Justificación:* Aplica el enmascaramiento de direcciones IP (NAT) a la salida por la interfaz WAN `enp0s3`.

#### Solución Preguntas de Desarrollo RA3:

* **Solución Pregunta D5 [RA3]:**  
  *Archivo `/etc/apache2/sites-available/dbs.sri.local.conf`:*
  ```apache
  <VirtualHost *:80>
      ServerName dbs.sri.local
      Redirect permanent / https://dbs.sri.local/
  </VirtualHost>

  <VirtualHost *:443>
      ServerName dbs.sri.local
      DocumentRoot /var/www/dbs

      SSLEngine on
      SSLCertificateFile /etc/ssl/certs/dragonball.crt
      SSLCertificateKeyFile /etc/ssl/private/dragonball.key

      ErrorLog ${APACHE_LOG_DIR}/dbs_error.log
      CustomLog ${APACHE_LOG_DIR}/dbs_access.log combined
  </VirtualHost>
  ```

* **Solución Pregunta D6 [RA3]:**  
  1. **Habilitación de IP Forwarding:** Editar `/etc/sysctl.conf`, descomentar `net.ipv4.ip_forward=1` y ejecutar `sudo sysctl -p`.  
  2. **Regla NAT IPTables:** Ejecutar `sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE` para sustituir la IP privada origen por la IP de la WAN.  
  3. **Persistencia tras reinicio:** Instalar `iptables-persistent` y guardar el estado actual con `sudo netfilter-persistent save` o `sudo dpkg-reconfigure iptables-persistent`.
