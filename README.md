# ai-toolkit-personal

Fuente de verdad de mis skills de IA personales. Este repositorio es un almacén versionado: aquí se documentan y mantienen las skills, y desde aquí se copian a mano a los proyectos que las necesiten.

## Cómo se usa

1. **Usar una skill en un proyecto:** copia su carpeta completa desde `skills/` al proyecto de destino (por ejemplo, a `.claude/skills/` del proyecto).
2. **Mejorar una skill:** si al usarla en un proyecto la mejoras, trae los cambios de vuelta aquí para que este repo siga siendo la versión canónica.
3. **Crear una skill nueva:** añade una carpeta en `skills/` siguiendo la convención de abajo.

No hay symlinks, plugins ni scripts de instalación: el flujo es copiar carpetas a mano.

## Convenciones

- Cada skill vive en `skills/<nombre-en-kebab-case>/`.
- Cada skill tiene un `SKILL.md` con frontmatter YAML (formato estándar de Agent Skills):

  ```markdown
  ---
  name: nombre-de-la-skill
  description: Cuándo usar esta skill y qué hace.
  ---

  Instrucciones de la skill...
  ```

- Los ficheros de apoyo (scripts, plantillas, referencias) van dentro de la carpeta de la skill.

## Estructura

```
ai-toolkit-personal/
├── README.md
└── skills/
    └── <una carpeta por skill>
```
