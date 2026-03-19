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

# 🔧 10. ACTIVAR APACHE

```bash
sudo a2enmod rewrite
sudo a2enmod headers
sudo systemctl restart apache2
```

---

# 🌐 11. ACCESO WEB OCS

Desde tu PC:

👉 `http://192.168.56.10/ocsreports`

* Completar instalación
* Crear config

---

## 🔐 Seguridad

```bash
sudo rm -f /usr/share/ocsinventory-reports/ocsreports/install.php
```

---

# 🧱 12. INSTALAR GLPI

```bash
cd /var/www/html
sudo wget https://github.com/glpi-project/glpi/releases/latest/download/glpi.tgz
sudo tar -xvzf glpi.tgz
sudo chown -R www-data:www-data glpi
```

---

# 🌐 13. ACCESO GLPI

👉 `http://192.168.56.10/glpi`

* Usuario: `glpi`
* Password: `glpi`

---

# 🔗 14. CONECTAR GLPI CON OCS

Dentro de GLPI:

* Plugins → instalar OCS
* Configurar:

  * Host: `localhost`
  * DB: `ocsweb`
  * Usuario: `ocs`
  * Pass: `ocspass`

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

