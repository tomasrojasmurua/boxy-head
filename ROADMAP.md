# Roadmap — Cabeza Cúbica

Marca con `[x]` lo que vayas completando.

## Hecho

- [x] v0.1: arena fija, 7 armas, 4 enemigos, oleadas, cajas, sonido sintetizado, controles de teclado, ratón y táctiles.
- [x] v0.3: multijugador en línea hasta 4 (salas con código, link para WhatsApp, anfitrión con autoridad, reaparición por oleada).
- [x] v0.2: nuevo aspecto. Cámara que sigue al jugador sobre un mapa de 1600×1200, personajes y escenario en 3D de bloques con contorno, suelo crema con sangre permanente, cadáveres, pilares octogonales, marcador de 9 dígitos con combo, puntos flotantes, etiqueta de arma y munición sobre el jugador.

## 0. Verificación y ajuste del aspecto (siguiente paso)

- [ ] Jugar 5 oleadas completas en escritorio y revisar la consola del navegador.
- [ ] Comparar con la imagen de referencia y ajustar: tamaño de personajes, altura de pilares, cantidad de sangre, tamaño de la tipografía del marcador.
- [ ] Medir rendimiento con 100 o más zombis en pantalla (fotogramas por segundo) y optimizar si baja de 50.
- [ ] Probar en móvil: palancas, barra de armas, pausa al cambiar de pestaña, tamaño del HUD.
- [ ] Ajustar balance: umbrales de desbloqueo, daño, frecuencia de cajas, vida de enemigos, ritmo de aparición.
- [x] Corregir zombis que se traban en pilares y bloques (campo de flujo con Dijkstra).

## 1. Jugabilidad

- [ ] Barriles y cubos sólidos que bloquean el paso.
- [ ] Mejoras de arma compradas con puntos entre oleadas (cadencia, daño, cargador).
- [ ] Recarga y cargadores en lugar de munición continua (opcional).
- [ ] Jefes cada 5 oleadas.
- [ ] Más tipos de enemigo (explosivo, que invoca, blindado).
- [ ] Combos con bonificaciones especiales (por ejemplo, vida extra al llegar a x50).

## 2. Modos y contenido

- [ ] Modo cooperativo local para 2 jugadores (teclado compartido o teclado y mando), con cámara que encuadra a ambos.
- [ ] Varios mapas con distintas distribuciones de pilares y suelos.
- [ ] Modos extra: supervivencia sin fin, contrarreloj, oleadas con modificadores.
- [ ] Selector de dificultad.

## 3. Arte, sonido y sensación

- [ ] Animación de caminar de brazos y piernas, y animación de muerte.
- [ ] Más variedad visual de zombis (ropa, pelo, accesorios) para que la multitud no se vea repetida.
- [ ] Miembros y trozos que salgan despedidos con las explosiones.
- [ ] Música de fondo y mejores efectos de sonido.
- [ ] Cadáveres que se desvanezcan al estamparse en el suelo.

## 4. Técnico

- [ ] Dividir en módulos ES (`js/`) si el archivo supera ~1500 líneas (ya está cerca).
- [ ] Guardar ajustes (sonido, dificultad) y récords por modo.
- [ ] Soporte para mando (Gamepad API).
- [ ] Pruebas automáticas de lógica pura (colisiones, oleadas, combo).
- [ ] Publicación (GitHub Pages u otro hosting estático).
- [ ] En línea: que otro jugador tome el lugar del anfitrión si se va.
- [ ] En línea: chat rápido o frases predefinidas.

## 5. Pulido

- [ ] Pantalla de ajustes y de ayuda dentro del juego.
- [ ] Accesibilidad: opción de reducir efectos, contraste y tamaño del HUD.
- [ ] Tabla de récords local.
