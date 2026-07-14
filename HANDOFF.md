# Handoff: Rutinas Espartaco — Generador de Rutinas de Hipertrofia

## Overview
App móvil (prototipo) que genera rutinas de entrenamiento de hipertrofia a medida. El usuario elige género, cantidad de días de entrenamiento por semana (1 a 5) y qué grupos musculares quiere trabajar (pecho, espalda, hombros, bíceps, tríceps, piernas, abdominales). La app reparte automáticamente esos grupos musculares entre los días disponibles y arma una rutina completa con ejercicios, series, repeticiones y descanso. Permite regenerar los ejercicios (misma configuración, otra selección), y guardar/nombrar rutinas favoritas para volver a cargarlas después.

## About the Design Files
Los archivos en `design_source/` son **referencias de diseño construidas en HTML** — un prototipo de alta fidelidad que muestra el look final y el comportamiento completo, no código de producción para copiar tal cual. La tarea, si se decide llevar esto a un codebase real, es **recrear este diseño en el entorno de destino** (React con build estándar, React Native, Vue, SwiftUI/Kotlin nativo, etc.) usando los patrones y librerías ya establecidos en ese proyecto — o, si no existe codebase todavía, elegir el framework más adecuado. `docs/index.html` (en la raíz de este paquete) es la versión autocontenida y ejecutable del mismo prototipo, útil para testear el comportamiento sin tocar código.

## Fidelity
**Alta fidelidad (hifi).** Colores, tipografía, espaciado, animaciones y microinteracciones son finales, no placeholders. Recrear pixel a pixel donde sea posible.

## Screens / Views

### 1. Configuración (pantalla de inicio)
**Propósito:** elegir género, días de entrenamiento y grupos musculares, y generar la rutina.

**Layout:** columna única, scroll vertical, ancho de celular (~390px). De arriba a abajo:
- Header (`padding: 6px 20px 18px`): botón ícono "Mis rutinas" (lista) fijo arriba a la derecha (`position:absolute; top:6px; right:20px`); debajo, centrado: eyebrow con ícono de mancuerna + texto "GENERADOR · HIPERTROFIA"; título "Armá tu rutina" (una sola línea); subtítulo descriptivo (máx. 280px de ancho, centrado).
- Card "1 · Género": dos botones tipo pill lado a lado (Hombre / Mujer), 50/50 del ancho, `gap:8px`.
- Card "2 · Días por semana": 5 botones cuadrados en fila (1 a 5), `aspect-ratio:1`, `gap:8px`.
- Card "3 · Grupos musculares": chips en grilla flexible centrada, ~3 por fila (`flex:1 1 27%`), 7 opciones: Pecho, Espalda, Hombros, Bíceps, Tríceps, Piernas, Abdominales.
- Botón CTA "Generar rutina": ancho completo (`width:calc(100% - 40px)`), deshabilitado (opacidad .3) si no hay ningún grupo muscular elegido.

**Componentes:**
- Cards: fondo `oklch(19% .022 275)`, borde `1px solid oklch(31% .03 275)`, `border-radius:16px`, padding `16px`.
- Pills/chips/día no seleccionados: fondo `oklch(24% .024 275)`, borde `oklch(31% .03 275)`, texto blanco.
- Seleccionado: fondo/borde en verde neón `oklch(87% .27 142)` (texto pasa a oklch(13% .02 275) en pills/días; en chips queda fondo verde translúcido `oklch(87% .27 142 / .16)` con texto y borde verde sólido), con `box-shadow` de glow (`0 0 18px oklch(87% .27 142 / .55)` en pills/días, más sutil en chips).

### 2. Rutina generada
**Propósito:** ver la rutina resultante, navegar entre días, expandir el detalle de cada ejercicio, regenerar o guardar.

**Layout:**
- Header: fila superior con dos botones ícono iguales (38×38, blanco con borde blanco): "volver" (izquierda) y "guardar rutina" (derecha). Debajo: eyebrow con género + días/semana; título con la lista de grupos musculares elegidos (envuelve en varias líneas si hace falta, no debe desbordar el ancho de pantalla).
- Fila de stats (3 columnas): días totales, ejercicios totales, cantidad de músculos — número grande en verde + etiqueta chica debajo.
- Tabs de días (scroll horizontal): "Día 1", "Día 2"... el tab activo tiene fondo verde neón.
- Subtítulo del día activo: qué grupos musculares corresponden a ese día (p. ej. "Pecho + Tríceps").
- Lista de ejercicios (tarjetas apiladas, `gap:10px`): cada tarjeta tiene un número a la izquierda (columna fija 46px), y a la derecha: tag de tipo de ejercicio (FUERZA/SUPERSERIE/VOLUMEN/BURNOUT/CORE, cada uno con su color), nombre del ejercicio, submúsculo trabajado, y 3 datos en mono (series / reps / descanso). Al tocar la tarjeta se expande mostrando la nota técnica del ejercicio.
- Botón "Regenerar ejercicios" (relleno magenta neón, no ghost) al final de la lista.

### 3. Mis rutinas (guardadas)
**Propósito:** ver rutinas guardadas anteriormente y volver a cargarlas.

**Layout:** header con botón "volver" + título "Mis rutinas" + subtítulo. Si no hay rutinas guardadas: mensaje vacío centrado. Si hay: lista de tarjetas, cada una con nombre (fuente display), metadatos (género · días · grupos musculares) y dos botones: "Cargar" (relleno verde) y "Eliminar" (outline).

### Modal: Guardar rutina
Modal tipo bottom-sheet (se desliza desde abajo, fondo oscuro semitransparente detrás): input de texto para nombrar la rutina + botones "Cancelar" / "Guardar".

## Interactions & Behavior
- **Generación:** al tocar "Generar rutina", se arma la rutina con un seed nuevo (`Date.now()`), determinista dado ese seed — permite reproducir la misma rutina si se guarda el seed.
- **Regenerar:** genera un seed nuevo con la misma configuración (género/días/músculos), cambiando qué ejercicios concretos salen de cada grupo muscular (sin cambiar el reparto de músculos por día).
- **Reparto de días:** ver lógica exacta en la sección "State Management" abajo.
- **Expandir ejercicio:** un solo ejercicio expandido a la vez por día (acordeón simple, no multi-expand).
- **Guardar rutina:** abre modal, pide nombre, persiste en `localStorage`.
- **Cargar rutina guardada:** restaura género/días/músculos/seed exactos y navega a la pantalla de rutina.
- **Animaciones:**
  - Tarjetas de ejercicios y de rutinas guardadas: entrada con fade + translateY(12px→0), 0.35s ease, con stagger de 45ms entre ítems.
  - Detalle expandido de ejercicio: fade + translateY(-6px→0), 0.22s ease.
  - CTA principal (verde) y botón "Regenerar" (magenta): glow pulsante infinito, 2.6s ease-in-out (box-shadow oscila entre alfa .4 y .75). Se desactiva completamente si el botón está disabled.
  - Todos los botones/chips/tabs/pills: `transform: scale(.94)` al tocar (active state), con transición de 150ms; los que tienen estado on/off también transicionan `background`, `border-color` y `box-shadow` en 250ms.
- **Responsive:** pensado para un viewport de celular fijo (~390px de ancho), dentro de un marco de iPhone en el prototipo — en producción debería adaptarse a distintos anchos de pantalla mobile.

## State Management
Estado necesario (nombres tal cual el prototipo, adaptar a lo que use el codebase destino):
- `screen`: `"config" | "routine" | "saved"`
- `gender`: `"hombre" | "mujer"`
- `days`: número, 1 a 5
- `muscles`: array de claves de músculo seleccionadas (`pecho`, `espalda`, `hombros`, `biceps`, `triceps`, `piernas`, `abdominales`)
- `seed`: string/número usado para determinismo de la generación (cambia en "Generar" y en "Regenerar")
- `activeDay`: índice del día activo en la vista de rutina
- `openEx`: índice del ejercicio expandido (o `null`)
- `saved`: array de rutinas guardadas (persistido en `localStorage`, clave `rutinas-hipertrofia-saved`), cada una con `{id, name, gender, days, muscles, seed, meta}`
- `showSaveModal`, `routineNameInput`: estado del modal de guardado

**Lógica de reparto de días (`buildDaySplit`):**
- Cada grupo muscular tiene un "peso" relativo: piernas 3, espalda 2.5, pecho 2, hombros 1.5, bíceps/tríceps/abdominales 1.
- Si `days >= cantidad de músculos elegidos`: cada músculo tiene garantizado al menos 1 día propio; los días sobrantes se reparten iterativamente al músculo con mayor ratio peso/días-ya-asignados (los grupos más grandes —piernas, espalda, pecho— tienden a recibir más días si sobran).
- Si `days < cantidad de músculos elegidos`: los músculos se reparten round-robin entre los días disponibles (varios músculos por día, para "reclutar" el máximo posible en cada sesión).
- Ejercicios por músculo por día: 5 si ese día tiene 1 solo músculo, 3 si tiene 2, 2 si tiene 3, 1 si tiene 4 o más — así el volumen total por sesión se mantiene razonable.
- Selección de ejercicios: de la lista de ejercicios de ese músculo filtrada por género (`ambos` + el género elegido), shuffle determinista con el `seed` (mulberry32 + hash simple del string músculo+seed+día), y se prioriza que el primer ejercicio elegido sea de tipo FUERZA si hay alguno disponible en la lista filtrada.

## Design Tokens

**Colores (formato oklch):**
- Fondo app: `oklch(13% .02 275)` (negro frío)
- Cards: `oklch(19% .022 275)` · Cards secundarias/inputs: `oklch(24% .024 275)`
- Bordes: `oklch(31% .03 275)`
- Texto principal: `oklch(96% .006 275)` · Texto secundario/mute: `oklch(63% .02 275)`
- Acento primario (verde neón): `oklch(87% .27 142)`
- Acento secundario (magenta neón, usado en "Regenerar" y en el glow decorativo de fondo): `oklch(72% .27 340)`
- Colores por tipo de ejercicio: FUERZA usa el verde acento sólido; SUPERSERIE `oklch(80% .22 195)` (cian); VOLUMEN `oklch(80% .22 70)` (ámbar); BURNOUT `oklch(72% .27 340)` (magenta); CORE `oklch(78% .20 260)` (violeta) — todos con fondo al 16% de opacidad como "chip" y el mismo tono como texto.
- Fondo de la app tiene además un gradiente decorativo muy sutil: combinación de `linear-gradient` diagonal + 3 `radial-gradient` en las esquinas, mezclando el verde y el magenta al 5-12% de opacidad.

**Tipografía:**
- Display/títulos/números grandes: **Bebas Neue** (Google Fonts), regular, mayúsculas visualmente por diseño de la fuente.
- Texto de cuerpo, labels, botones: **Inter** (400 a 800).
- Datos/stats/mono (series, reps, descanso, eyebrow): **IBM Plex Mono** (400-600).

**Radios y espaciados:**
- Cards grandes: `border-radius:16px` · Tarjetas de ejercicio/guardadas: `14px` · Botones/chips/días/CTA: `12px`–`14px` · Tabs/pills: `999px` (full pill).
- Gap estándar entre elementos de una fila: `8px`. Padding de cards: `16px`. Margen lateral de página: `20px`.

## Assets
- **`logo-espartaco.svg`** — logo de marca "Rutinas Espartaco" (mancuerna + puño), provisto por el usuario. En el prototipo actual **no está montado en la UI** (se agregó y luego se revirtió a pedido); queda disponible en `design_source/` por si se retoma.
- Ícono de mancuerna inline (SVG simple, 5 rectángulos) usado junto al eyebrow del header — ver `ICON_LOGO` en el código fuente.
- Sin fotos/videos reales: los ejercicios no tienen imagen; se decidió explícitamente no usar placeholders de imagen en el detalle expandido (solo texto).

## Files
- `design_source/Generador de Rutinas.dc.html` — archivo principal: template + lógica (clase `Component`) + base de datos de ejercicios (`EXDB`, 7 grupos musculares × 6-9 ejercicios cada uno) + algoritmo de generación de rutinas.
- `design_source/ios-frame.jsx` — componente de marco de iPhone (bezel, status bar, home indicator) usado solo para la presentación visual del prototipo; no es parte de la lógica de la app.
- `design_source/support.js` — runtime interno del entorno de diseño que hace correr el archivo `.dc.html`; no es necesario ni reutilizable en un codebase de producción.
- `design_source/logo-espartaco.svg` — logo de marca (ver "Assets").
- `docs/index.html` — versión autocontenida y ejecutable de toda la app (recomendada para testear el comportamiento sin leer código).
