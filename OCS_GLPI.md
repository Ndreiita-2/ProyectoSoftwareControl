# 🧱 🖥️ 1. Arquitectura del laboratorio

Vas a crear:

### 🔹 VM 1 — Servidor (Linux)

* Ubuntu Server
* IP fija (ej: `192.168.56.10`)
* OCS + GLPI

### 🔹 VM 2 — Cliente (Windows)

* Windows 10/11
* IP (ej: `192.168.56.20`)
* Agente OCS

---

# 🌐 🧩 2. Red en VirtualBox (IMPORTANTE)

Configura ambas VMs en:

👉 **Red interna o Host-Only Adapter**

Recomendado:

* Adaptador: **Host-Only**
* Rango típico: `192.168.56.0/24`

---

# 🟢 🐧 3. Instalar servidor Ubuntu

Descarga:

* Ubuntu Server 22.04

Durante instalación:

* IP manual:

  ```
  IP: 192.168.56.10
  Gateway: 192.168.56.1 (o vacío si no usas salida)
  DNS: 8.8.8.8
  ```

---

# ⚙️ 🧰 4. Instalar OCS Inventory

Dentro del servidor:

```bash
sudo apt update
sudo apt install -y apache2 mariadb-server php php-mysql php-curl php-xml php-mbstring git unzip
```

---

### 🔹 Descargar OCS

```bash
cd /opt
sudo wget https://github.com/OCSInventory-NG/OCSInventory-Server/releases/latest/download/OCSNG_UNIX_SERVER-latest.tar.gz
sudo tar -xvzf OCSNG_UNIX_SERVER-latest.tar.gz
cd OCSNG_UNIX_SERVER-*
sudo ./setup.sh
```

👉 Sigue el instalador (todo por defecto suele ir bien)

---

### 🔹 Finalizar instalación

Accede desde tu navegador (host):

👉 `http://192.168.56.10/ocsreports`

Ahí terminarás el setup web

---

# 🟢 🧠 5. Instalar GLPI

```bash
cd /var/www/html
sudo wget https://github.com/glpi-project/glpi/releases/latest/download/glpi.tgz
sudo tar -xvzf glpi.tgz
sudo chown -R www-data:www-data glpi
```

---

### 🔹 Acceso web

👉 `http://192.168.56.10/glpi`

* Usuario: `glpi`
* Password: `glpi`

(Sigue asistente)

---

# 🔗 🔥 6. Conectar GLPI con OCS

Dentro de GLPI:

1. Ir a:
   👉 **Configuración → Plugins**
2. Instalar plugin OCS (si no viene incluido)
3. Configurar conexión:

   * Host: `localhost`
   * Usuario DB
   * Base de datos OCS

💡 Esto hace que GLPI “lea” lo que recoge OCS

---

# 🖥️ 💻 7. Crear cliente Windows

En otra VM:

* Instala Windows
* IP manual:

  ```
  192.168.56.20
  ```

---

# 📦 ⚙️ 8. Instalar agente OCS en cliente

Descarga:
👉 [https://ocsinventory-ng.org](https://ocsinventory-ng.org)

Instala con:

* Server: `http://192.168.56.10/ocsinventory`

💡 MUY IMPORTANTE: poner bien la URL

---

# 🚀 9. Ver resultados

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

# ⚠️ Problemas típicos

* ❌ No aparece cliente → firewall o URL mal
* ❌ GLPI no ve OCS → conexión DB mal
* ❌ Agente no reporta → servicio parado

---
