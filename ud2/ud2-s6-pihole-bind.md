# Integración de BIND y Pi-hole en la misma red

Sí, **se pueden usar BIND y Pi-hole a la vez**, y de hecho es una combinación excelente y muy habitual en entornos de red avanzados o domésticos donde se busca un control total de la resolución de nombres (DNS).

Para que funcionen juntos sin pisarse, lo ideal es entender qué papel desempeña cada uno y cómo configurarlos en **cascada**.

---

## ¿Cómo deben organizarse?

Tanto BIND como Pi-hole actúan como servidores DNS, pero tienen objetivos diferentes:

* **Pi-hole:** Está pensado principalmente para el bloqueo de publicidad, rastreadores y control de consultas a nivel de red (además de ofrecer una interfaz web excelente y estadísticas).
* **BIND:** Es un servidor DNS completo, autoritativo y recursivo, ideal para gestionar tus propias zonas locales (por ejemplo, tu propio dominio en tu red) y reenviar consultas al exterior.

### Flujo de la resolución DNS

```
[ Cliente ] ──(Petición DNS)──> [ Pi-hole (Puerto 53) ]
                                         │
                         ┌───────────────┴───────────────┐
                         ▼                               ▼
                 (Si es publicidad)            (Si no es publicidad)
                         │                               │
                   [ Bloqueado ]                         ▼
                                               [ BIND (Puerto 5353) ]
                                                         │
                                         ┌───────────────┴───────────────┐
                                         ▼                               ▼
                                 (Si es zona local)            (Si es consulta externa)
                                         │                               │
                                  [ Responde BIND ]                      ▼
                                                                [ Servidores Raíz / IP Pública ]
```

1. **Pi-hole como DNS principal de la red:** Los clientes de tu red (ordenadores, móviles, dispositivos IoT) envían sus peticiones DNS directamente a Pi-hole.
2. **BIND como "Upstream" (servidor superior) de Pi-hole:** Cuando Pi-hole recibe una consulta que no está bloqueada ni en su caché, en lugar de enviarla directamente a servidores externos como los de Cloudflare (`1.1.1.1`) o Google (`8.8.8.8`), **la reenvía a BIND**.
3. **BIND resuelve o reenvía:** 
   * Si la consulta es para un dominio de tus zonas locales (por ejemplo, tu dominio personal), BIND la resuelve directamente.
   * Si es para internet (como `google.com`), BIND se encarga de realizar la consulta recursiva hacia los servidores raíz o los reenviadores públicos que hayas configurado (`forwarders`).

---

## Ventajas de esta configuración

* **Gestión de dominios locales:** Puedes seguir manteniendo tus registros locales (como `aldarias.es` o tus servicios en red local) con la potencia y flexibilidad de BIND.
* **Bloqueo de publicidad intacto:** Pi-hole sigue filtrando la publicidad y el malware para todos los dispositivos de la casa.
* **Privacidad y control:** Todo el tráfico DNS pasa primero por tus propias herramientas locales antes de salir al exterior.

---

## Puntos clave a tener en cuenta en la configuración

### 1. Conflictos de puertos
Tanto BIND como Pi-hole (a través de su componente `FTL` / `dnsmasq`) necesitan escuchar en el **puerto 53 (UDP/TCP)** por defecto. 

* **Pi-hole** debe seguir en el puerto 53 para atender a los clientes de la red.
* **BIND** debe configurarse para escuchar en una IP distinta dentro de tu máquina o en un puerto alternativo (por ejemplo, `127.0.0.1#5353` o en otra IP interna de tu servidor si está en un contenedor/LXC diferente).

### 2. Configuración en Pi-hole
En la interfaz web de Pi-hole:
1. Ve a **Settings > DNS**.
2. Desactiva los proveedores externos predeterminados (Cloudflare, Google, etc.).
3. Añade la IP y puerto donde escucha BIND como **Custom Upstream DNS** (por ejemplo, `127.0.0.1#5353`).

### 3. Configuración en BIND (`named.conf.options`)
Asegúrate de configurar correctamente las opciones básicas de BIND para permitir la recursión desde Pi-hole:

```named
options {
    directory "/var/cache/bind";

    // Puerto alternativo si BIND comparte la misma IP local con Pi-hole
    listen-on port 5353 { 127.0.0.1; };

    allow-query { localhost; 192.168.1.0/24; };
    recursion yes;

    // Servidores DNS externos para resolución pública
    forwarders {
        1.1.1.1;
        8.8.8.8;
    };
};
```