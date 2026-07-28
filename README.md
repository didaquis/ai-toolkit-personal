# ai-toolkit-personal

Fuente de verdad de mis skills de IA personales. Este repositorio es un almacén versionado: aquí se documentan y mantienen las skills, y desde aquí se copian a mano a los proyectos que las necesiten.

## Cómo se usa

1. **Usar una skill en un proyecto:** copia su carpeta completa desde `skills/` al proyecto de destino (por ejemplo, a `.claude/skills/` del proyecto).
2. **Mejorar una skill:** si al usarla en un proyecto la mejoras, trae los cambios de vuelta aquí para que este repo siga siendo la versión canónica.
3. **Crear una skill nueva:** añade una carpeta en `skills/` siguiendo la convención de abajo.

No hay symlinks, plugins ni scripts de instalación: el flujo es copiar carpetas a mano.

## Convenciones

- Cada skill vive en `skills/<nombre-en-kebab-case>/`.
- Cada skill es nombrada con el prefijo `d-`, por tanto el nombre es `skills/d-<nombre-en-kebab-case>/`.

## Estructura

```
ai-toolkit-personal/
├── README.md
└── skills/
    └── <una carpeta por skill>
```
