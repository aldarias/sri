# Actividad Evaluable 2: Despliegue de Servidor DNS BIND9 en Linux

- **Módulo:** Servicios de Red e Internet (SRI)
- **Ciclo:** FP Grado Superior en Administración de Sistemas Informáticos en Red (ASIR)
- **Tiempo estimado:** 60 minutos
- **Puntuación máxima:** 10 puntos

---

## Escenario de Trabajo

La empresa *TechCorp* requiere la puesta en marcha de un servidor DNS interno en su infraestructura Linux. Se te ha asignado la tarea de configurar el servidor BIND9 en un sistema operativo Debian/Ubuntu Server para gestionar la zona local `techcorp.lan` correspondiente a la red `192.168.100.0/24`.

### Datos de la Red
* **Red local:** `192.168.100.0/24`
* **Servidor DNS (`ns1`):** `192.168.100.10`
* **Puerta de enlace (`gw`):** `192.168.100.1`
* **Servidor Web (`web`):** `192.168.100.20`
* **Servidor de Correo (`mail`):** `192.168.100.30`

---

## Tareas a Realizar

### Tarea 1: Configuración Global y Reenviadores (1,5 puntos)
Edita el fichero de opciones globales de BIND9 (`/etc/bind/named.conf.options`) para cumplir con las siguientes especificaciones:
1. Configura reenviadores (*forwarders*) a las IPs `8.8.8.8` y `1.1.1.1`.
2. Permite las consultas DNS (`allow-query`) exclusivamente a la red local `192.168.100.0/24` y a la interfaz local (`localhost`).

### Tarea 2: Declaración y Fichero de Zona Directa (4 puntos)
1. Declara la zona directa `techcorp.lan` en el fichero `/etc/bind/named.conf.local`.
2. Crea el fichero de zona correspondiente (`/etc/bind/db.techcorp.lan`) e incluye los siguientes registros:
   * **SOA y NS:** Servidor de nombres principal `ns1.techcorp.lan`. Email de contacto: `admin.techcorp.lan`.
   * **Registros A:** 
     * `ns1` -> `192.168.100.10`
     * `gw` -> `192.168.100.1`
     * `web` -> `192.168.100.20`
     * `mail` -> `192.168.100.30`
   * **Registro CNAME:** `www` que apunte al equipo `web.techcorp.lan`.
   * **Registro MX:** `techcorp.lan` con prioridad 10 apuntando a `mail.techcorp.lan`.

### Tarea 3: Declaración y Fichero de Zona Inversa (2,5 puntos)
1. Declara la zona de resolución inversa correspondiente a la red `192.168.100.0/24` (`100.168.192.in-addr.arpa`) en `/etc/bind/named.conf.local`.
2. Crea el fichero de zona inversa (`/etc/bind/db.192.168.100`) e incluye los siguientes registros:
   * **SOA y NS:** Mismos parámetros que la zona directa.
   * **Registros PTR:**
     * `10` -> `ns1.techcorp.lan.`
     * `1` -> `gw.techcorp.lan.`
     * `20` -> `web.techcorp.lan.`
     * `30` -> `mail.techcorp.lan.`

### Tarea 4: Comprobación y Verificación (2 puntos)
1. Verifica la sintaxis de las zonas y de los ficheros de configuración mediante las herramientas nativas de BIND9 (`named-checkconf` y `named-checkzone`).
2. Reinicia el servicio `named` (o `bind9`).
3. Desde un equipo cliente o desde el propio servidor, ejecuta las siguientes consultas con `dig` o `nslookup` guardando las capturas o salidas de texto en el informe de entrega:
   * Consulta de dirección A para `www.techcorp.lan`.
   * Consulta inversa PTR para la IP `192.168.100.20`.
   * Consulta de registros de correo MX para el dominio `techcorp.lan`.

---

## Rúbrica de Evaluación

| Criterio | Excelente (100%) | Aceptable (50%-75%) | Insuficiente (0%-25%) | Puntos |
| :--- | :--- | :--- | :--- | :---: |
| **Configuración Global (Opciones)** | Reenviadores y control de acceso (`allow-query`) configurados sin errores sintácticos. | Configuración parcial (falta reenviador o permisos de consulta). | No se ha modificado o genera fallos al arrancar el servicio. | **1.5** |
| **Zona Directa** | Declaración correcta en `named.conf.local`. Fichero de zona completo con SOA, NS, A, CNAME y MX válidos. | Fichero de zona funcional con pequeñas deficiencias (ej. olvidó el punto final en un FQDN). | Zona mal declarada o con errores graves de sintaxis que impiden la carga. | **4.0** |
| **Zona Inversa** | Declaración e implementación correcta de registros PTR mapeados adecuadamente a sus FQDNs. | Zona creada con errores menores en el formato IP/PTR o nombres no totalmente cualificados. | La resolución inversa no funciona o no se ha configurado la zona. | **2.5** |
| **Validación y Diagnóstico** | Pasa las herramientas `named-checkconf/checkzone` y adjunta pruebas correctas de consultas DNS (`dig`). | El servicio arranca pero faltan pruebas de verificación o se usaron parámetros incorrectos. | No se verifica la configuración ni se aportan evidencias de funcionamiento. | **2.0** |

---

## Formato de Entrega
Se entregará un único documento en formato PDF o Markdown que contenga los bloques de código o capturas de pantalla de los ficheros de configuración editados y las salidas de los comandos de verificación ejecutados en la Tarea 4.