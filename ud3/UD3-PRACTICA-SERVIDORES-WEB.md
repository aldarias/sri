# Práctica Unidad 3: Servicios Web (Servicios de Red e Internet - ASIR)

---

## **PRÁCTICA 1: INSTALACIÓN Y CONFIGURACIÓN DE APACHE2 EN UBUNTU**

### **Objetivo**
Configurar un servidor web Apache2 en Ubuntu Server para alojar dos sitios web independientes (`sri.local` y `shop.sri.local`), primero mediante protocolo seguro/inseguro HTTP/HTTPS y haciendo uso de Hosts Virtuales (VirtualHosts) y certificados digitales autofirmados.

---

### **Enunciado**

1. **Instalación de Apache2:** Actualizar los repositorios e instalar el paquete `apache2`.
2. **Estructura de Directorios:** Crear las carpetas necesarias para alojar los dos sitios web:
   - `/var/www/sri.local/www/`
   - `/var/www/sri.local/shop/`
   - Asignar permisos y propietario adecuados.
3. **Páginas de prueba:** Crear un archivo `index.html` básico en cada uno de los directorios para verificar el funcionamiento.
4. **Hosts Virtuales (HTTP - Puerto 80):** Configurar dos Virtual Hosts (`sri.local` y `shop.sri.local`), habilitarlos y comprobar su funcionamiento.
5. **Servicios HTTPS (Puerto 443):**
   - Habilitar el módulo SSL de Apache (`mod_ssl`).
   - Generar un certificado digital X.509 autofirmado y su clave privada mediante OpenSSL.
   - Configurar la redirección de HTTP (80) a HTTPS (443).
   - Configurar el tráfico seguro SSL/TLS en ambos Virtual Hosts.

---

### **Solución Paso a Paso**

#### **Paso 1: Instalación de Apache2**
Actualizamos la lista de paquetes de la distribución e instalamos el servidor web Apache:

```bash
sudo apt update
sudo apt install apache2 -y
```

#### **Paso 2: Creación de la estructura de directorios y permisos**
Creamos las rutas indicadas en el enunciado para los dos sitios web:

```bash
sudo mkdir -p /var/www/sri.local/www
sudo mkdir -p /var/www/sri.local/shop
```

Asignamos como propietario al usuario actual y al grupo `www-data` (o `$USER:$USER`), e imponemos permisos estándar `755`:

```bash
sudo chown -R $USER:$USER /var/www/sri.local
sudo chmod -R 755 /var/www/sri.local
```

#### **Paso 3: Creación de los archivos `index.html` de prueba**

**1. Archivo para el sitio web principal (`/var/www/sri.local/www/index.html`):**

```bash
nano /var/www/sri.local/www/index.html
```

*Contenido:*
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sitio Web www</title>
</head>
<body>
    <h1>Hola Mundo desde www.sri.local</h1>
</body>
</html>
```

**2. Archivo para el sitio web tienda (`/var/www/sri.local/shop/index.html`):**

```bash
nano /var/www/sri.local/shop/index.html
```

*Contenido:*
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sitio Web Shop</title>
</head>
<body>
    <h1>Hola Mundo desde shop.sri.local</h1>
</body>
</html>
```

---

#### **Paso 4: Configuración de Hosts Virtuales HTTP (Puerto 80)**

Creamos los archivos de configuración dentro de `/etc/apache2/sites-available/`.

**1. Archivo `/etc/apache2/sites-available/sri.local.conf`:**

```apache
<VirtualHost *:80>
    ServerAdmin soporte@sri.local
    ServerName sri.local
    ServerAlias www.sri.local
    DocumentRoot /var/www/sri.local/www

    ErrorLog ${APACHE_LOG_DIR}/sri.local.err.log
    CustomLog ${APACHE_LOG_DIR}/sri.local.access.log combined
</VirtualHost>
```

**2. Archivo `/etc/apache2/sites-available/shop.sri.local.conf`:**

```apache
<VirtualHost *:80>
    ServerAdmin soporte@sri.local
    ServerName shop.sri.local
    ServerAlias www.shop.sri.local
    DocumentRoot /var/www/sri.local/shop

    ErrorLog ${APACHE_LOG_DIR}/shop.sri.local.err.log
    CustomLog ${APACHE_LOG_DIR}/shop.sri.local.access.log combined
</VirtualHost>
```

**3. Validar sintaxis, activar sitios y reiniciar Apache:**

```bash
# Validar sintaxis de archivos de configuración
sudo apache2ctl configtest

# Habilitar los sitios
sudo a2ensite sri.local.conf
sudo a2ensite shop.sri.local.conf

# Deshabilitar el sitio por defecto (opcional)
sudo a2dissite 000-default.conf

# Reiniciar servicio
sudo systemctl restart apache2
```

---

#### **Paso 5: Configuración de HTTPS y Certificados SSL/TLS**

**1. Generación del certificado autofirmado:**

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/sri.local-selfsigned.key \
  -out /etc/ssl/certs/sri.local-selfsigned.crt
```

**2. Habilitar módulo SSL:**

```bash
sudo a2enmod ssl
```

**3. Modificación del archivo de configuración `sri.local.conf` (HTTP + Redirección a HTTPS):**

```bash
sudo nano /etc/apache2/sites-available/sri.local.conf
```

*Contenido completo:*
```apache
<VirtualHost *:80>
    ServerAdmin soporte@sri.local
    ServerName sri.local
    ServerAlias www.sri.local
    
    # Redirección de puerto 80 a 443
    Redirect permanent / https://sri.local/
</VirtualHost>

<VirtualHost *:443>
    ServerAdmin soporte@sri.local
    ServerName sri.local
    ServerAlias www.sri.local
    DocumentRoot /var/www/sri.local/www

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/sri.local-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/sri.local-selfsigned.key

    ErrorLog ${APACHE_LOG_DIR}/sri.local.error.log
    CustomLog ${APACHE_LOG_DIR}/sri.local.access.log combined
</VirtualHost>
```

**4. Modificación del archivo de configuración `shop.sri.local.conf`:**

```bash
sudo nano /etc/apache2/sites-available/shop.sri.local.conf
```

*Contenido completo:*
```apache
<VirtualHost *:80>
    ServerAdmin soporte@sri.local
    ServerName shop.sri.local
    ServerAlias www.shop.sri.local

    # Redirección de puerto 80 a 443
    Redirect permanent / https://shop.sri.local/
</VirtualHost>

<VirtualHost *:443>
    ServerAdmin soporte@sri.local
    ServerName shop.sri.local
    ServerAlias www.shop.sri.local
    DocumentRoot /var/www/sri.local/shop

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/sri.local-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/sri.local-selfsigned.key

    ErrorLog ${APACHE_LOG_DIR}/shop.sri.local.error.log
    CustomLog ${APACHE_LOG_DIR}/shop.sri.local.access.log combined
</VirtualHost>
```

**5. Verificación y reinicio final:**

```bash
sudo apache2ctl configtest
sudo systemctl restart apache2
sudo systemctl status apache2
```

---
---

## **PRÁCTICA 2: INSTALACIÓN Y CONFIGURACIÓN DE NGINX EN UBUNTU**

### **Objetivo**
Instalar el servidor web Nginx en la misma máquina Ubuntu reutilizando las carpetas y páginas index existentes (`/var/www/sri.local/www` y `/var/www/sri.local/shop`), configurando los bloques `server` para los protocolos HTTP y HTTPS.

> **Nota previa:** Antes de realizar esta práctica, se debe detener o deshabilitar Apache para evitar conflictos de puerto 80 y 443 (`sudo systemctl stop apache2`).

---

### **Enunciado**

1. **Instalación de Nginx:** Actualizar e instalar el servicio Nginx en Ubuntu Server.
2. **Reutilización del DocumentRoot:** Usar los directorios creados en la Práctica 1:
   - `/var/www/sri.local/www/`
   - `/var/www/sri.local/shop/`
3. **Configuración HTTP y HTTPS:**
   - Configurar los bloques `server` para HTTP (puerto 80) que redirijan automáticamente a HTTPS (puerto 443).
   - Generar/Asignar los certificados SSL autofirmados para el servidor web seguro en Nginx.
   - Comprobar sintaxis y estado del servicio.

---

### **Solución Paso a Paso**

#### **Paso 1: Detener Apache e Instalar Nginx**

```bash
# Detener servicio Apache para liberar puertos 80 y 443
sudo systemctl stop apache2
sudo systemctl disable apache2

# Instalar Nginx
sudo apt update
sudo apt install nginx -y
```

#### **Paso 2: Certificado SSL para Nginx**
Generamos un certificado SSL para Nginx (o podemos utilizar el creado en la Práctica 1):

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/nginx-sri.local.key \
  -out /etc/ssl/certs/nginx-sri.local.crt
```

---

#### **Paso 3: Configuración de los Server Blocks en Nginx**

Creamos los archivos de configuración dentro del directorio `/etc/nginx/conf.d/` o `/etc/nginx/sites-available/`.

**1. Configuración para `sri.local` (`/etc/nginx/conf.d/sri.local.conf`):**

```bash
sudo nano /etc/nginx/conf.d/sri.local.conf
```

*Contenido:*
```nginx
# Servidor HTTP: Redirección permanente a HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name sri.local www.sri.local;

    return 301 https://$host$request_uri;
}

# Servidor HTTPS
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name sri.local www.sri.local;

    root /var/www/sri.local/www;
    index index.html index.htm;

    ssl_certificate /etc/ssl/certs/nginx-sri.local.crt;
    ssl_certificate_key /etc/ssl/private/nginx-sri.local.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        try_files $uri $uri/ =404;
    }

    error_log /var/log/nginx/sri.local.error.log;
    access_log /var/log/nginx/sri.local.access.log;
}
```

**2. Configuración para `shop.sri.local` (`/etc/nginx/conf.d/shop.sri.local.conf`):**

```bash
sudo nano /etc/nginx/conf.d/shop.sri.local.conf
```

*Contenido:*
```nginx
# Servidor HTTP: Redirección permanente a HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name shop.sri.local www.shop.sri.local;

    return 301 https://$host$request_uri;
}

# Servidor HTTPS
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name shop.sri.local www.shop.sri.local;

    root /var/www/sri.local/shop;
    index index.html index.htm;

    ssl_certificate /etc/ssl/certs/nginx-sri.local.crt;
    ssl_certificate_key /etc/ssl/private/nginx-sri.local.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        try_files $uri $uri/ =404;
    }

    error_log /var/log/nginx/shop.sri.local.error.log;
    access_log /var/log/nginx/shop.sri.local.access.log;
}
```

---

#### **Paso 4: Comprobación y Comprobación en DNS Local / `/etc/hosts`**

**1. Verificar sintaxis y reiniciar servicio Nginx:**

```bash
# Validar la configuración
sudo nginx -t

# Reiniciar el servicio
sudo systemctl restart nginx
sudo systemctl status nginx
```

**2. Resolución de nombres en el cliente (Prueba local `/etc/hosts`):**

En la máquina cliente desde la cual probaremos la conexión (o localmente en el servidor), añadimos las líneas en `/etc/hosts`:

```bash
sudo nano /etc/hosts
```

*Añadir:*
```plaintext
127.0.0.1   sri.local www.sri.local
127.0.0.1   shop.sri.local www.shop.sri.local
```

**3. Verificación de funcionamiento mediante comandos `curl`:**

```bash
# Probar la redirección HTTP -> HTTPS
curl -I http://sri.local

# Probar la respuesta en HTTPS (añadir -k para omitir error por certificado autofirmado)
curl -k https://sri.local
curl -k https://shop.sri.local
```