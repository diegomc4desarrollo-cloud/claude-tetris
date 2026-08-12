# CLAUDE.md

Este archivo proporciona guía a Claude Code (claude.ai/code) al trabajar con código en este repositorio.

## Proyecto

Implementación de Tetris en JavaScript vanilla usando HTML5 Canvas y CSS. Sin dependencias, sin proceso de build, sin package.json.

## Cómo ejecutar el juego

Abre `index.html` directamente en el navegador, o sírvelo con cualquier servidor estático:

```bash
python3 -m http.server 8000
# o
npx serve .
```

No hay herramientas de build, lint ni tests en este repo: los cambios en `game.js`/`index.html`/`style.css` se aplican al recargar la página.

## Arquitectura

Todo el juego vive en tres archivos que cooperan: `index.html` (estructura del DOM y canvas), `style.css` (tema dark/retro), `game.js` (toda la lógica, en un único scope global, sin módulos).

Flujo de `game.js`:

- **Estado**: un conjunto de variables `let` a nivel de módulo (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.) contiene todo el estado del juego — no hay un contenedor de estado ni una clase.
- **Modelo del tablero**: una matriz `ROWS × COLS` donde cada celda es `0` (vacía) o un índice de color `1–7` que identifica qué pieza quedó fijada ahí.
- **Piezas**: definidas como matrices cuadradas en `PIECES`; la rotación se hace mediante `rotateCW` (transposición + inversión), no guardando estados pre-rotados.
- **Colisión** (`collide`): comprueba los límites del tablero y el solape con celdas ya fijadas.
- **Wall kicks** (`tryRotate`): tras rotar, prueba los desplazamientos `[0, -1, 1, -2, 2]` columnas hasta encontrar una posición sin colisión.
- **Bucle del juego** (`loop`): impulsado por `requestAnimationFrame`; acumula el tiempo transcurrido y baja la pieza una fila cuando `dropAccum >= dropInterval`.
- **Limpieza de líneas** (`clearLines`): recorre de abajo hacia arriba, elimina (`splice`) las filas completas e inserta (`unshift`) filas vacías arriba; la puntuación usa `LINE_SCORES = [0, 100, 300, 500, 800]` multiplicado por `level`.
- **Nivel/velocidad**: el nivel sube cada 10 líneas; `dropInterval = max(100, 1000 - (level - 1) * 90)` ms.
- **Pieza fantasma** (`ghostY`): proyecta la pieza actual hacia abajo hasta su posición de aterrizaje, dibujada con `globalAlpha = 0.2`.
- **Renderizado**: `draw()` redibuja todo el canvas del tablero en cada frame (grid, bloques fijados, fantasma, pieza actual); `drawNext()` renderiza la vista previa de la siguiente pieza en un canvas separado.

Constantes ajustables al inicio de `game.js`: `COLS`, `ROWS`, `BLOCK` (px por celda), `COLORS`, `LINE_SCORES`. Si cambias `COLS`/`ROWS`/`BLOCK`, actualiza también `width`/`height` del `<canvas id="board">` en `index.html` para que coincidan (`COLS × BLOCK` y `ROWS × BLOCK`).

La entrada de teclado se gestiona con un único listener `keydown` al final de `game.js` (flechas para mover/rotar/soft-drop, Espacio para hard drop, P para pausar); `init()` se llama una vez al cargar y está conectado al botón de reinicio.
