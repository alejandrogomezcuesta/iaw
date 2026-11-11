# Ampliar el almacenamiento de una instancia de AWS

## Ampliar el volumen en la consola de AWS:

1. Dirígete a la consola de **Amazon EC2** y en el menú lateral selecciona **Volúmenes** (en la sección *Almacenamiento*).
2. Selecciona el volumen adjunto a tu instancia y haz clic en **Acciones** > **Modificar volumen** (es la 1ª opción).
3. Introduce el **nuevo tamaño** (en GiB) deseado y confirma haciendo clic en **Modificar**.

*AWS cambiará el estado del volumen a "optimizing". El volumen ya tiene el nuevo espacio asignado, pero el sistema operativo de tu servidor aún no lo detecta.*

*Recarga la página hasta que deje de salir el mensaje en azul.*


2. **Verificar la nueva capacidad en el servidor:** Acceso por SSH / RDP necesario.
Accede a tu instancia por consola (SSH) y ejecuta:

```bash
df -h
lsblk

```
* Con `df -h` verás el espacio que está usando actualmente tu sistema de archivos.
* Con `lsblk` comprobarás que el disco físico (ej. `nvme0n1` o `xvda`) ya muestra el nuevo tamaño, pero la partición interna aún mantiene el tamaño antiguo.

Un resultado podría ser:

```bash
admin@ip-172-31-16-174:~$ lsblk
NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0          7:0    0  71.7M  1 loop /snap/certbot/5893
loop1          7:1    0 105.2M  1 loop /snap/core/17292
loop2          7:2    0  44.7M  1 loop /snap/snapd/28254
loop3          7:3    0  66.8M  1 loop /snap/core24/2124
nvme0n1      259:0    0    32G  0 disk 
├─nvme0n1p1  259:1    0   7.9G  0 part /
├─nvme0n1p14 259:2    0     3M  0 part 
└─nvme0n1p15 259:3    0   124M  0 part /boot/ef
```


3. **Instalar las herramientas de particionado**: Necesario si growpart no está disponible por defecto.
Ejecuta el siguiente comando para asegurarte de tener la herramienta growpart:
```bash
sudo apt update && sudo apt install -y cloud-guest-utils
```

3. **Extender la partición y el sistema de archivos:** Comandos para Linux.
Dependiendo del sistema de archivos y el tipo de volumen de tu instancia:

**1. Extender la partición:**
Si el disco es `nvme0n1` y la partición principal es la `1`:

```bash
sudo growpart /dev/nvme0n1 1

```

*(Nota el espacio entre el nombre del dispositivo `/dev/nvme0n1` y el número de partición `1`).*

**2. Extender el sistema de archivos:**

* Para **ext4** (el habitual en Ubuntu/Debian):

```bash
sudo resize2fs /dev/nvme0n1p1

```


4. **Confirmar los cambios:**
Vuelve a verificar con:

```bash
df -h

```

El punto de montaje principal (`/`) ya debería mostrar el 100% de la nueva capacidad asignada.

