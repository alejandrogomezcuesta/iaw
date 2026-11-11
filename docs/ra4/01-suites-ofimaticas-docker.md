# Suites ofimáticas con docker


## Onlyoffice 

El fichero siguiente se tiene que llamar `docker-compose.yml`.

**No se puede ejecutar porque hacen falta al menos 12GB de RAM**.

```yml
services:
  onlyoffice-mysql-server:
    image: mysql:8.0
    container_name: onlyoffice-mysql-server
    environment:
      MYSQL_ROOT_PASSWORD: mi_contraseña_root_segura
      MYSQL_DATABASE: onlyoffice
    volumes:
      - mysql_data:/var/lib/mysql
    restart: always

  onlyoffice-document-server:
    image: onlyoffice/documentserver:latest
    container_name: onlyoffice-document-server
    environment:
      JWT_ENABLED: "true"
      JWT_SECRET: mi_clave_secreta_jwt
    volumes:
      - ds_data:/var/www/onlyoffice/Data
      - ds_log:/var/log/onlyoffice
    restart: always

  onlyoffice-community-server:
    image: onlyoffice/communityserver:latest
    container_name: onlyoffice-community-server
    ports:
      - "80:80"
      - "443:443"
    environment:
      MYSQL_SERVER_HOST_NAME: onlyoffice-mysql-server
      MYSQL_SERVER_PORT: 3306
      MYSQL_SERVER_DB_NAME: onlyoffice
      MYSQL_SERVER_USER: root
      MYSQL_SERVER_PASS: mi_contraseña_root_segura
      DOCUMENT_SERVER_PORT_80_TCP_ADDR: onlyoffice-document-server
      DOCUMENT_SERVER_JWT_ENABLED: "true"
      DOCUMENT_SERVER_JWT_SECRET: mi_clave_secreta_jwt
    volumes:
      - cs_data:/var/www/onlyoffice/Data
      - cs_log:/var/log/onlyoffice
    depends_on:
      - onlyoffice-mysql-server
      - onlyoffice-document-server
    restart: always

volumes:
  mysql_data:
  ds_data:
  ds_log:
  cs_data:
  cs_log:
```

## LibreOffice 

Configura correctamente la línea 6 para tener tus documentos en un volumen o bind mount, siempre que los pongas en el escritorio.

```bash hl_lines="6" linenums="1"
docker run -d \
  --name=libreoffice-web \
  -e CUSTOM_USER=miusuario \
  -e PASSWORD=micontraseñasegura \
  -p 3000:3000 \
  -v /ruta/a/tus/documentos:/config/Desktop \
  --shm-size=1gb \
  --restart unless-stopped \
  lscr.io/linuxserver/webtop:ubuntu-xfce;

docker exec -it libreoffice-web apt update;
docker exec -it libreoffice-web apt install -y libreoffice;
docker exec -it libreoffice-web apt install -y libreoffice-l10n-es;
docker exec -it webtop sudo chown -R miusuario:miusuario /config/Desktop;
```

Aunque también podrías escribir esto otro desde 

- **Consumo de RAM**: ~400–600 MB (frente a los más de 2–3 GB que consume ONLYOffice).
- **Configuración**: Cero archivos `.env` o servidores extra. Al entrar a `http://localhost:3000` tienes el escritorio con LibreOffice (Writer, Calc, Impress) listo para usar.
- **Persistencia**: Todo lo que guardes en el escritorio del navegador se guardará directamente en la carpeta `/ruta/a/tus/documentos` de tu PC real.

## Etherpad + Ethercalc

Si solo necesitas procesador de textos y hojas de cálculo simples para notas, redactar documentos o tablas colaborativas sin la complejidad de un archivo .docx pesado, puedes usar Etherpad:

```bash
docker run -d \
  --name=etherpad \
  -p 9001:9001 \
  --restart unless-stopped \
  etherpad/etherpad:latest
```

- **Consumo de RAM**: Mínimo (~50–100 MB).
- **Uso**: Abres http://localhost:9001, creas un documento y escribes al instante sin pantallas de carga ni licencias.
