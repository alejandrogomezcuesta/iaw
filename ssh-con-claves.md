# Apuntes: Acceso SSH con claves RSA en Debian

Estos apuntes explican cómo instalar SSH, generar claves, configurar el servidor para aceptarlas, preparar el usuario, conectarse y usar el fichero `~/.ssh/config`.  
En cada paso se indica si los comandos se ejecutan **en tu ordenador (LOCAL)** o **en el servidor Debian (REMOTO)**.

---

## 1. Instalar el servidor SSH  
📍 **SERVIDOR (REMOTO)**

    sudo apt update
    sudo apt install openssh-server

Comprobar que está funcionando:

    systemctl status ssh

SSH usa el puerto **22** por defecto.

---

## 2. Crear una clave RSA  
📍 **TU ORDENADOR (LOCAL)**

SSH utiliza un par de claves:

- **Clave privada** → se queda en tu ordenador  
- **Clave pública** → se copia al servidor

### 2.1 Crear una clave con nombre por defecto

    ssh-keygen -t rsa -b 4096

Esto genera:

- `~/.ssh/id_rsa`  
- `~/.ssh/id_rsa.pub`

### 2.2 Crear una clave con nombre personalizado

    ssh-keygen -t rsa -b 4096 -f ~/.ssh/debian_server_rsa

Esto genera:

- `~/.ssh/debian_server_rsa`  
- `~/.ssh/debian_server_rsa.pub`

---

## 3. Configurar SSH para aceptar claves  
📍 **SERVIDOR (REMOTO)**

Editar la configuración:

    sudo nano /etc/ssh/sshd_config

Asegurar que estas líneas están activas:

    PubkeyAuthentication yes
    AuthorizedKeysFile .ssh/authorized_keys

Reiniciar SSH:

    sudo systemctl restart ssh

---

## 4. Preparar la clave pública en el servidor  
📍 **LOCAL → enviar la clave**  
📍 **REMOTO → guardarla en el usuario**

Supongamos que el usuario remoto es:

    pepe

### 4.1 Crear la carpeta `.ssh`  
📍 **SERVIDOR (REMOTO)**

    sudo mkdir -p /home/pepe/.ssh
    sudo chmod 700 /home/pepe/.ssh
    sudo chown pepe:pepe /home/pepe/.ssh

### 4.2 Copiar la clave pública

#### Opción A: Automática  
📍 **LOCAL**

    ssh-copy-id -i ~/.ssh/debian_server_rsa.pub pepe@IP_DEL_SERVIDOR

#### Opción B: Manual  
📍 **LOCAL**

    cat ~/.ssh/debian_server_rsa.pub

Copiar el contenido.

📍 **SERVIDOR (REMOTO)**

    sudo nano /home/pepe/.ssh/authorized_keys

Pegar la clave pública.

Permisos:

    sudo chmod 600 /home/pepe/.ssh/authorized_keys
    sudo chown pepe:pepe /home/pepe/.ssh/authorized_keys

---

## 5. Conectarse usando la clave privada  
📍 **TU ORDENADOR (LOCAL)**

Si tu clave privada se llama `debian_server_rsa`:

    ssh -i ~/.ssh/debian_server_rsa pepe@IP_DEL_SERVIDOR

Si se llama `id_rsa`, no hace falta `-i`:

    ssh pepe@IP_DEL_SERVIDOR

---

## 6. El fichero `~/.ssh/config`  
📍 **TU ORDENADOR (LOCAL)**

Este archivo permite crear **atajos** para conectarte más rápido.

Editar:

    nano ~/.ssh/config

---

# Ejemplos de configuración

## A. Conexión sin clave RSA

    Host servidorClase
        HostName 192.168.1.50
        User pepe

Conexión:

    ssh servidorClase

---

## B. Conexión con clave RSA

    Host debianCasa
        HostName 192.168.1.50
        User pepe
        IdentityFile ~/.ssh/debian_server_rsa

Conexión:

    ssh debianCasa

---

## C. HostName + Puerto + Usuario

    Host servidorPuerto
        HostName 192.168.1.77
        User admin
        Port 2222

Conexión:

    ssh servidorPuerto

---

## D. HostName + Puerto + Usuario + Clave RSA

    Host servidorCompleto
        HostName 10.0.0.44
        User alumno1
        Port 2222
        IdentityFile ~/.ssh/alumno1_rsa

Conexión:

    ssh servidorCompleto

---

## 7. Resumen final (LOCAL vs REMOTO)

| Paso | Dónde        | Acción                    |
|------|--------------|---------------------------|
| 1    | REMOTO       | Instalar SSH              |
| 2    | LOCAL        | Crear claves RSA          |
| 3    | REMOTO       | Configurar SSH            |
| 4    | LOCAL/REMOTO | Copiar clave pública      |
| 5    | LOCAL        | Conectarse con clave      |
| 6    | LOCAL        | Crear atajos en `config`  |
