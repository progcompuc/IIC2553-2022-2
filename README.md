# Taller de Programación Competitiva II
Página para IIC2553-2022-2.

## Instalación
Esta página usa el módulo mkdocs material

```bash
pip install mkdocs-material
```

## Uso

Todas las entradas están en formato markdown el directorio `docs/`. Si se quiere añadir una nueva entrada se tiene que modificar el archivo `mkdocs.yml` en la sección de nav. 

### Comandos útiles

Compilar la página

```bash
mkdocs build
```

Iniciar un servidor con los cambios en directo

```bash
mkdocs serve
```

Hacer deploy

```bash
mkdocs gh-deploy
```