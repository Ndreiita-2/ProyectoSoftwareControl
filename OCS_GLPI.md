# 🧱 🧭 VISIÓN GENERAL

Vas a montar:

* 🖥️ 1 VM servidor (Ubuntu)

* 💻 1 VM cliente (Windows)

* 🌐 2 redes:

  * NAT → internet
  * Host-Only → laboratorio

* 🧠 Software:

  * OCS Inventory NG
  * GLPI

---

# 🧱 1. CREAR VM EN VirtualBox

## 🖥️ Servidor Ubuntu

* RAM: 4 GB
* CPU: 2
* Disco: 40 GB (dinámico)

---

# 🌐 2. CONFIGURAR RED (CLAVE)

## 🔹 Adaptador 1

* Tipo: **NAT** ✅
  👉 Internet

## 🔹 Adaptador 2

* Tipo: **Adaptador sólo anfitrión**
* Nombre: `vboxnet0` ✅
  👉 Red interna

---

# 🧠 Resultado esperado

| Interfaz | Uso       |
| -------- | --------- |
| enp0s3   | NAT       |
| enp0s8   | Host-Only |

---

# 🐧 3. INSTALAR UBUNTU SERVER

Durante instalación:

* Usuario: `sc`
* Password:
* Instala OpenSSH ✔️

---

# ⚙️ 4. CONFIGURAR RED (NETPLAN)

Editar:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

👉 Dejar EXACTAMENTE así:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:   # NAT (internet)
      dhcp4: true

    enp0s8:   # Host-Only (red laboratorio)
      dhcp4: false
      addresses:
        - 192.168.56.10/24
```

---

## 🔄 Aplicar

```bash
sudo netplan apply
```

---

# 🔄 5. ACTUALIZAR SISTEMA

```bash
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
```

---

Perfecto 👌 te dejo **TU MISMA GUÍA**, sin cambiar lo que ya tienes, solo **añadiendo lo que faltaba** (bien integrado y en orden).

---

# 🧰 6. INSTALAR DEPENDENCIAS OCS

```bash
sudo apt install -y apache2 mariadb-server php php-mysql php-curl php-xml php-mbstring php-zip libapache2-mod-perl2 libnet-ip-perl libxml-simple-perl libarchive-zip-perl libio-compress-perl
```

---

# 🔐 7. CONFIGURAR MYSQL

```bash
sudo mysql_secure_installation
```

👉 Todo YES

---

## Crear base de datos

```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE ocsweb;
CREATE USER 'ocs'@'localhost' IDENTIFIED BY 'ocspass';
GRANT ALL PRIVILEGES ON ocsweb.* TO 'ocs'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

# 📦 8. DESCARGAR OCS

```bash
cd /opt
sudo wget https://github.com/OCSInventory-NG/OCSInventory-ocsreports/releases/download/2.12.3/OCSNG_UNIX_SERVER-2.12.3.tar.gz
sudo tar -xvzf OCSNG_UNIX_SERVER-2.12.3.tar.gz
cd OCSNG_UNIX_SERVER-2.12.3
```

---

# ⚙️ 9. INSTALAR OCS

```bash
sudo ./setup.sh
```

👉 Durante instalación:

* Todo ENTER

* DB:

  * Nombre: `ocsweb`
  * Usuario: `ocs`
  * Pass: `ocspass`

---

# 🔥 9.1 COPIAR ARCHIVOS WEB

```bash
sudo mkdir -p /usr/share/ocsinventory-reports
sudo cp -r ocsreports /usr/share/ocsinventory-reports/
```

---

# 🔥 9.2 PERMISOS

```bash
sudo chown -R www-data:www-data /usr/share/ocsinventory-reports
sudo chmod -R 755 /usr/share/ocsinventory-reports
```

---

# 🔧 10. ACTIVAR APACHE

```bash
sudo a2enmod rewrite
sudo a2enmod headers
sudo systemctl restart apache2
```

---

# 🔥 10.1 CONFIGURAR APACHE PARA OCS

```bash
sudo nano /etc/apache2/conf-available/ocsinventory-reports.conf
```

👉 Pegar:

```apache
Alias /ocsreports "/usr/share/ocsinventory-reports/ocsreports"

<Directory "/usr/share/ocsinventory-reports/ocsreports">
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

---

## Activar configuración

```bash
sudo a2enconf ocsinventory-reports
sudo systemctl reload apache2
```

---

# 🌐 11. ACCESO WEB OCS

Desde tu PC:

👉

```
http://192.168.56.10/ocsreports
```
* Completar instalación
* Crear config
* Entramos con: admin/admin
---

## 🔐 Seguridad

```bash
sudo rm -f /usr/share/ocsinventory-reports/ocsreports/install.php
```

---

# 🧱 12. INSTALAR GLPI

```bash
cd /var/www/html
sudo wget https://github.com/glpi-project/glpi/releases/download/10.0.16/glpi-10.0.16.tgz
sudo tar -xvzf glpi-10.0.16.tgz
sudo chown -R www-data:www-data glpi
```

---

Instalar extensiones faltantes

```bash
sudo apt install -y php-gd php-intl php-ldap php-bz2
sudo systemctl restart apache2
```

Perfecto 👌 te lo integro **exactamente en tu guía**, sin cambiar nada, solo añadiendo el bloque de seguridad justo donde toca.

---

# 🌐 13. ACCESO GLPI

👉 `http://192.168.56.10/glpi`

Entra en MySQL:

```bash
sudo mysql -u root -p
```

Ejecuta esto:

```bash
CREATE DATABASE glpi;
CREATE USER 'glpi'@'localhost' IDENTIFIED BY 'glpipass';
GRANT ALL PRIVILEGES ON glpi.* TO 'glpi'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Ahora entramos a la base de datos con:

Servidor: localhost
Usuario: glpi
Password: glpipass

Luego a la interfaz de GLPI con:

* Usuario: `glpi`
* Password: `glpi`

---

# 🔐 13.1 SEGURIDAD INICIAL GLPI

👉 Dentro de GLPI:

**Administración → Usuarios**

Cambiar contraseña de:

* `glpi`
* `tech`
* `normal`
* `post-only`

---

## 🔹 Borrar instalador (OBLIGATORIO)

```bash
sudo rm -f /var/www/html/glpi/install/install.php
```

---

## 🔹 Corregir advertencia de seguridad (cookies)

Editar configuración de PHP:

```bash
sudo nano /etc/php/8.3/apache2/php.ini
```

Buscar:

```ini
session.cookie_httponly =
```

Dejarlo así:

```ini
session.cookie_httponly = On
```

Guardar y reiniciar Apache:

```bash
sudo systemctl restart apache2
```

---

## ⚠️ Nota

👉 El aviso de directorio inseguro (`/glpi/public`) se puede ignorar en laboratorio.

---

# 🔗 14. CONECTAR GLPI CON OCS

Dentro de GLPI:

* Plugins → instalar OCS
* Configurar:

  * Host: `localhost`
  * DB: `ocsweb`
  * Usuario: `ocs`
  * Pass: `ocspass`

## ❗ CORREGIR ERROR TRACE_DELETED 

👉 Si aparece el error:

```text
Configuración OCSNG incorrecta (debe activar TRACE_DELETED)
```

---

### 🔹 Entrar en MariaDB

```bash
sudo mysql -u root -p
```

---

### 🔹 Ejecutar:

```sql
USE ocsweb;

SELECT NAME, IVALUE FROM config WHERE NAME='TRACE_DELETED';
```

---

### 🔹 Si NO existe:

```sql
INSERT INTO config (NAME, IVALUE) VALUES ('TRACE_DELETED','1');
```

---

### 🔹 Si vale 0:

```sql
UPDATE config SET IVALUE='1' WHERE NAME='TRACE_DELETED';
```

---

### 🔹 Salir:

```sql
EXIT;
```

---

### 🔄 Reiniciar Apache

```bash
sudo systemctl restart apache2
```

---

### 🔁 IMPORTANTE

👉 Cerrar sesión en GLPI y volver a entrar


---

# 💻 15. CLIENTE WINDOWS

Crear VM:

* RAM: 2 GB
* Red:

  * NAT
  * Host-Only

IP manual:

```
192.168.56.20
```

---

# 📦 16. INSTALAR AGENTE OCS

En Windows:

* Descargar agente OCS
* Instalar con:

```
http://192.168.56.10/ocsinventory
```

---

# 🎉 17. RESULTADO FINAL

En OCS / GLPI verás:

* Equipos conectados
* Software instalado
* Hardware

---

# 🚀 CONCLUSIÓN

👉 Con esto tienes:

* Laboratorio completo
* Control centralizado
* Simulación real de empresa

---

# 💡 Si quieres siguiente nivel

Te puedo enseñar:

* Despliegue por GPO
* Informes de software prohibido
* Automatización
* Alertas

---

👉 Ahora dime:

¿en qué paso estás exactamente?
y te acompaño en tiempo real hasta que lo tengas funcionando 😄


# 🚀 18. Ver resultados

Después de instalar el agente:

👉 Espera 1–2 minutos
👉 Ve a:

* OCS:
  `http://192.168.56.10/ocsreports`

* GLPI:
  `http://192.168.56.10/glpi`

🎉 Ya deberías ver:

* El PC cliente
* Software instalado
* Hardware

---

