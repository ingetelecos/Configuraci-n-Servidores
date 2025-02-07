Aquí tienes una guía paso a paso para configurar un servidor Linux Ubuntu desde cero.  

---

## **Paso 1: Descargar e Instalar Ubuntu Server**  
1. **Descargar la ISO**  
   - Ve a [Ubuntu Server](https://ubuntu.com/download/server) y descarga la versión más reciente.  
2. **Crear un medio de instalación**  
   - Si instalas en una máquina física, usa **Rufus** (Windows) o **dd** (Linux/macOS) para crear un USB booteable.  
   - Si usas una máquina virtual, monta la ISO directamente.  
3. **Instalar Ubuntu Server**  
   - Arranca desde el USB o la ISO.  
   - Sigue el asistente de instalación.  
   - Configura **red, usuario root, zona horaria y particionado** (puedes usar LVM).  
   - Instala **OpenSSH Server** cuando te lo pregunte.  

---

## **Paso 2: Acceder y Actualizar el Servidor**  
1. **Accede por SSH (si es remoto)**  
   ```bash
   ssh usuario@ip_del_servidor
   ```
2. **Actualizar paquetes**  
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

---

## **Paso 3: Configuración de Red (Opcional)**  
Si necesitas una IP estática:  
1. Edita el archivo de configuración de red:  
   ```bash
   sudo nano /etc/netplan/00-installer-config.yaml
   ```
2. Define una IP estática (ejemplo para Ethernet en `eth0`):  
   ```yaml
   network:
     ethernets:
       eth0:
         dhcp4: no
         addresses: [192.168.1.100/24]
         gateway4: 192.168.1.1
         nameservers:
           addresses: [8.8.8.8, 8.8.4.4]
     version: 2
   ```
3. Aplica los cambios:  
   ```bash
   sudo netplan apply
   ```

---

## **Paso 4: Crear y Configurar Usuarios**  
1. **Agregar un nuevo usuario**  
   ```bash
   sudo adduser usuario_nuevo
   ```  
2. **Agregarlo al grupo sudo**  
   ```bash
   sudo usermod -aG sudo usuario_nuevo
   ```
3. **Configurar autenticación SSH** (recomendado)  
   - Copia tu clave pública a `/home/usuario_nuevo/.ssh/authorized_keys`.  

---

## **Paso 5: Configurar Firewall y Seguridad**  
1. **Instalar UFW y habilitarlo**  
   ```bash
   sudo apt install ufw -y
   sudo ufw allow OpenSSH
   sudo ufw enable
   ```
2. **Deshabilitar el acceso root por SSH** (seguridad adicional)  
   - Edita `/etc/ssh/sshd_config`:  
     ```bash
     sudo nano /etc/ssh/sshd_config
     ```
   - Cambia `PermitRootLogin yes` a `PermitRootLogin no`.  
   - Reinicia el servicio:  
     ```bash
     sudo systemctl restart ssh
     ```

---

## **Paso 6: Instalar y Configurar Servicios Básicos**  
### **Servidor Web (Nginx o Apache)**  
- **Nginx**:  
  ```bash
  sudo apt install nginx -y
  sudo systemctl enable --now nginx
  ```
- **Apache**:  
  ```bash
  sudo apt install apache2 -y
  sudo systemctl enable --now apache2
  ```

### **Base de Datos (MySQL/MariaDB o PostgreSQL)**  
- **MySQL/MariaDB**:  
  ```bash
  sudo apt install mysql-server -y
  sudo mysql_secure_installation
  ```
- **PostgreSQL**:  
  ```bash
  sudo apt install postgresql postgresql-contrib -y
  ```

---

## **Paso 7: Monitoreo y Mantenimiento**  
1. **Instalar herramientas de monitoreo**  
   ```bash
   sudo apt install htop iftop vnstat -y
   ```
2. **Configurar actualizaciones automáticas**  
   ```bash
   sudo apt install unattended-upgrades -y
   sudo dpkg-reconfigure unattended-upgrades
   ```
3. **Configurar backups automáticos**  
   - Usa `rsync`, `tar`, o herramientas como **Bacula** o **Duplicity**.  

---

Con esto, ya tienes un **servidor Ubuntu** básico y seguro. ¿Quieres configurar algo más específico como un servidor web con Docker o un firewall avanzado?