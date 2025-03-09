# MKdocs
MkDocs es un generador de sitios web estáticos que nos permite crear de forma sencilla un sitio web para documentar un proyecto. El contenido del sitio web está escrito en texto plano en formato Markdown y se configura con un único archivo de configuración en formato YAML.

# Crear un proyecto MKdocs
Con el siguiente comando podemos crear la estructura de mkdocs.
```bash
docker run --rm -it -p 8000:8000 -u $(id -u):$(id -g) -v "$PWD":/docs squidfunk/mkdocs-material new .
```
![](/capturas/proyectomkdocs.png)

El archivo mkdocs.yml contiene la configuración de nuestro proyecto mkdocs. Por ejemplo:
```yml
site_name: Sami Benomar Megri
nav:
  - Principal: index.md
  - Acerca de: about.md
  - Practica5-1: Practica5-1.md
  - Practica5-2: Practica5-2.md
  - Practica5-4: Practica5-4.md
theme: 
  name: material
  palette:
    primary: 'blue'
    accent: 'indigo'
  font:
    text: 'Roboto'
    code: 'Roboto Mono'  
```
Site_name es el nomre de nuestro sitio web, nav es una barra lateral donde salen nuestras páginas y theme nos permite modificar el aspecto de nuestras páginas.
# Desplegar nuestro proyecto
Podemos ver nuestra página en un navegador de varias formas, por ejemplo:
```bash
docker run --rm -it -p 8000:8000 -u $(id -u):$(id -g) -v "$PWD":/docs squidfunk/mkdocs-material
```
![](/capturas/servidor.png)
![](/capturas/paginadefaullt.png)

## Despliegue desde github
Podemos desplegar nuestro sitio gracias a Github pages, para ello deberemos crear la rama "gh-pages" y configurar un workplace con el siguiente contenido:
```yml
name: build-push-mkdocs

# Eventos que desescandenan el workflow
on:
  push:
    branches: ["main"]

  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:

  # Job para crear la documentación de mkdocs
  build:
    # Indicamos que este job se ejecutará en una máquina virtual con la última versión de ubuntu
    runs-on: ubuntu-latest
    
    # Definimos los pasos de este job
    steps:
      - name: Clone repository
        uses: actions/checkout@v4

      - name: Install Python3
        uses: actions/setup-python@v4
        with:
          python-version: 3.x

      - name: Install Mkdocs
        run: |
          pip install mkdocs
          pip install mkdocs-material 

      - name: Build MkDocs
        run: |
          mkdocs build

      - name: Push the documentation in a branch
        uses: s0/git-publish-subdir-action@develop
        env:
          REPO: self
          BRANCH: gh-pages # The branch name where you want to push the assets
          FOLDER: site # The directory where your assets are generated
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} # GitHub will automatically add this - you don't need to bother getting a token
          MESSAGE: "Build: ({sha}) {msg}" # The commit message

```

Una vez hecho todo podemos ver nuestra página escribiendo nuestro usuario seguido de .github.io/ el nombre del repositorio.
