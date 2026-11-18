# Despliegue, Administración y Explotación de Gestores de Contenidos (CMS)

## 1. Objetivo de la práctica

El objetivo de esta actividad es desplegar un Gestor de Contenidos (CMS) de software libre mediante contenedores **Docker** y explorar a fondo sus capacidades de administración, personalización, seguridad y distribución de la información.

## 2. Organización y dinámica de trabajo

- **Agrupamiento:** El trabajo se realizará en **parejas** (grupos de 2 alumnos/as).
- **Asignación de herramientas:** El profesor asignará de forma **aleatoria** a cada pareja uno de los 13 gestores de contenidos listados al final de este documento.
- **Despliegue:** La instalación del CMS deberá realizarse obligatoriamente mediante **Docker** utilizando un archivo `docker-compose.yml` documentado.
- **Exposición oral:** Si la planificación temporal del módulo lo permite, cada pareja realizará una presentación práctica ante el resto de la clase con una duración **máxima de 10 minutos**, mostrando el entorno en funcionamiento y destacando las particularidades de su gestor frente al resto.

---

## 3. Puntos obligatorios a desarrollar y documentar

Deberéis entregar una memoria técnica (en formato PDF) acompañando vuestro entorno Docker donde se explique, justifique y demuestre mediante capturas de pantalla propias cómo se gestionan los siguientes **8 apartados** en vuestro CMS asignado:

1. **Usuarios y grupos / perfiles:** Creación, edición y borrado de cuentas de usuario. Organización de las mismas en grupos o diferentes roles de usuario (perfiles): administrador, usuario estándar, editor, etc.
2. **Control de accesos:** Configuración de cómo restringir el acceso a diferentes usuarios. Por ejemplo: si hay que entrar con usuario/contraseña, o con email/contraseña, pudiendo limitar el acceso a solo un dominio (ejemplo: dominio `@iesamachado.org`) o restringiendo secciones según el rol.
3. **Integración de módulos:** Búsqueda, instalación, activación y configuración de al menos una extensión, plugin o módulo que amplíe las funcionalidades base del sistema (siempre que sea posible en el CMS asignado).
4. **Gestión de temas y plantillas:** Instalación, activación y personalización visual de la apariencia global del sitio (*themes* o *skins*) y uso de distintas plantillas de diseño.
5. **Copias de seguridad:** Procedimiento completo para realizar un respaldo (*backup*) tanto de los contenidos/base de datos como de los archivos del sistema, así como la verificación de su proceso de restauración.
6. **Sindicación de contenidos:** Configuración, generación y comprobación de canales de suscripción automática (**RSS / Atom**) para difundir las actualizaciones del sitio.
7. **Posicionamiento en la Web (SEO):** Configuración de URLs amigables (*slugs*), metadatos (`title`, `description`), generación de mapas del sitio (`sitemap.xml`) o integración de herramientas específicas de optimización para buscadores.
8. **Importación y exportación de la información:** Migración de contenidos (artículos, páginas, usuarios o configuraciones) hacia y desde formatos estándar (XML, CSV, JSON o paquetes nativos del propio CMS).

---

## 4. Listado de Gestores de Contenidos (CMS) asignables

El profesor sorteará entre las parejas los siguientes **13 gestores de contenidos gratuitos y de código abierto**:

1. **WordPress**
   - *Descripción:* El gestor de contenidos más extendido a nivel mundial. Destaca por su facilidad de uso, su editor de bloques y un inmenso ecosistema de plugins y temas.
2. **Drupal**
   - *Descripción:* CMS orientado a portales institucionales y corporativos complejos. Sobresale por su avanzado sistema nativo de roles, permisos granulares y estructuración de datos.
3. **Joomla!**
   - *Descripción:* Plataforma multipropósito muy equilibrada que incorpora de serie listas de control de acceso (ACL), gestión múltiple de plantillas y sindicación sin depender de extensiones externas.
4. **TYPO3**
   - *Descripción:* Gestor de nivel empresarial muy implantado en Europa, especialmente potente en la gestión de árboles de páginas, flujos editoriales y permisos multiusuario.
5. **Backdrop CMS**
   - *Descripción:* Bifurcación (*fork*) más ligera y accesible de Drupal que mantiene su potencia en roles, bloques y plantillas de diseño (*Layouts*) con una curva de aprendizaje más rápida.
6. **Concrete CMS**
   - *Descripción:* Sistema que permite la edición visual y contextual directamente sobre la página web, con un control de permisos muy detallado a nivel de página y bloque.
7. **Silverstripe CMS**
   - *Descripción:* Gestor basado en arquitectura MVC que destaca por la limpieza de su panel de administración y la clara separación entre lógica, seguridad y plantillas.
8. **ProcessWire**
   - *Descripción:* Plataforma muy flexible donde cada página se construye a partir de plantillas con campos personalizados y control de acceso ligado directamente a dichas plantillas.
9. **Umbraco**
   - *Descripción:* El CMS libre de referencia en el ecosistema Microsoft (.NET), muy utilizado en entornos corporativos para definir tipos de documentos y plantillas a medida.
10. **Grav**
    - *Descripción:* Gestor moderno de arquitectura *flat-file* (no utiliza base de datos relacional; almacena la información en archivos Markdown), destacando por su velocidad y facilidad de respaldo.
11. **Plone**
    - *Descripción:* CMS desarrollado en Python, reconocido por su alto nivel de seguridad y su motor nativo de estados y flujos de trabajo (borrador, revisión y publicación).
12. **Moodle**
    - *Descripción:* El sistema de gestión del aprendizaje (LMS) más utilizado en educación, dotado de una compleja gestión de roles, cohortes, copias de seguridad (`.mbz`) y módulos.
13. **MediaWiki**
    - *Descripción:* El software colaborativo que impulsa Wikipedia. Especializado en bases de conocimiento, permisos por niveles, plantillas reutilizables e importación/exportación XML.

---

## 5. Persistencia de datos y preparación del entorno Docker para su entrega

Del curso de Docker que hemos estado siguiendo, nos faltaba todavía [la sesión 6](https://iesgn.github.io/curso_docker_2021/sesion6/). Para realizar esta práctica no basta con levantar los contenedores por defecto: **el profesor debe poder levantar vuestro proyecto en su ordenador y ver exactamente los mismos usuarios, entradas, temas y configuraciones que habéis creado**.

En Docker, si no configuráis volúmenes persistentes vinculados a carpetas locales (*bind mounts*), al detener o mover los contenedores se pierden los datos. Para evitarlo y poder entregar vuestro trabajo completo, debéis seguir estos pasos:

1. **Usad carpetas locales (*bind mounts*) en lugar de volúmenes anónimos o nombrados:**
   En vuestro archivo `docker-compose.yml`, mapead las carpetas de datos tanto del contenedor del CMS (donde se guardan los temas, plugins y archivos subidos) como del contenedor de la base de datos (donde se guardan los usuarios, artículos y ajustes) hacia subcarpetas dentro de vuestro proyecto (por ejemplo, `./cms_data` y `./db_data`).
   - *Ejemplo orientativo (caso WordPress + MariaDB):*
     ```yaml
     services:
       cms:
         image: wordpress:latest
         ports:
           - "8080:80"
         environment:
           WORDPRESS_DB_HOST: db
           WORDPRESS_DB_USER: usuario
           WORDPRESS_DB_PASSWORD: password
           WORDPRESS_DB_NAME: cms_db
         volumes:
           - ./wp-content:/var/www/html/wp-content  # Guarda temas, plugins y subidas

       db:
         image: mariadb:10.11
         environment:
           MYSQL_ROOT_PASSWORD: rootpassword
           MYSQL_DATABASE: cms_db
           MYSQL_USER: usuario
           MYSQL_PASSWORD: password
         volumes:
           - ./db_data:/var/lib/mysql  # Guarda la base de datos (usuarios, posts, etc.)
     ```
2. **Comprobad qué rutas internas usa vuestro CMS:**
   Cada CMS guarda sus archivos personalizados en una ruta distinta dentro del contenedor (por ejemplo, `/var/www/html/wp-content` en WordPress, `/var/www/html/sites` en Drupal o `/var/lib/mysql` en MariaDB/MySQL). Revisad la documentación de la imagen oficial que os haya tocado para mapear correctamente sus directorios de datos.
3. **Detened los contenedores antes de empaquetar:**
   Antes de comprimir la carpeta para entregarla, ejecutad siempre `docker compose down`. Si comprimís los archivos de la base de datos mientras el contenedor está encendido, los datos pueden corromperse y no arrancarán en el equipo del profesor.
4. **Verificad que es portable:**
   En el fichero `docker-compose.yml` usad siempre **rutas relativas** (`./carpeta:/ruta/contenedor`) y nunca rutas absolutas de vuestro ordenador (`/home/alumno/...` o `C:\Users\...`), para que funcione al descomprimirlo en cualquier equipo con un simple `docker compose up -d`.

---

## 6. Criterios de entrega

La entrega se realizará a través de **Google Classroom** subiendo **dos ficheros**:

1. **Memoria técnica en formato PDF:** Con portada, índice y los 8 puntos desarrollados y documentados con capturas. El nombre del fichero será `xxx.pdf`, donde `xxx` es el nombre del CMS asignado (por ejemplo: `wordpress.pdf`, `drupal.pdf`, `moodle.pdf`).
2. **Entorno Docker completo comprimido (`xxx_docker.zip`):** Un único archivo comprimido que contenga la carpeta raíz de vuestro despliegue con:
   - El archivo `docker-compose.yml` funcional y comentado.
   - Todas las carpetas asociadas a los volúmenes (*bind mounts*) generadas durante la práctica (por ejemplo, `db_data/`, `wp-content/`, etc.) tras haber detenido los contenedores con `docker compose down`.

> **Importante:** Solo tiene que realizar la entrega en Classroom **una persona de la pareja**. Quien no realice la entrega deberá marcar la tarea como completada indicando en un comentario privado quién es su compañero/a de grupo.