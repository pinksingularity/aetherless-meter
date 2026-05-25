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
- Reconexión automática
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

Usar WebSocket URL manual:

```bash
./ffxiv-tui --ws ws://FFXIV_PC_IP:10501/ws
```

Si tu IINACT usa `/` en vez de `/ws`:

```bash
./ffxiv-tui --host FFXIV_PC_IP --path /
```

## Idiomas

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

## Nota de desarrollo

Partes de este proyecto fueron creadas con asistencia de AI y revisadas/probadas por la mantenedora.

La mantenedora es responsable del código, comportamiento, empaquetado y releases.

## Notas

Este proyecto no es oficial y no está afiliado con Square Enix, ACT, OverlayPlugin ni IINACT.

Usa herramientas de terceros con responsabilidad y en privado.
