# Cabeza Cúbica

Shooter cenital de oleadas de zombis hecho con Canvas, en un solo `index.html`. Se juega solo o en línea con hasta 4 jugadores.

## Jugar en línea

1. Abre el juego y pulsa **Crear sala**.
2. Comparte el link con **Compartir por WhatsApp** o **Copiar link**. También pueden entrar escribiendo el código de 4 letras.
3. Cuando estén todos, pulsa **Empezar**. Si alguien llega tarde, entra a la partida en curso.

Quien crea la sala es el anfitrión: su navegador mueve a los zombis, así que debe dejar el juego abierto y en primer plano. Los jugadores que caen vuelven en la siguiente oleada con media vida.

## Controles

- Mover: `W` `A` `S` `D` o flechas. Apuntar: ratón. Disparar: clic o `Espacio`.
- Armas: `1`–`7`, `Q` `E` o rueda.
- Pantalla táctil: palanca izquierda para mover, derecha para apuntar y disparar.

## Publicar

Es un sitio estático. En GitHub: **Settings → Pages → Deploy from a branch → `main` / `(root)`**.

## Detalles técnicos

Ver [CLAUDE.md](CLAUDE.md). La conexión entre jugadores usa [PeerJS](https://peerjs.com/) (MIT), incluido en `vendor/`.
