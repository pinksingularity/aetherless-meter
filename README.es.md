# Aetherless Meter

**A minimal FFXIV TUI meter by Pink Singularity / Nixie.**

Un medidor de combate para terminal usando datos del **WebSocket de IINACT / OverlayPlugin**.

Puede correr en la misma PC de FFXIV o en una laptop/mini PC ligera por LAN.

El ejecutable sigue siendo `ffxiv-tui` para que sea claro y fácil de encontrar.

## Funciones

- Interfaz TUI con Rich
- Tabla y barras de DPS
- Columnas opcionales de HPS
- Modo alliance de 24 jugadores
- Barras por job o plain
- Modo de privacidad para ocultar nombres, URL WebSocket y errores de conexión con caracteres sombreados de terminal
- Aviso automático de actualización desde GitHub Releases
- Reconexión automática
- Cierre limpio con Ctrl+C
- UI multi-idioma: inglés, español, chino, francés, alemán, coreano y japonés
- Host, puerto, ruta o WebSocket URL completa configurables

## Requisitos

- Python 3.10+
- WebSocket Server de IINACT u OverlayPlugin activado
- Terminal con soporte Unicode

## Instalación

```bash
python -m venv ~/.local/share/ffxiv-tui
source ~/.local/share/ffxiv-tui/bin/activate
pip install -r requirements.txt
```

Para fish shell:

```fish
source ~/.local/share/ffxiv-tui/bin/activate.fish
```

## Uso rápido

Misma PC donde corre IINACT / OverlayPlugin:

```bash
./ffxiv-tui
```

Segunda PC/laptop por LAN:

```bash
./ffxiv-tui --host FFXIV_PC_IP
```

Reemplaza `FFXIV_PC_IP` por la IP LAN de la máquina donde corre FFXIV e IINACT / OverlayPlugin.

Modo alliance:

```bash
./ffxiv-tui --host FFXIV_PC_IP --alliance --bar-mode job --bar-width 10
```

Barras sin color:

```bash
./ffxiv-tui --host FFXIV_PC_IP --bar-mode plain
```

Mostrar HPS:

```bash
./ffxiv-tui --host FFXIV_PC_IP --show-hps
```

## Aviso de actualización

Aetherless Meter revisa GitHub Releases por default y muestra un aviso de alto contraste en el header cuando hay una versión nueva.

La TUI inicia primero; la búsqueda de actualizaciones corre en segundo plano después de una pequeña pausa.

Repo usado por default:

```text
pinksingularity/aetherless-meter
```

Desactivar búsqueda de actualizaciones:

```bash
./ffxiv-tui --no-check-updates
```

Usar otro repo para buscar actualizaciones:

```bash
./ffxiv-tui --update-repo owner/repo
```

Ajustar tiempos del update check:

```bash
./ffxiv-tui --update-delay 2 --update-timeout 1.5
```
## Modo de privacidad

Oculta nombres usando caracteres sombreados de terminal.

Ocultar a todos:

```bash
./ffxiv-tui --host FFXIV_PC_IP --privacy-mode all
```

Ocultarte solo a ti:

```bash
./ffxiv-tui --host FFXIV_PC_IP --privacy-mode self
```

Ocultar a todos excepto a ti:

```bash
./ffxiv-tui --host FFXIV_PC_IP --privacy-mode others
```

Estilos de máscara:

```bash
./ffxiv-tui --privacy-mode all --privacy-style light
./ffxiv-tui --privacy-mode all --privacy-style medium
./ffxiv-tui --privacy-mode all --privacy-style heavy
./ffxiv-tui --privacy-mode all --privacy-style mixed
```

Los estilos usan `░`, `▒`, `▓` o un patrón mixto.

## Opciones WebSocket

Usar WebSocket URL manual:

```bash
./ffxiv-tui --ws ws://FFXIV_PC_IP:10501/ws
```

Si tu IINACT usa `/` en vez de `/ws`:

```bash
./ffxiv-tui --host FFXIV_PC_IP --path /
```

## Idiomas

La salida de `--help` siempre se muestra en inglés. El idioma de la TUI sigue controlándose con `--lang` y por default usa `auto`.



El idioma predeterminado es `auto`. Intenta usar el locale del sistema y, si no reconoce uno compatible, cae a inglés.

```bash
./ffxiv-tui --lang auto
```

Opciones disponibles:

```bash
./ffxiv-tui --lang en
./ffxiv-tui --lang es
./ffxiv-tui --lang zh
./ffxiv-tui --lang fr
./ffxiv-tui --lang de
./ffxiv-tui --lang ko
./ffxiv-tui --lang ja
```

## Configuración de IINACT / OverlayPlugin

Para usarlo en la misma PC, el default normalmente basta:

```bash
./ffxiv-tui
```

Para usarlo desde otra laptop/PC:

1. Activa el WebSocket Server en IINACT / OverlayPlugin.
2. Pon el listen/bind IP en `0.0.0.0`.
3. Mantén el puerto `10501`, salvo que lo hayas cambiado.
4. Conecta desde la segunda máquina usando la IP LAN de la PC de FFXIV:

```bash
./ffxiv-tui --host FFXIV_PC_IP
```

No expongas este puerto a internet. Úsalo solo en tu red local.

## Versionado

Las releases públicas usan un esquema simple tipo `v1.0`, `v1.1`, `v1.2`.

- Las versiones mayores o hitos usan `.0`
- Las versiones pequeñas de features/fixes suben en `.1`

Versión actual: **v1.1**


## Nota sobre el desarrollo

Algunas partes de este proyecto se han creado con ayuda de la IA y han sido revisadas y probadas por el responsable del mantenimiento. La precisión de los idiomas distintos del español y el inglés puede variar.

El mantenedor es responsable del código, el comportamiento, el empaquetado y las versiones.

## Notas

Este proyecto no es oficial y no está afiliado a Square Enix, ACT, OverlayPlugin ni IINACT.

Utiliza las herramientas de terceros de forma responsable y privada. Recuerda que este tipo de herramientas están pensadas para ayudarte a mejorar, no para gritar a los demás. Si ese es el caso, por favor, abstente de utilizar esta herramienta.

## Preguntas frecuentes: ?

¿Por qué existe esto?

Es sencillo: tengo un monitor de baja resolución que no me permite usar superposiciones en el juego. Para evitar que todo se vea abarrotado, decidí desempolvar un PC de hace 16 años para mostrar las métricas, lo que me ahorra la molestia de una pantalla abarrotada y le da a esa vieja máquina una nueva vida.

## Probado en:


Arch Linux / Shell (TUI), i3 370M con 4 GB de RAM como segunda máquina para mostrar métricas

CachyOS / Kitty, Ghostty, Konsole, Alacritty, Ryzen 3 3200G, 20 GB de RAM

(No se ha probado la compatibilidad con Windows)
