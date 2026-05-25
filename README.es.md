# Aetherless Meter

**A minimal FFXIV TUI meter by Pink Singularity / Nixie.**

Un medidor de combate para terminal usando datos del **WebSocket de IINACT / OverlayPlugin**.

Aetherless Meter es Linux-first y está diseñado para setups de terminal, equipos ligeros, segunda pantalla o entornos mínimos.

El ejecutable/script sigue siendo `ffxiv-tui` por claridad, mientras que el lanzador instalado es `aetherless-meter`.

## Funciones

- Interfaz TUI con Rich
- Tabla y barras de DPS
- Columnas opcionales de HPS
- Modo alliance de 24 jugadores
- Barras por job o plain
- Relative Score local opcional
- Modo de privacidad para ocultar nombres, URL WebSocket y errores de conexión con caracteres sombreados de terminal
- Avisos automáticos de actualización desde GitHub Releases
- Reconexión automática
- Cierre limpio con Ctrl+C
- Salida de `--help` siempre en inglés
- UI multi-idioma: inglés, español, chino, francés, alemán, coreano y japonés
- Host, puerto, ruta o WebSocket URL completa configurables
- Instalador con entorno virtual privado y lanzador global

## Soporte de plataformas

| Plataforma | Estado | Notas |
|---|---|---|
| Linux | Probado / objetivo principal | Plataforma recomendada. Diseñado alrededor de setups ligeros y terminal-first. |
| Windows | Experimental / no probado | La app en Python podría funcionar con PowerShell o Windows Terminal, pero el instalador actual está orientado a Linux. |
| macOS | No probado | Podría funcionar en teoría por estar basado en Python, pero no se ha probado un setup FFXIV/macOS. |

Aetherless Meter está pensado principalmente para personas que quieren un medidor en terminal, setup de segunda pantalla o alternativa ligera y amigable para Linux.

## Requisitos

- Python 3.10+
- WebSocket Server de IINACT u OverlayPlugin activado
- Terminal con soporte Unicode

## Instalación

Instalación recomendada:

```bash
python ffxiv_tui.py --install
```

Esto crea un entorno virtual privado en:

```text
~/.local/share/aetherless-meter/venv
```

e instala un lanzador global en:

```text
~/.local/bin/aetherless-meter
```

El instalador intenta detectar `bash`, `zsh` o `fish` y agrega `~/.local/bin` a la configuración del shell correspondiente automáticamente.

Después de instalar, puedes ejecutarlo inmediatamente con la ruta completa del lanzador:

```bash
~/.local/bin/aetherless-meter
```

Después de reiniciar la terminal o recargar la configuración del shell, ejecútalo desde cualquier lugar con:

```bash
aetherless-meter
```

Omitir cambios en PATH/configuración del shell:

```bash
python ffxiv_tui.py --install --no-modify-path
```

Desinstalar:

```bash
aetherless-meter --uninstall
```

Instalación manual para desarrollo:

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
aetherless-meter
```

Segunda PC/laptop por LAN:

```bash
aetherless-meter --host FFXIV_PC_IP
```

Reemplaza `FFXIV_PC_IP` por la IP LAN de la máquina donde corre FFXIV e IINACT / OverlayPlugin.

Modo alliance:

```bash
aetherless-meter --host FFXIV_PC_IP --alliance --bar-mode job --bar-width 10
```

Barras sin color:

```bash
aetherless-meter --host FFXIV_PC_IP --bar-mode plain
```

Mostrar HPS:

```bash
aetherless-meter --host FFXIV_PC_IP --show-hps
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
aetherless-meter --no-check-updates
```

Usar otro repo para buscar actualizaciones:

```bash
aetherless-meter --update-repo owner/repo
```

Ajustar tiempos del update check:

```bash
aetherless-meter --update-delay 2 --update-timeout 1.5
```

## Modo de privacidad

Oculta nombres e información potencialmente sensible usando caracteres sombreados de terminal.

Ocultar a todos:

```bash
aetherless-meter --privacy-mode all
```

Ocultarte solo a ti:

```bash
aetherless-meter --privacy-mode self
```

Ocultar a todos excepto a ti:

```bash
aetherless-meter --privacy-mode others
```

Estilos de máscara:

```bash
aetherless-meter --privacy-mode all --privacy-style light
aetherless-meter --privacy-mode all --privacy-style medium
aetherless-meter --privacy-mode all --privacy-style heavy
aetherless-meter --privacy-mode all --privacy-style mixed
```

Los estilos usan `░`, `▒`, `▓` o un patrón mixto.

El modo de privacidad puede ocultar:

- nombres de jugadores
- nombre detectado en el header
- URL WebSocket
- texto de errores de conexión

## Relative Score

Aetherless Meter incluye un modo opcional de **Relative Score** local.

Esto **no** es un parse de FFLogs. No usa datos de FFLogs, rankings históricos, partitions de bosses ni estadísticas globales por job.

Solo compara el DPS visible/actual dentro de Aetherless Meter.

Ejemplos:

```bash
aetherless-meter --score-mode overall
aetherless-meter --score-mode role
aetherless-meter --score-mode job
```

Modos:

- `off`: desactivado
- `overall`: compara DPS contra todos los combatientes visibles
- `role`: compara DPS contra el mismo grupo de rol
- `job`: compara DPS contra el mismo job, útil solo si hay duplicados

Los grupos con menos de dos miembros válidos muestran `-` para evitar un 100 solitario y engañoso.

Relative Score usa colores de terminal inspirados en FFLogs como ayuda visual, pero sigue sin ser un parse de FFLogs.

## Nota sobre FFLogs / Archon

Aetherless Meter **no** sube logs a FFLogs y **no** reemplaza FFLogs Uploader ni Archon.

Lee datos live del WebSocket para mostrarlos en una terminal ligera. Para logs oficiales, parses, rankings y análisis post-pull, usa las herramientas de FFLogs / Archon.

## Opciones WebSocket

Usar WebSocket URL manual:

```bash
aetherless-meter --ws ws://FFXIV_PC_IP:10501/ws
```

Si tu IINACT usa `/` en vez de `/ws`:

```bash
aetherless-meter --host FFXIV_PC_IP --path /
```

## Idiomas

La salida de `--help` siempre se muestra en inglés. El idioma de la TUI sigue controlándose con `--lang` y por default usa `auto`.

El idioma predeterminado es `auto`. Intenta usar el locale del sistema y, si no reconoce uno compatible, cae a inglés.

```bash
aetherless-meter --lang auto
```

Opciones disponibles:

```bash
aetherless-meter --lang en
aetherless-meter --lang es
aetherless-meter --lang zh
aetherless-meter --lang fr
aetherless-meter --lang de
aetherless-meter --lang ko
aetherless-meter --lang ja
```

## Uso de recursos

Aetherless Meter está diseñado para ser ligero. No guarda historial largo de combate; solo mantiene el estado actual del encuentro en memoria.

En pruebas actuales con Linux/Konsole, usó alrededor de **40 MiB RSS**, aunque esto puede variar según Python, terminal, librerías del sistema y setup.

Para reducir uso de CPU, baja el refresh rate:

```bash
aetherless-meter --refresh 2
```

## Versionado

Las releases públicas usan un esquema simple tipo `v1.0`, `v1.1`, `v1.2`.

- Las versiones mayores o hitos usan `.0`
- Las versiones pequeñas de features/fixes suben en `.1`

Versión actual: **v2.0**


## Nota sobre el desarrollo

Algunas partes de este proyecto se han creado con ayuda de la IA y han sido revisadas y probadas por el responsable del mantenimiento. La precisión de los idiomas distintos del español y el inglés puede variar.

El mantenedor es responsable del código, el comportamiento, el empaquetado y las versiones.

## Notas

v2.0 consolida los cambios hechos después de v1.2 y documenta el proyecto como un medidor terminal Linux-first más completo.

Este proyecto no es oficial y no está afiliado con Square Enix, ACT, OverlayPlugin, IINACT, FFLogs ni Archon.

Usa herramientas de terceros con responsabilidad y en privado. Recuerda que este tipo de herramientas están pensadas para ayudarte a mejorar, no para gritar a los demás. Si ese es el caso, por favor, abstente de utilizar esta herramienta.

## Preguntas frecuentes: ?

¿Por qué existe esto?

Es sencillo: tengo un monitor de baja resolución que no me permite usar superposiciones en el juego. Para evitar que todo se vea abarrotado, decidí desempolvar un PC de hace 16 años para mostrar las métricas, lo que me ahorra la molestia de una pantalla abarrotada y le da a esa vieja máquina una nueva vida.

## Probado en:


Arch Linux / Shell (TUI), i3 370M con 4 GB de RAM como segunda máquina para mostrar métricas

CachyOS / Kitty, Ghostty, Konsole, Alacritty, Ryzen 3 3200G, 20 GB de RAM

(No se ha probado la compatibilidad con Windows)

