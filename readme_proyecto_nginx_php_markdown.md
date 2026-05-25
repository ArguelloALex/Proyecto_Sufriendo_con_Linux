# Proyecto NGINX + PHP Compilado desde Código Fuente

## Integrantes
- Alexis Osorno

---

# Objetivo
Implementar un servidor PHP compilado desde código fuente en la versión 8.4.x utilizando NGINX como servidor web en Linux.

El proyecto debe permitir la comunicación entre PHP-FPM y NGINX, además de soportar procesamiento de imágenes, fechas e internacionalización.

---

# Requisitos del Proyecto

- PHP 8.4.x compilado desde código fuente.
- Instalación en `/srv/nginx`.
- Usuario y grupo del servicio: `nginx`.
- Soporte para PHP-FPM.
- Comunicación funcional entre NGINX y PHP.
- Extensiones habilitadas:
  - GD (imágenes)
  - Intl (internacionalización)
  - Date/Time

---

# Instalación de Dependencias

## Actualización del sistema

```bash
sudo apt update && sudo apt upgrade -y
```

## Instalación de herramientas necesarias

```bash
sudo apt install build-essential wget curl git unzip -y
```

## Librerías necesarias para PHP

```bash
sudo apt install libxml2-dev libsqlite3-dev libssl-dev \
libcurl4-openssl-dev libjpeg-dev libpng-dev libonig-dev \
libzip-dev libicu-dev pkg-config zlib1g-dev -y
```

---

# Descarga de PHP

```bash
cd /usr/local/src
wget https://www.php.net/distributions/php-8.4.0.tar.gz
```

## Descompresión

```bash
tar -xvzf php-8.4.0.tar.gz
cd php-8.4.0
```

---

# Configuración y Compilación de PHP

```bash
./configure --prefix=/srv/nginx/php \
--enable-fpm \
--with-fpm-user=nginx \
--with-fpm-group=nginx \
--with-curl \
--with-openssl \
--with-zlib \
--enable-mbstring \
--with-jpeg \
--with-png \
--enable-gd \
--enable-intl
```

## Compilación

```bash
make
```

## Instalación

```bash
sudo make install
```

---

# Configuración de PHP-FPM

## Copiar archivos de configuración

```bash
cp sapi/fpm/php-fpm.conf /srv/nginx/php/etc/php-fpm.conf
cp /srv/nginx/php/etc/php-fpm.d/www.conf.default \
/srv/nginx/php/etc/php-fpm.d/www.conf
```

## Iniciar PHP-FPM

```bash
/srv/nginx/php/sbin/php-fpm
```

---

# Configuración de NGINX

## Archivo de configuración

```nginx
server {
    listen 80;
    server_name localhost;

    root /srv/nginx/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        include fastcgi_params;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

---

# Prueba de Funcionamiento

## Crear archivo PHP

```bash
nano /srv/nginx/html/index.php
```

Contenido:

```php
<?php
phpinfo();
?>
```

---

# Problemas Encontrados

## Error con CURL

Durante la instalación se presentaron problemas relacionados con la descarga de archivos mediante `curl`.

### Solución

Se instalaron nuevamente las dependencias:

```bash
sudo apt install curl wget -y
```

---

## Error de compilación de PHP

La compilación falló debido a librerías faltantes.

### Solución

Se instalaron las librerías necesarias manualmente:

```bash
sudo apt install libxml2-dev libicu-dev libpng-dev -y
```

---

## Error de conexión entre NGINX y PHP-FPM

NGINX no podía interpretar archivos PHP.

### Solución

Se verificó que PHP-FPM estuviera activo:

```bash
ps aux | grep php-fpm
```

Y se configuró correctamente `fastcgi_pass`.

---

# Evidencias

## Capturas sugeridas

- Instalación de dependencias.
- Compilación de PHP.
- Configuración de NGINX.
- Ejecución de PHP-FPM.
- Resultado de `phpinfo()`.

---

# Conclusión

Se logró implementar correctamente un entorno funcional utilizando NGINX y PHP compilado desde código fuente.

Además, se configuró PHP-FPM para permitir la comunicación entre ambos servicios y se habilitaron extensiones necesarias para procesamiento de imágenes e internacionalización.

El proyecto permitió comprender el proceso de compilación manual de PHP, configuración de servidores web y solución de errores durante la instalación.

---

# Repositorio GitHub

```text
https://github.com/Alex-0h/proyecto-nginx-php
```

