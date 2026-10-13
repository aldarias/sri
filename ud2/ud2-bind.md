# UD2-S5: BIND y cómo abordar la actividad 2

- **Módulo:** Servicios de Red e Internet (SRI)
- **Ciclo:** FP Grado Superior en Administración de Sistemas Informáticos en Red (ASIR)
- **Autor:** Paco Aldarias
- **Fecha:** 10/10/2026

## 1. ¿Qué es DNS?

El DNS (Domain Name System) es el sistema que transforma nombres humanos en direcciones IP y viceversa.

Ejemplo:

- `www.google.es` -> `142.250.184.196`
- `192.168.1.10` -> `servidor.local`

Sin DNS, tendríamos que recordar direcciones IP en lugar de nombres de dominio.

### Conceptos básicos

- Dominio: parte del nombre que identifica un conjunto de equipos. Ejemplo: `ejemplo.com`.
- Subdominio: dominio que cuelga de otro. Ejemplo: `intranet.ejemplo.com`.
- FQDN: Fully Qualified Domain Name, nombre completo de un equipo. Ejemplo: `www.ejemplo.com.`
- Zona: conjunto de registros DNS asociados a un dominio.
- Resolver: programa o servicio que hace la consulta DNS.
- Servidor DNS recursivo: responde a la consulta del cliente y busca la información en otros servidores si hace falta.
- Servidor DNS autoritativo: tiene la información oficial de una zona.
- TTL: Time To Live, tiempo durante el cual una respuesta puede cachearse.

---

## 2. ¿Qué es BIND?

BIND (Berkeley Internet Name Domain) es el software más usado para montar servidores DNS en Linux.

Su función principal es:

- servir zonas DNS,
- responder consultas de clientes,
- reenviar peticiones si no conoce la respuesta,
- gestionar registros de nombre y dirección.

En sistemas Debian/Ubuntu se suele instalar con:

```bash
sudo apt install bind9 bind9utils
```

### Archivos importantes de BIND

- `/etc/bind/named.conf.options` -> configuración global
- `/etc/bind/named.conf.local` -> declaración de zonas
- `/etc/bind/db.*` -> ficheros de zona

---

## 3. ¿Qué hace un servidor DNS?

Un servidor DNS puede actuar de dos formas:

### 3.1. Servidor recursivo
Cuando un cliente pregunta por `www.ies.es`, el servidor recursivo intenta resolver la consulta consultando otros servidores DNS si es necesario.

### 3.2. Servidor autoritativo
Cuando el servidor tiene la zona local, responde con la información que guarda en sus archivos de zona.

Esto es lo que ocurre en una red local: el servidor DNS interno responde sobre un dominio de la empresa o del aula.

---

## 4. Tipos de registros DNS más importantes

### SOA
Registro de inicio de autoridad. Indica que ese servidor es el responsable de la zona.

Ejemplo:

```bind
@ IN SOA ns1.ejemplo.local. admin.ejemplo.local. (
    2026101001 ; serial
    3600       ; refresh
    1800       ; retry
    604800     ; expire
    86400      ; negative cache TTL
)
```

#### Conceptos dentro de SOA

- `@` = dominio actual
- `IN` = clase Internet
- `SOA` = Start of Authority
- `ns1.ejemplo.local.` = servidor principal de la zona
- `admin.ejemplo.local.` = dirección de contacto
- `serial` = versión de la zona

### NS
Define qué servidores son responsables de la zona.

```bind
@ IN NS ns1.ejemplo.local.
```

### A
Asocia un nombre a una dirección IPv4.

```bind
ns1 IN A 10.10.10.10
web IN A 10.10.10.20
```

### AAAA
Asocia un nombre a una dirección IPv6.

```bind
ns1 IN AAAA 2001:db8::10
```

### CNAME
Crea un alias. Se usa cuando un nombre apunta a otro nombre, no a una IP directa.

```bind
www IN CNAME web.ejemplo.local.
```

Esto significa:

- `www.ejemplo.local` no tiene IP propia,
- apunta a `web.ejemplo.local`,
- la resolución final llega al `A` de `web`.

### MX
Define el servidor de correo del dominio.

```bind
@ IN MX 10 mail.ejemplo.local.
```

Significa:

- el dominio acepta correo,
- `mail.ejemplo.local` es el servidor de correo,
- la prioridad `10` indica mayor preferencia que otra posible entrada.

### PTR
Se usa en la zona inversa para convertir una IP en nombre.

```bind
20 IN PTR web.ejemplo.local.
```

Esto permite responder consultas como:

```bash
dig -x 10.10.10.20
```

---

## 5. ¿Qué es una zona?

Una zona es el conjunto de registros DNS que pertenecen a un dominio concreto.

Existen dos tipos principales:

### Zona directa
Resuelve nombres a direcciones IP.

Ejemplo:

```text
www.ejemplo.local -> 10.10.10.20
```

Archivo típico:

```text
/etc/bind/db.ejemplo.local
```

### Zona inversa
Resuelve IP a nombre.

Ejemplo:

```text
10.10.10.20 -> web.ejemplo.local
```

Archivo típico:

```text
/etc/bind/db.10.10.10
```

La zona inversa se declara con un nombre especial:

```text
10.10.10.in-addr.arpa
```

porque la dirección `10.10.10.20` se representa al revés en la zona inversa: `20` dentro del bloque `10.10.10`.

---

## 6. ¿Qué significa `allow-query` y `forwarders`?

### `allow-query`
Define quién puede hacer consultas al servidor.

Ejemplo:

```bind
allow-query { localhost; 10.10.10.0/24; };
```

Esto permite que solo el propio equipo y la red local consulten al servidor.

### `forwarders`
Cuando el servidor no sabe la respuesta, puede delegar la búsqueda en servidores externos.

Ejemplo:

```bind
forwarders {
    8.8.8.8;
    1.1.1.1;
};
```

Esto se usa para resolver sitios externos y no dejar que el servidor lo haga todo por sí mismo.

---

## 7. Cómo realizar la actividad 2: enfoque general

La práctica consiste en montar un servidor DNS interno con una zona directa y otra inversa.

### Ejemplo distinto al de la práctica

Vamos a usar un dominio ficticio llamado `campus.local` y una red `172.20.0.0/24`.

- Red local: `172.20.0.0/24`
- Servidor DNS: `ns1.campus.local` -> `172.20.0.10`
- Puerta de enlace: `gw.campus.local` -> `172.20.0.1`
- Web: `web.campus.local` -> `172.20.0.20`
- Correo: `mail.campus.local` -> `172.20.0.30`

La estructura es parecida a la práctica, pero con otros nombres y otra red, para que el ejemplo siga siendo claro sin repetir la tarea entregable.

---

## 8. Paso 1: Configurar las opciones globales

Edita el archivo:

```bash
sudo nano /etc/bind/named.conf.options
```

Añade algo parecido a esto:

```bind
options {
    directory "/var/cache/bind";

    forwarders {
        8.8.8.8;
        1.1.1.1;
    };

    allow-query { localhost; 172.20.0.0/24; };

    auth-nxdomain no;
    listen-on-v6 { any; };
};
```

### Qué hace esto

- `forwarders`: resuelve fuera de la red local si no conoce la respuesta.
- `allow-query`: limita qué equipos pueden preguntar al servicio.
- `directory`: carpeta de trabajo de BIND.

---

## 9. Paso 2: Declarar las zonas

Edita:

```bash
sudo nano /etc/bind/named.conf.local
```

Añade:

```bind
zone "campus.local" {
    type master;
    file "/etc/bind/db.campus.local";
};

zone "0.20.172.in-addr.arpa" {
    type master;
    file "/etc/bind/db.172.20.0";
};
```

### ¿Por qué la zona inversa tiene ese nombre?

La dirección `172.20.0.20` se escribe en la zona inversa como `20.0.20.172.in-addr.arpa`.

Pero en los ficheros de BIND se suele abbreviar la red a:

```text
0.20.172.in-addr.arpa
```

porque la red es `172.20.0.0/24` y se trabaja solo con el bloque `172.20.0`.

---

## 10. Paso 3: Crear la zona directa

Crea el fichero:

```bash
sudo nano /etc/bind/db.campus.local
```

Contenido:

```bind
$TTL    604800
@       IN      SOA     ns1.campus.local. admin.campus.local. (
                              2026101001 ; Serial
                                  604800 ; Refresh
                                   86400 ; Retry
                                 2419200 ; Expire
                                  604800 ) ; Negative Cache TTL
;
@       IN      NS      ns1.campus.local.
@       IN      MX      10 mail.campus.local.

ns1     IN      A       172.20.0.10
gw      IN      A       172.20.0.1
web     IN      A       172.20.0.20
mail    IN      A       172.20.0.30

www     IN      CNAME   web.campus.local.
```

### Explicación

- `SOA`: indica autoridad sobre la zona.
- `NS`: nombra los servidores de nombres.
- `MX`: indica el servidor de correo.
- `A`: resuelve nombre a IPv4.
- `CNAME`: alias para `www` -> `web`.

---

## 11. Paso 4: Crear la zona inversa

Crea el fichero:

```bash
sudo nano /etc/bind/db.172.20.0
```

Contenido:

```bind
$TTL    604800
@       IN      SOA     ns1.campus.local. admin.campus.local. (
                              2026101001 ; Serial
                                  604800 ; Refresh
                                   86400 ; Retry
                                 2419200 ; Expire
                                  604800 ) ; Negative Cache TTL
;
@       IN      NS      ns1.campus.local.

10      IN      PTR     ns1.campus.local.
1       IN      PTR     gw.campus.local.
20      IN      PTR     web.campus.local.
30      IN      PTR     mail.campus.local.
```

### ¿Qué hace PTR?

PTR transforma una IP en nombre. Por ejemplo:

```text
172.20.0.20 -> web.campus.local
```

Es lo que hace `dig -x 172.20.0.20`.

---

## 12. Paso 5: Validar la configuración

Antes de arrancar el servicio, hay que comprobar que no hay errores.

```bash
sudo named-checkconf
sudo named-checkzone campus.local /etc/bind/db.campus.local
sudo named-checkzone 0.20.172.in-addr.arpa /etc/bind/db.172.20.0
```

### ¿Qué pasa si hay error?

Normalmente aparece algo como:

- falta de `;`
- nombre sin punto final
- zona mal declarada
- archivo de zona con sintaxis incorrecta

---

## 13. Paso 6: Reiniciar el servicio

```bash
sudo systemctl restart bind9
```

O, en algunos sistemas:

```bash
sudo systemctl restart named
```

---

## 14. Paso 7: Probar consultas DNS

### Consulta A
Resuelve la dirección del alias `www`:

```bash
dig @172.20.0.10 www.campus.local +short
```

Resultado esperado:

```text
web.campus.local.
172.20.0.20
```

### Consulta MX
Comprueba el correo del dominio:

```bash
dig @172.20.0.10 campus.local MX +short
```

Resultado esperado:

```text
10 mail.campus.local.
```

### Consulta PTR
Comprueba la resolución inversa:

```bash
dig @172.20.0.10 -x 172.20.0.20 +short
```

Resultado esperado:

```text
web.campus.local.
```

---

## 15. Resumen de la idea principal

BIND es el servicio que nos permite:

- responder consultas de nombre a IP,
- responder consultas inversas de IP a nombre,
- organizar la información en zonas,
- gestionar aliases, correo y servidores de nombres.

En la práctica de la UD2 se realiza exactamente esto, pero con los datos concretos del dominio de la empresa o del escenario asignado.

La diferencia entre la práctica y este ejemplo es solo el dominio, la red y los datos, no el procedimiento.

---

## 16. Mini checklist para la actividad

1. Instalar BIND9.
2. Configurar `named.conf.options` con `forwarders` y `allow-query`.
3. Declarar la zona directa e inversa en `named.conf.local`.
4. Crear `db.*` con SOA, NS, A, CNAME y MX.
5. Crear la zona inversa con PTR.
6. Validar con `named-checkconf` y `named-checkzone`.
7. Reiniciar el servicio.
8. Comprobar con `dig`.

---

## 17. Conclusión

BIND permite crear un servidor DNS completo y útil en una red local. Entender bien los conceptos de zona, registros y consulta DNS es clave para resolver la actividad 2 de forma correcta.

No se trata solo de escribir archivos, sino de comprender qué expresa cada línea del fichero de configuración y qué consulta quiere resolver el cliente cuando pregunta por un nombre o por una IP.
