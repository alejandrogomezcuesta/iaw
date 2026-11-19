# Despliegue, Administración y Explotación de Gestores de Contenidos (CMS)

## 1. Objetivo de la práctica
El objetivo de esta actividad es desplegar un Gestor de Contenidos (CMS) de software libre mediante contenedores **Docker** y explorar de forma práctica sus capacidades de administración, personalización, seguridad y distribución de la información en un entorno controlado.

## 2. Organización, dinámica y planificación (4 sesiones)
- **Agrupamiento:** El trabajo se realizará de forma individual.
- **Asignación de herramientas:** Para optimizar el tiempo disponible, el alumno elige uno de los **4 gestores de contenidos principales** detallados en el apartado 4 (**WordPress, Drupal, Moodle o MediaWiki**).
- **Despliegue:** La instalación del CMS deberá realizarse obligatoriamente mediante **Docker** utilizando un archivo `docker-compose.yml` documentado con persistencia en carpetas locales (*bind mounts*).
- **Temporalización prevista (4 días / sesiones):**
  - **Sesión 1:** Sorteo de CMS, creación del `docker-compose.yml` con volúmenes locales y despliegue inicial del entorno.
  - **Sesión 2:** Configuración de usuarios, roles, control de accesos, módulos y personalización de temas/plantillas (Puntos 1 al 4).
  - **Sesión 3:** Copias de seguridad, sindicación RSS, SEO, importación/exportación y empaquetado final del entorno (Puntos 5 al 8).
  - **Sesión 4:** Si hubiera tiempo disponible, exposiciones prácticas ante la clase (**máximo 7-10 minutos**) de algunos puntos (no todos, porque es inviable) de cada CMS. Entrega en Classroom de los trabajos.

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

## 4. Gestores de Contenidos (CMS) asignables

Las alumnas y alumnos podéis elegir uno de los siguientes **4 sistemas de referencia** (todos gratuitos, de código abierto y con soporte oficial en Docker):

1. **WordPress** (`wordpress`)
   - *Enfoque:* Webs corporativas y blogs. Es el estándar del mercado; destaca por su facilidad de uso, su editor de bloques y su inmenso ecosistema de plugins y temas.
2. **Drupal** (`drupal`)
   - *Enfoque:* Portales institucionales y empresariales complejos. Sobresale por su avanzado sistema nativo de roles, permisos granulares y estructuración de contenidos.
3. **Moodle** (`bitnami/moodle` o imagen oficial)
   - *Enfoque:* Plataformas educativas y aulas virtuales (LMS). Destaca por su compleja gestión de roles educativos, cohortes, copias de seguridad (`.mbz`) y módulos formativos.
4. **MediaWiki** (`mediawiki`)
   - *Enfoque:* Wikis y bases de conocimiento colaborativas (el motor de Wikipedia). Especializado en edición colaborativa, permisos por niveles, plantillas reutilizables e importación/exportación XML.

---

## 5. Persistencia de datos y preparación del entorno Docker para su entrega

Siguiendo lo visto en [la sesión 6 del curso de Docker](https://iesgn.github.io/curso_docker_2021/sesion6/), **el profesor debe poder levantar vuestro proyecto en su ordenador y ver exactamente los mismos usuarios, entradas, temas y configuraciones que habéis creado**.

Para evitar que los datos se pierdan al detener o mover los contenedores, debéis configurar **volúmenes vinculados a carpetas locales (*bind mounts*)**:

1. **Mapead las carpetas de datos hacia subcarpetas del proyecto:**
   En vuestro archivo `docker-compose.yml`, vinculad los directorios críticos tanto del contenedor del CMS como de su base de datos usando **rutas relativas** (`./carpeta:/ruta/interna`).

   *Tabla de referencia rápida de volúmenes para los 4 CMS:*

   | CMS Asignado | Carpeta de personalización / datos del CMS | Carpeta de Base de Datos |
   | :--- | :--- | :--- |
   | **WordPress** | `./wp-content:/var/www/html/wp-content` | `./db_data:/var/lib/mysql` |
   | **Drupal** | `./sites:/var/www/html/sites` | `./db_data:/var/lib/mysql` |
   | **Moodle** | `./moodle_data:/bitnami/moodle` | `./db_data:/bitnami/mariadb` |
   | **MediaWiki** | `./images:/var/www/html/images` y `./LocalSettings.php:/var/www/html/LocalSettings.php` | `./db_data:/var/lib/mysql` |

   *Ejemplo orientativo (caso WordPress + MariaDB):*
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

2. **Detened los contenedores antes de empaquetar:**
   Antes de comprimir la carpeta para entregarla, ejecutad siempre `docker compose down`. Si comprimís los archivos de la base de datos mientras el contenedor está encendido, los datos pueden corromperse y no arrancarán en el equipo del profesor.
3. **Verificad la portabilidad:**
   Aseguraos de no usar rutas absolutas de vuestro ordenador (`/home/alumno/...` o `C:\Users\...`) en el `docker-compose.yml` para que funcione al descomprimirlo en cualquier equipo con un simple `docker compose up -d`.

---

## 6. Criterios de entrega

La entrega se realizará al finalizar la **4ª sesión** a través de **Google Classroom** subiendo **dos ficheros**:

1. **Memoria técnica en formato PDF:** Con portada, índice y los 8 puntos desarrollados y documentados con capturas. El nombre del fichero será `xxx.pdf`, donde `xxx` es el nombre del CMS asignado (`wordpress.pdf`, `drupal.pdf`, `moodle.pdf` o `mediawiki.pdf`).
2. **Entorno Docker completo comprimido (`xxx_docker.zip`):** Un único archivo comprimido que contenga la carpeta raíz de vuestro despliegue con:
   - El archivo `docker-compose.yml` funcional y comentado.
   - Todas las carpetas asociadas a los volúmenes (*bind mounts*) generadas durante la práctica tras haber detenido los contenedores con `docker compose down`.