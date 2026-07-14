# Rutinas Espartaco — Generador de Rutinas de Hipertrofia

Generador de rutinas de entrenamiento de hipertrofia. El usuario elige género, cantidad de días de entrenamiento (1 a 5) y grupos musculares (pecho, espalda, hombros, bíceps, tríceps, piernas, abdominales), y la app arma automáticamente una rutina completa, repartiendo los grupos musculares elegidos entre los días disponibles.

## ¿Qué hay en esta carpeta?

- **`docs/index.html`** — la app completa, autocontenida (sin dependencias externas salvo Google Fonts vía CDN). Se puede abrir directo en un navegador con doble click, o servir con cualquier servidor estático. Esto es lo que hay que compartir/testear.
- **`design_source/`** — los archivos fuente originales del prototipo (ver sección "Sobre los archivos de diseño" abajo).
- **`HANDOFF.md`** — documentación técnica detallada del diseño (pantallas, estados, tokens de diseño, lógica de generación) pensada para que un desarrollador lo reimplemente en un stack de producción (React, Vue, nativo, etc.), si en algún momento se quiere llevar esto más allá del prototipo.

## Cómo probarlo ya mismo

Opción más simple: abrir `docs/index.html` directamente en cualquier navegador (Chrome, Safari, Firefox). No requiere build, no requiere `npm install`, no requiere servidor.

Si preferís servirlo (por ejemplo para probar en el celular en la misma red):
```bash
cd docs
npx serve .
```

## Cómo subirlo a GitHub

```bash
cd handoff
git init
git add .
git commit -m "Rutinas Espartaco — prototipo del generador de rutinas"
git branch -M main
git remote add origin <URL_DE_TU_REPO>
git push -u origin main
```

Con GitHub Pages podés además publicarlo como sitio web gratis: Settings → Pages → Deploy from branch → `main` → carpeta `/docs`. Así cualquiera puede probarlo desde un link, sin instalar nada.

## Sobre los archivos de diseño

Los archivos en `design_source/` son un **prototipo funcional construido en HTML/React**, hecho como diseño de referencia de alta fidelidad (hifi): colores, tipografía, animaciones y comportamiento están definidos y son los finales. Corren con un runtime propio del entorno de diseño (`support.js`), no con una toolchain estándar de React (sin bundler, sin npm). Es perfectamente funcional y testeable tal cual (ver `docs/index.html`), pero si el objetivo final es integrarlo a una app de producción real (por ejemplo con Next.js, React Native, SwiftUI, etc.), lo recomendable es **recrear este diseño usando el stack y los componentes ya establecidos en ese codebase**, tomando este prototipo como referencia exacta de UI y comportamiento — no copiar el HTML tal cual. `HANDOFF.md` tiene el detalle necesario para hacer esa recreación sin necesidad de haber estado en la conversación original.

## Stack técnico del prototipo

- HTML + React (vía runtime del entorno de diseño, sin build step)
- Fuentes: Anton (títulos), Inter (texto), IBM Plex Mono (datos/stats) — Google Fonts
- Sin backend: toda la generación de rutinas y el guardado de "mis rutinas" corre en el cliente, con `localStorage` para persistir rutinas guardadas
- Marco de iPhone (`ios-frame.jsx`) usado solo para la presentación visual del prototipo
