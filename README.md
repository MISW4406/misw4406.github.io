# Repositorio del sitio del curso MISW4406
## Diseño y construcción de soluciones no monolíticas

En este repositorio puede encontrar toda la documentación acerca de tutoriales del curso. Este repositorio usa [Jekyll](https://github.com/BillRaymond/install-jekyll-apple-silicon/blob/main/README.md) para el compilación de archivos `.md` en páginas HTML y CSS. Finalmente, el servidor de documentación se despliega usando [Github Pages](https://pages.github.com/).

### Correr localmente

Si desea correr el servidor localmente ejecute el siguiente comando para instalar todas las dependencias:

```bash
bundle install
```

Una vez con todas las dependencias puee ejectuar el servidor. Si usa el parámetro `--livereload` le va a permitir ir haciendo cambios y verlos en su browser (sin necesidad de tener que parar y correr el servidor con cada nuevo cambio).

```bash
bundle exec jekyll serve --livereload
```
