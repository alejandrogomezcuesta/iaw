# Alpine Linux con XFCE

Cómo tener un contenedor de docker que ejecuta un sistema operativo con interfaz de ventanas:

**Alipe con icewm**
```bash
docker run -d \
  --name=webtop \
  -e CUSTOM_USER=miusuario \
  -e PASSWORD=micontraseñasegura \
  -p 3000:3000 \
  --shm-size=1gb \
  --restart unless-stopped \
  lscr.io/linuxserver/webtop:alpine-icewm
```

**Ubuntu con XCFE**
```bash
docker run -d \
  --name=webtop \
  -e CUSTOM_USER=miusuario \
  -e PASSWORD=micontraseñasegura \
  -p 3000:3000 \
  --shm-size=1gb \
  lscr.io/linuxserver/webtop:ubuntu-lxqt
```