# CLAUDE.md — Cabeza Cúbica

Contexto para continuar el desarrollo de este proyecto.

## Qué es

Shooter cenital de oleadas de zombis, para un jugador o en línea hasta 4. Un único `index.html` con CSS y JS inline, sin build. La única dependencia es `vendor/peerjs.min.js` (PeerJS 1.5.5, licencia MIT), que se carga solo al crear o unirse a una sala. Canvas 2D con vista de **800×600** (4:3) sobre un mundo de **1600×1200** con cámara que sigue al jugador. Escalado por CSS y por `devicePixelRatio` (máx. 2).

## Reglas del proyecto

- Idioma de la interfaz y los textos: **español**.
- Sin librerías ni frameworks salvo que el roadmap lo pida. Si el archivo crece demasiado, dividir en módulos ES (`js/*.js`) servidos con un servidor local.
- Todo el arte se dibuja por código. Look: pseudo-3D de bloques (proyección oblicua con contorno oscuro), suelo crema cubierto de sangre roja, pilares octogonales blancos, marcador con combo. Los personajes son diseños propios; no copiar sprites, colores ni nombres de otros juegos.
- Nombre y marca propios: no usar el nombre ni los personajes del juego que inspiró el género.
- Tema único oscuro a propósito (concreto gris, amarillo de señal de peligro `#f2c230`, rojo sangre). Las fuentes son Bungee (títulos) y Barlow Condensed (interfaz).
- Mantener soporte táctil y `prefers-reduced-motion` (sin temblor de pantalla).

## Arquitectura del script

El JS está organizado en secciones en este orden:

1. **Configuración**: `VW/VH` (vista), `WW/WH` (mundo), `WEAPONS`, `ZT` (tipos de zombi, con `look` de colores), `PILLARS`, `BLOCKS`, `COMBO_TIME`. Casi todo el balance se ajusta aquí.
2. **Utilidades**: `rand`, `clamp`, `inWall`, `collideWorld` (pilares circulares y bloques rectangulares). **Rutas**: `wallDist`, `clearLine` (línea de visión con el radio del zombi), `mkNav`, `navUpdate`, `navDir`.
3. **Sonido**: WebAudio sintetizado (`tone`, `noise`, `sfx`). Se inicia con el primer clic en Jugar.
4. **Estado**: variables globales de partida y `reset()`.
5. **Volúmenes 3D**: `extrude`, `box`, `octagon`, `drawBoxer`, `drawCorpse`. Cada parte del cuerpo es un prisma extruido hacia arriba. **Suelo y sangre**: canvas fuera de pantalla `bgc` del tamaño del mundo (`paintFloor`, `splat`, `scorch`); los cadáveres antiguos se estampan ahí.
6. **Partículas**, **Entidades** (`mkZombie`, oleadas), **Armas y disparo** (`fire`, `explode`, `damageZombie`, `onKill`).
7. **Entrada**: teclado, ratón (pointer events), dos palancas táctiles, rueda.
8. **Actualización** `update(dt)`: simulación completa (un jugador o anfitrión): jugadores, oleadas, zombis, balas, bolas de fuego, minas, cajas y limpieza. `driveLocal` mueve al jugador de esta pantalla, `updateFx` corre en todas las pantallas (partículas, estelas, posiciones suavizadas de compañeros).
9. **Dibujo** `render()` (cámara, objetos ordenados por profundidad `y`) y `hud()` (en coordenadas de pantalla: marcador con ceros, anillo de combo, barra de armas).
10. **Red** (`net`): salas con PeerJS. Ver «Multijugador» abajo.
11. **Flujo** (`start`, `gameOver`, `showOver`) y bucle principal con subpasos de máx. 1/60 s.

Estados: `state` es `'menu' | 'play' | 'over'`, más la bandera `paused` (solo sin red).

## Multijugador

- `players` es la lista de jugadores y `me` el de esta pantalla. Cada jugador tiene su vida, munición (`ammo`) y arma; las bajas, el puntaje, el combo y los desbloqueos son del equipo.
- Los zombis persiguen al jugador vivo más cercano; el campo de flujo usa a todos los vivos como fuentes.
- Un jugador caído vuelve al empezar la siguiente oleada con media vida (`startWave`). La partida acaba cuando no queda nadie vivo.
- Topología: quien crea la sala es el **anfitrión** y tiene la autoridad. Simula con `update()` y manda `snapshot()` 20 veces por segundo. Los **invitados** no simulan: `clientUpdate()` mueve a su propio jugador sin esperar a la red, manda `{t:'in', x, y, a, f, w, ws, tp}` 30 veces por segundo, e interpola zombis entre fotos (`applySnap`).
- Todo lo visual o sonoro que nace en la simulación (sangre, explosiones, puntos flotantes, avisos) pasa por `fx(tipo, ...)`: se ejecuta en el anfitrión y viaja en el campo `fx` de la foto siguiente. Para un efecto nuevo: entrada en `FX` y llamada con `fx()`, con argumentos que se puedan pasar a JSON.
- `tp` cuenta las reapariciones: el anfitrión ignora posiciones de un invitado con `tp` viejo. `ws` cuenta los cambios de arma pedidos por el invitado.
- Mensajes: `hola`/`bienvenida`/`llena`/`sala` (sala), `start`, `s` (foto), `in` (entrada), `over`, `ping` (latido cada segundo; 10 s de silencio da la conexión por perdida).
- Id en el servidor de señalización: `cabeza-cubica-v1-` + código de 4 letras. Link de invitación: `?sala=CODIGO`. Si cambia el protocolo de red, subir el `v1` del prefijo.
- Señalización con el servidor público gratuito de PeerJS (`0.peerjs.com`); los datos van directo entre navegadores (WebRTC) o por los servidores TURN de PeerJS si la red no deja conexión directa.

## Probar el multijugador en local

```bash
npm i peer@1.0.2 && node -e "require('peer').PeerServer({port:9000,path:'/',host:'127.0.0.1'})" &
npx http-server . -p 8080
```

Abrir `http://127.0.0.1:8080/?servidor=127.0.0.1:9000` en varias ventanas (o perfiles). El parámetro `servidor` se arrastra al link de invitación. Sin él se usa el servidor público.

## Convenciones

- Las entidades son objetos simples en arrays (`zombies`, `bullets`, `crates`, ...). Se marcan con `dead = true` y se filtran al final de `update`.
- Para añadir un arma: nueva entrada en `WEAPONS`, caso en `fire()`, sonido en `sfx()`. La barra de armas y las teclas `1`–`7` están ligadas a 7 slots; al pasar de 7, ajustar `SLOT_W`, el regex de `Digit[1-7]` y el HUD.
- Para añadir un enemigo: entrada en `ZT`, regla en `pickType()` y, si tiene comportamiento propio, rama en el bucle de zombis de `update()`.
- Para añadir un mapa: nuevo arreglo de paredes, `paintFloor()` parametrizable y reconstruir `NAVS` con `mkNav()` a partir de las paredes nuevas.
- Las explosiones encadenan por recursión usando banderas `dead`; no eliminar esas banderas.

## Cómo comprobar cambios

```bash
sed -n '/^<script>/,/^<\/script>/p' index.html | sed '1d;$d' > /tmp/check.js && node --check /tmp/check.js
```

Luego abrir `index.html` y jugar al menos 3 oleadas. Revisar la consola del navegador en busca de errores.

## Limitaciones conocidas

- Aún no hay una ronda completa de pruebas de juego; el balance de armas, oleadas y daño es una primera estimación.
- Los zombis van en línea recta solo cuando ven al jugador; si no, siguen un campo de flujo (`NAVS`, rejilla de 16 px, Dijkstra desde la celda del jugador). Los brutos usan una rejilla con más margen y no intentan pasar por huecos donde no caben.
- Los barriles no son sólidos.
- Los zombis aparecen a 520–680 px del jugador, fuera de cámara; no hay puertas de aparición.
- Una sola arena fija.
- En línea, si el anfitrión cambia de pestaña o bloquea el móvil, el navegador frena el juego para todos. Si el anfitrión se va, la sala se cierra (no hay migración de anfitrión).
- No hay protección contra trampas: el anfitrión confía en la posición que manda cada invitado. Pensado para jugar entre amigos.
- En la pantalla vertical del móvil la arena 4:3 queda pequeña; se juega mejor en horizontal.
- Sonido sintetizado básico; sin música.

## Prioridades siguientes

Ver `ROADMAP.md`.
