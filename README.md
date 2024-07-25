# CACAO Workshop, IT Summit 2024

MKDocs website for CACAO Workshop, IT Summit 2024

TBD: pretty much everything, set up action, set GH Pages to DNS, build website, add pages, convert all `rst` materials and update

https://cacao-itsummit2024.cyverse.org/

# Development

## Testing locally

Follow the [Material for MKDocs instructions](https://squidfunk.github.io/mkdocs-material/getting-started/)

```
$ git clone https://github.com/CyVerse-learning-materials/cacao-uarizona-its2024.git
$ cd cacao-uarizona-its2024
$ pip install -r requirements
$ mkdocs serve
```

## Action with ReadTheDocs Theme

This is a template that uses the [MkDocs deploy](https://github.com/marketplace/actions/deploy-mkdocs) GitHub action.

We are using the `@nomaterial` branch for the [Action](.github/workflows/main.yml) to produce the ReadTheDocs style layout with the `theme: readthedocs` in the [mkdocs.yml](./mkdocs.yml):

```
theme:
  name: readthedocs
```

## Action with Material Theme

To change to [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) theme, change [Action](./github/workflows/main.yml) to `@master` and set `theme: material` in the [mkdocs.yml](./mkdocs.yml):

```
theme:
  name: material
```
