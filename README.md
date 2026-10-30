# BR41N D4M4G3

Minijuego web oscuro e inquietante, en tercera persona (vista 3/4 desde arriba), donde el personaje y todo el mundo están hechos de **cerebritos diminutos** dibujados solo con contornos sobre fondo negro.

Es **un solo archivo** (`index.html`), sin librerías, sin frameworks y sin imágenes ni audios externos: los gráficos se dibujan con Canvas 2D y todo el sonido se genera con Web Audio.

## Cómo probarlo

Abre `index.html` con doble clic, o sirve la carpeta con cualquier servidor estático:

```bash
python -m http.server 8000
```

y entra a `http://localhost:8000/index.html`.

## Cómo se juega

Escapa de tres cuartos: una llave y una puerta, unas palancas que hay que tirar en orden y un código de tres dígitos escondido por el mapa. Al final hay dos puertas, y solo una es la buena.

| Tecla | Acción |
| --- | --- |
| `W A S D` o flechas | moverse |
| `E` | interactuar (llaves, palancas, puertas, panel) |
| `P` o `Esc` | pausa |
| `M` | silenciar |
| `Enter` o `Espacio` | empezar desde la pantalla de inicio |
| `F3` | contador de fps (para medir) |

En pantallas táctiles aparece un joystick virtual. En la pantalla de inicio se elige personaje con las flechas del selector, con `←` `→` (o `A` `D`) o tocando al personaje.

## Personajes

Se elige uno al empezar (la elección no se guarda). Es solo estético: todos tienen la misma caja de colisión, velocidad y puzzles.

| Personaje | Cómo es |
| --- | --- |
| Encapuchado | túnica con capucha vacía y un farol de donde sale su luz |
| Humano | silueta humana con esqueleto invisible |
| Cerebro con patas | un cerebro con un ojo, cuatro patitas y la médula arrastrando |
| Ojo | un globo ocular flotante con nervio óptico |
| Gusano | parado como cobra, repta al caminar |
| Araña | una mano con un ojo en la palma que camina sobre seis dedos |

## Estructura

```
index.html      el juego completo
pruebas/        bancos de prueba de los personajes (cada uno es una página suelta)
LICENSE         MIT
```

Las páginas de `pruebas/` son independientes del juego y se abren con doble clic. Cada una muestra un personaje a tamaño real y ampliado, caminando solo o con el teclado:

- `cerebro-patas.html`, `ojo.html`, `gusano.html`, `arana.html`, `encapuchado.html`: los personajes del juego.
- `muleco-comparar.html`: cuatro variantes del encapuchado lado a lado.
- `muleco-ideas.html`: nueve borradores de personaje (de aquí salieron varios del juego; algunos se descartaron).
- `fase0-prueba.html`: el cerebrito mini a distintos tamaños.

## Configurarlo

Casi todo lo ajustable está agrupado en el objeto `CONFIG` al inicio del script de `index.html`: tamaños, velocidades, luz, tiempos, sonido, la posición del título y de los controles de la portada, etc.

Ojo: `CONFIG.pausa.urlSitio` es la dirección a la que lleva el botón **"Volver al sitio"** (pausa y pantalla de SALVACIÓN). Apunta al sitio del autor: cámbiala si haces tu propia versión.

## Agregar un personaje

Cada personaje es una función `gen(P, c)` que, en cada cuadro, dice dónde va cada cerebrito.

1. Copia una página de `pruebas/` (por ejemplo `ojo.html`) y escribe tu generador ahí, hasta que se vea bien en las cuatro direcciones.
2. Pasa tu función y su bloque de `CONFIG` a `index.html` y regístrala en la lista `PERSONAJES`:

   ```js
   { id: 'mio', nombre: 'Mío', gen: genMio, farol: false, crear: () => crearEnjambreGen(genMio) }
   ```

Lo que recibe `gen`:

- `P(x, y, tam, tono, alfa)` pone un cerebrito. `tam` es `'m'`, `'s'` o `'xs'`; `tono` es `0` (normal), `1` (tenue) o `2` (brillo). Las combinaciones disponibles están en `CAPA_SETS`. `alfa` (0 a 1) sirve para desvanecer lo que queda atrás.
- `c` trae `t` (tiempo), `mov` (0 quieto, 1 caminando), `fase` (ciclo de la caminata), `yaw` (hacia dónde mira: 0 de frente, π de espaldas), `dx`/`dy` (rumbo) y `rastro(lag)` (dónde estaba hace `lag` segundos).
- Las coordenadas son locales: `(0, 0)` es el suelo bajo el personaje y la `y` negativa es hacia arriba.

Reglas que ayudan a que se vea bien:

- Siempre pon **la misma cantidad** de cerebritos en cada cuadro.
- Nunca cambies de golpe el tamaño, el tono o la forma de un rasgo: usa `alfa` y posiciones que se deslicen.
- La dirección debe leerse en la silueta, no en detalles de 3 a 6 px.
- Mantén el cuerpo visible cerca de los pies: la colisión es solo la de los pies (los pasillos miden 90 px) y un cuerpo que flota muy alto parece caber donde no cabe.

## Licencia

[MIT](LICENSE)
