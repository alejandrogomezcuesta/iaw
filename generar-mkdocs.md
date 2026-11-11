# Generar la web de MkDocs

Este proyecto genera la web a partir de los documentos de `docs/` y la
configuración de [mkdocs.yml](./mkdocs.yml). Usa el tema Material para MkDocs.

## Requisitos

- Python 3
- `pip`

## Preparar el entorno

Desde la raíz del proyecto, abre PowerShell y ejecuta:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install mkdocs-material
```

La activación del entorno es necesaria en cada terminal nueva antes de usar
los comandos `mkdocs`.

## Previsualizar la web

```powershell
mkdocs serve --config-file mkdocs.yml
```

Abre en el navegador la dirección que indique el comando (normalmente
`http://127.0.0.1:8000/`). El servidor actualiza la previsualización al guardar
cambios en los documentos.

## Generar los archivos del sitio

```powershell
mkdocs build --config-file mkdocs.yml --strict --clean
```

El sitio generado queda en `site/`. La opción `--strict` hace que la
generación falle si hay advertencias, y `--clean` limpia primero los archivos
generados anteriormente.

## Actualizar la navegación

La navegación está declarada manualmente en la sección `nav:` de
`mkdocs.yml`. Cuando añadas, quites o muevas una página Markdown dentro de
`docs/`, actualiza también esa sección para que la página aparezca en el menú.

## Publicar en GitHub Pages

El workflow de GitHub Actions construye y publica el sitio automáticamente al
subir cambios a la rama `main` o `master`. También se puede iniciar
manualmente desde la pestaña **Actions** del repositorio. La build de
publicación utiliza el mismo comando estricto indicado arriba.
