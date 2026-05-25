#!/usr/bin/env python3
"""
Aetherless Meter

A minimal terminal combat meter for IINACT / OverlayPlugin WebSocket data.

Examples:
  ffxiv-tui
  ffxiv-tui --host FFXIV_PC_IP
  ffxiv-tui --host FFXIV_PC_IP --alliance --bar-mode job --bar-width 10
  ffxiv-tui --host FFXIV_PC_IP --lang es
  ffxiv-tui --ws ws://127.0.0.1:10501/ws --dump

Dependencies:
  pip install rich websockets
"""

from __future__ import annotations

import argparse
import asyncio
import json
import locale
import time
from dataclasses import dataclass, field
from typing import Any

try:
    from websockets.asyncio.client import connect
except ImportError:
    from websockets import connect  # type: ignore

from rich import box
from rich.align import Align
from rich.console import Console, Group
from rich.live import Live
from rich.panel import Panel
from rich.table import Table
from rich.text import Text


console = Console()

SUPPORTED_LANGS = ("auto", "en", "es", "zh", "fr", "de", "ko", "ja")

TRANSLATIONS: dict[str, dict[str, str]] = {
    "en": {
        "description": "A minimal FFXIV TUI meter for IINACT / OverlayPlugin WebSocket.",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "disconnected",
        "status_idle": "connected / no recent combat",
        "status_receiving": "receiving data",
        "rows": "Rows",
        "bars": "Bars",
        "encounter": "Encounter",
        "time": "Time",
        "raid_dps": "Raid DPS",
        "raid_hps": "Raid HPS",
        "damage": "Damage",
        "player_detected": "Detected player",
        "error": "Error",
        "waiting_combatdata": "Waiting for CombatData...",
        "footer": "Ctrl+C to quit • --alliance for 24 rows • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "Job",
        "col_name": "Name",
        "col_dps": "DPS",
        "col_dps_bar": "DPS Bar",
        "col_hps": "HPS",
        "col_hps_bar": "HPS Bar",
        "col_damage": "Damage",
        "col_percent": "%",
        "col_crit": "Crit%",
        "col_dh": "DH%",
        "col_max_hit": "Max Hit",
        "help_ws": "Full WebSocket URL. Overrides --host, --port and --path.",
        "help_host": "IINACT/OverlayPlugin host IP. Default: 127.0.0.1",
        "help_port": "IINACT/OverlayPlugin WebSocket port. Default: 10501",
        "help_path": "WebSocket path. Default: /ws",
        "help_lang": "UI language. Default: auto. auto uses your system locale.",
        "help_limit": "Maximum combatants to show.",
        "help_alliance": "Shortcut for --limit 24.",
        "help_refresh": "Terminal refreshes per second.",
        "help_show_lb": "Show Limit Break as a row.",
        "help_show_hps": "Show HPS and HPS bars.",
        "help_player_name": "Manual replacement for YOU, e.g. 'Character Name'.",
        "help_bar_mode": "Bar colors: job=role/job color, plain=no color.",
        "help_bar_width": "Width of DPS/HPS bars in characters.",
        "help_dump": "Print raw JSON messages for debugging.",
    },
    "es": {
        "description": "Medidor TUI minimalista para FFXIV usando WebSocket de IINACT / OverlayPlugin.",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "desconectado",
        "status_idle": "conectado / sin combate reciente",
        "status_receiving": "recibiendo datos",
        "rows": "Filas",
        "bars": "Barras",
        "encounter": "Encuentro",
        "time": "Tiempo",
        "raid_dps": "Raid DPS",
        "raid_hps": "Raid HPS",
        "damage": "Daño",
        "player_detected": "Jugador detectado",
        "error": "Error",
        "waiting_combatdata": "Esperando CombatData...",
        "footer": "Ctrl+C para salir • --alliance para 24 filas • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "Job",
        "col_name": "Nombre",
        "col_dps": "DPS",
        "col_dps_bar": "Barra DPS",
        "col_hps": "HPS",
        "col_hps_bar": "Barra HPS",
        "col_damage": "Daño",
        "col_percent": "%",
        "col_crit": "Crit%",
        "col_dh": "DH%",
        "col_max_hit": "Max Hit",
        "help_ws": "URL completa del WebSocket. Reemplaza --host, --port y --path.",
        "help_host": "IP del host IINACT/OverlayPlugin. Default: 127.0.0.1",
        "help_port": "Puerto WebSocket de IINACT/OverlayPlugin. Default: 10501",
        "help_path": "Ruta del WebSocket. Default: /ws",
        "help_lang": "Idioma de la interfaz. Default: auto. auto usa el idioma del sistema.",
        "help_limit": "Máximo de combatientes a mostrar.",
        "help_alliance": "Atajo para --limit 24.",
        "help_refresh": "Refrescos por segundo en terminal.",
        "help_show_lb": "Muestra Limit Break como fila.",
        "help_show_hps": "Muestra HPS y barras de HPS.",
        "help_player_name": "Reemplazo manual para YOU, ej. 'Nombre Personaje'.",
        "help_bar_mode": "Colores de barra: job=rol/job, plain=sin color.",
        "help_bar_width": "Ancho de barras DPS/HPS en caracteres.",
        "help_dump": "Imprime mensajes JSON crudos para depurar.",
    },
    "zh": {
        "description": "用于 IINACT / OverlayPlugin WebSocket 的极简 FFXIV 终端战斗统计器。",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "未连接",
        "status_idle": "已连接 / 最近无战斗",
        "status_receiving": "正在接收数据",
        "rows": "行数",
        "bars": "条形",
        "encounter": "战斗",
        "time": "时间",
        "raid_dps": "团队 DPS",
        "raid_hps": "团队 HPS",
        "damage": "伤害",
        "player_detected": "检测到玩家",
        "error": "错误",
        "waiting_combatdata": "等待 CombatData...",
        "footer": "Ctrl+C 退出 • --alliance 显示 24 行 • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "职业",
        "col_name": "名称",
        "col_dps": "DPS",
        "col_dps_bar": "DPS 条",
        "col_hps": "HPS",
        "col_hps_bar": "HPS 条",
        "col_damage": "伤害",
        "col_percent": "%",
        "col_crit": "暴击%",
        "col_dh": "直击%",
        "col_max_hit": "最大命中",
        "help_ws": "完整 WebSocket URL。会覆盖 --host、--port 和 --path。",
        "help_host": "IINACT/OverlayPlugin 主机 IP。默认: 127.0.0.1",
        "help_port": "IINACT/OverlayPlugin WebSocket 端口。默认: 10501",
        "help_path": "WebSocket 路径。默认: /ws",
        "help_lang": "界面语言。默认: auto。auto 使用系统语言。",
        "help_limit": "显示的最大战斗成员数量。",
        "help_alliance": "--limit 24 的快捷方式。",
        "help_refresh": "终端每秒刷新次数。",
        "help_show_lb": "将 Limit Break 显示为一行。",
        "help_show_hps": "显示 HPS 和 HPS 条。",
        "help_player_name": "手动替换 YOU，例如 '角色名'。",
        "help_bar_mode": "条形颜色: job=职业/职责, plain=无色。",
        "help_bar_width": "DPS/HPS 条宽度，单位为字符。",
        "help_dump": "打印原始 JSON 消息用于调试。",
    },
    "fr": {
        "description": "Compteur TUI minimal pour FFXIV via WebSocket IINACT / OverlayPlugin.",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "déconnecté",
        "status_idle": "connecté / pas de combat récent",
        "status_receiving": "réception des données",
        "rows": "Lignes",
        "bars": "Barres",
        "encounter": "Combat",
        "time": "Temps",
        "raid_dps": "DPS raid",
        "raid_hps": "HPS raid",
        "damage": "Dégâts",
        "player_detected": "Joueur détecté",
        "error": "Erreur",
        "waiting_combatdata": "En attente de CombatData...",
        "footer": "Ctrl+C pour quitter • --alliance pour 24 lignes • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "Job",
        "col_name": "Nom",
        "col_dps": "DPS",
        "col_dps_bar": "Barre DPS",
        "col_hps": "HPS",
        "col_hps_bar": "Barre HPS",
        "col_damage": "Dégâts",
        "col_percent": "%",
        "col_crit": "Crit%",
        "col_dh": "DH%",
        "col_max_hit": "Max Hit",
        "help_ws": "URL WebSocket complète. Remplace --host, --port et --path.",
        "help_host": "IP de l'hôte IINACT/OverlayPlugin. Défaut: 127.0.0.1",
        "help_port": "Port WebSocket IINACT/OverlayPlugin. Défaut: 10501",
        "help_path": "Chemin WebSocket. Défaut: /ws",
        "help_lang": "Langue de l'interface. Défaut: auto. auto utilise la locale système.",
        "help_limit": "Nombre maximal de combattants à afficher.",
        "help_alliance": "Raccourci pour --limit 24.",
        "help_refresh": "Rafraîchissements du terminal par seconde.",
        "help_show_lb": "Affiche le Limit Break comme une ligne.",
        "help_show_hps": "Affiche HPS et les barres HPS.",
        "help_player_name": "Remplacement manuel de YOU, ex. 'Nom Personnage'.",
        "help_bar_mode": "Couleurs: job=rôle/job, plain=sans couleur.",
        "help_bar_width": "Largeur des barres DPS/HPS en caractères.",
        "help_dump": "Affiche les messages JSON bruts pour le debug.",
    },
    "de": {
        "description": "Minimaler FFXIV-TUI-Meter für IINACT / OverlayPlugin WebSocket.",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "getrennt",
        "status_idle": "verbunden / kein aktueller Kampf",
        "status_receiving": "empfange Daten",
        "rows": "Zeilen",
        "bars": "Balken",
        "encounter": "Kampf",
        "time": "Zeit",
        "raid_dps": "Raid-DPS",
        "raid_hps": "Raid-HPS",
        "damage": "Schaden",
        "player_detected": "Erkannter Spieler",
        "error": "Fehler",
        "waiting_combatdata": "Warte auf CombatData...",
        "footer": "Ctrl+C zum Beenden • --alliance für 24 Zeilen • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "Job",
        "col_name": "Name",
        "col_dps": "DPS",
        "col_dps_bar": "DPS-Balken",
        "col_hps": "HPS",
        "col_hps_bar": "HPS-Balken",
        "col_damage": "Schaden",
        "col_percent": "%",
        "col_crit": "Crit%",
        "col_dh": "DH%",
        "col_max_hit": "Max Hit",
        "help_ws": "Vollständige WebSocket-URL. Überschreibt --host, --port und --path.",
        "help_host": "IINACT/OverlayPlugin Host-IP. Standard: 127.0.0.1",
        "help_port": "IINACT/OverlayPlugin WebSocket-Port. Standard: 10501",
        "help_path": "WebSocket-Pfad. Standard: /ws",
        "help_lang": "Sprache der Oberfläche. Standard: auto. auto nutzt die System-Locale.",
        "help_limit": "Maximale Anzahl angezeigter Kämpfer.",
        "help_alliance": "Kurzform für --limit 24.",
        "help_refresh": "Terminal-Aktualisierungen pro Sekunde.",
        "help_show_lb": "Zeigt Limit Break als Zeile.",
        "help_show_hps": "Zeigt HPS und HPS-Balken.",
        "help_player_name": "Manueller Ersatz für YOU, z. B. 'Charakter Name'.",
        "help_bar_mode": "Balkenfarben: job=Rolle/Job, plain=keine Farbe.",
        "help_bar_width": "Breite der DPS/HPS-Balken in Zeichen.",
        "help_dump": "Gibt rohe JSON-Nachrichten zum Debuggen aus.",
    },
    "ko": {
        "description": "IINACT / OverlayPlugin WebSocket용 미니멀 FFXIV 터미널 전투 미터.",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "연결 끊김",
        "status_idle": "연결됨 / 최근 전투 없음",
        "status_receiving": "데이터 수신 중",
        "rows": "행",
        "bars": "바",
        "encounter": "전투",
        "time": "시간",
        "raid_dps": "레이드 DPS",
        "raid_hps": "레이드 HPS",
        "damage": "피해량",
        "player_detected": "감지된 플레이어",
        "error": "오류",
        "waiting_combatdata": "CombatData 대기 중...",
        "footer": "Ctrl+C 종료 • --alliance 24행 • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "직업",
        "col_name": "이름",
        "col_dps": "DPS",
        "col_dps_bar": "DPS 바",
        "col_hps": "HPS",
        "col_hps_bar": "HPS 바",
        "col_damage": "피해량",
        "col_percent": "%",
        "col_crit": "극대%",
        "col_dh": "직격%",
        "col_max_hit": "최대타",
        "help_ws": "전체 WebSocket URL. --host, --port, --path를 덮어씁니다.",
        "help_host": "IINACT/OverlayPlugin 호스트 IP. 기본값: 127.0.0.1",
        "help_port": "IINACT/OverlayPlugin WebSocket 포트. 기본값: 10501",
        "help_path": "WebSocket 경로. 기본값: /ws",
        "help_lang": "UI 언어. 기본값: auto. auto는 시스템 로케일을 사용합니다.",
        "help_limit": "표시할 최대 전투원 수.",
        "help_alliance": "--limit 24의 단축 옵션.",
        "help_refresh": "초당 터미널 새로고침 횟수.",
        "help_show_lb": "Limit Break를 행으로 표시합니다.",
        "help_show_hps": "HPS와 HPS 바를 표시합니다.",
        "help_player_name": "YOU를 수동으로 바꿉니다. 예: '캐릭터 이름'.",
        "help_bar_mode": "바 색상: job=역할/직업, plain=색 없음.",
        "help_bar_width": "DPS/HPS 바 너비(문자 수).",
        "help_dump": "디버그용 원시 JSON 메시지를 출력합니다.",
    },
    "ja": {
        "description": "IINACT / OverlayPlugin WebSocket 用のミニマルな FFXIV ターミナルメーター。",
        "app_title": "Aetherless Meter",
        "byline": "A minimal FFXIV TUI meter by Pink Singularity / Nixie",
        "status_disconnected": "未接続",
        "status_idle": "接続済み / 最近の戦闘なし",
        "status_receiving": "データ受信中",
        "rows": "行",
        "bars": "バー",
        "encounter": "戦闘",
        "time": "時間",
        "raid_dps": "レイドDPS",
        "raid_hps": "レイドHPS",
        "damage": "ダメージ",
        "player_detected": "検出プレイヤー",
        "error": "エラー",
        "waiting_combatdata": "CombatData 待機中...",
        "footer": "Ctrl+C で終了 • --alliance で24行 • --bar-mode job/plain",
        "col_rank": "#",
        "col_job": "ジョブ",
        "col_name": "名前",
        "col_dps": "DPS",
        "col_dps_bar": "DPSバー",
        "col_hps": "HPS",
        "col_hps_bar": "HPSバー",
        "col_damage": "ダメージ",
        "col_percent": "%",
        "col_crit": "クリ%",
        "col_dh": "DH%",
        "col_max_hit": "最大ヒット",
        "help_ws": "完全な WebSocket URL。--host、--port、--path を上書きします。",
        "help_host": "IINACT/OverlayPlugin のホストIP。既定: 127.0.0.1",
        "help_port": "IINACT/OverlayPlugin WebSocket ポート。既定: 10501",
        "help_path": "WebSocket パス。既定: /ws",
        "help_lang": "UI言語。既定: auto。auto はシステムロケールを使用します。",
        "help_limit": "表示する戦闘メンバーの最大数。",
        "help_alliance": "--limit 24 のショートカット。",
        "help_refresh": "ターミナルの1秒あたり更新回数。",
        "help_show_lb": "Limit Break を行として表示します。",
        "help_show_hps": "HPS と HPSバーを表示します。",
        "help_player_name": "YOU の手動置換。例: 'キャラクター名'",
        "help_bar_mode": "バー色: job=ロール/ジョブ, plain=色なし。",
        "help_bar_width": "DPS/HPSバーの幅（文字数）。",
        "help_dump": "デバッグ用に生JSONメッセージを出力します。",
    },
}


def resolve_lang(lang: str) -> str:
    if lang != "auto":
        return lang if lang in TRANSLATIONS else "en"

    loc = (locale.getlocale()[0] or locale.getdefaultlocale()[0] or "").lower()
    if loc.startswith("es"):
        return "es"
    if loc.startswith("zh"):
        return "zh"
    if loc.startswith("fr"):
        return "fr"
    if loc.startswith("de"):
        return "de"
    if loc.startswith("ko"):
        return "ko"
    if loc.startswith("ja"):
        return "ja"
    return "en"


def tr(lang: str, key: str) -> str:
    return TRANSLATIONS.get(lang, TRANSLATIONS["en"]).get(key, TRANSLATIONS["en"].get(key, key))


@dataclass
class MeterState:
    connected: bool = False
    last_error: str = ""
    last_update: float = 0.0
    encounter: dict[str, Any] = field(default_factory=dict)
    combatants: dict[str, dict[str, Any]] = field(default_factory=dict)
    last_raw: dict[str, Any] = field(default_factory=dict)
    primary_player: str = ""



JOB_STYLES = {
    "Pld": "bright_blue",
    "War": "bright_blue",
    "Drk": "bright_blue",
    "Gnb": "bright_blue",
    "Whm": "bright_green",
    "Sch": "bright_green",
    "Ast": "bright_green",
    "Sge": "bright_green",
    "Mnk": "bright_red",
    "Drg": "bright_red",
    "Nin": "bright_red",
    "Sam": "bright_red",
    "Rpr": "bright_red",
    "Vpr": "bright_red",
    "Brd": "bright_cyan",
    "Mch": "bright_cyan",
    "Dnc": "bright_cyan",
    "Blm": "bright_magenta",
    "Smn": "bright_magenta",
    "Rdm": "bright_magenta",
    "Pct": "bright_magenta",
    "Blu": "bright_magenta",
}


def pick(data: dict[str, Any], *keys: str, default: str = "-") -> str:
    for key in keys:
        value = data.get(key)
        if value is not None and value != "":
            return str(value)
    return default


def to_float(value: Any) -> float:
    if value is None:
        return 0.0
    if isinstance(value, (int, float)):
        return float(value)
    cleaned = str(value).strip().replace(",", "").replace("%", "")
    if cleaned in {"", "-", "---", "NaN", "Infinity"}:
        return 0.0
    try:
        return float(cleaned)
    except ValueError:
        return 0.0


def fmt_intish(value: Any) -> str:
    number = to_float(value)
    if number <= 0:
        raw = str(value) if value is not None else "-"
        return raw if raw not in {"", "None"} else "-"
    return f"{number:,.0f}"


def get_dps(data: dict[str, Any]) -> float:
    return to_float(data.get("ENCDPS") or data.get("encdps") or data.get("DPS") or data.get("dps"))


def get_hps(data: dict[str, Any]) -> float:
    return to_float(
        data.get("ENCHPS")
        or data.get("enchps")
        or data.get("HPS")
        or data.get("hps")
        or data.get("healedps")
        or data.get("healps")
    )


def job_style(job: str) -> str:
    return JOB_STYLES.get(job.strip().title(), "bright_white")




def make_bar(value: float, max_value: float, width: int = 18, *, mode: str = "job", job: str = "") -> Text:
    """Small unicode bar. Modes: job, plain."""
    ratio = 0.0 if max_value <= 0 else max(0.0, min(value / max_value, 1.0))
    filled = round(ratio * width)
    empty = width - filled

    bar = Text()
    if mode == "plain":
        bar.append("█" * filled, style="bold")
    else:
        bar.append("█" * filled, style=job_style(job))

    bar.append("░" * empty, style="dim")
    return bar


def display_name(raw_name: str, data: dict[str, Any], state: MeterState, manual_player_name: str = "") -> str:
    if raw_name.upper() == "YOU":
        detected = manual_player_name or state.primary_player or pick(
            data, "CurrentPlayer", "currentPlayer", "Owner", "owner", default=""
        )
        if detected:
            return detected
    return raw_name


def normalize_combat_data(message: dict[str, Any]) -> tuple[dict[str, Any], dict[str, dict[str, Any]]]:
    encounter = message.get("Encounter")
    if not isinstance(encounter, dict):
        encounter = message

    combatants = message.get("Combatant") or message.get("combatants") or {}
    if not isinstance(combatants, dict):
        combatants = {}

    normalized: dict[str, dict[str, Any]] = {}
    for fallback_name, raw in combatants.items():
        if not isinstance(raw, dict):
            continue
        name = pick(raw, "name", "Name", default=str(fallback_name))
        normalized[name] = raw

    return encounter, normalized


def update_from_message(state: MeterState, message: dict[str, Any]) -> None:
    msg_type = message.get("type")

    if msg_type == "ChangePrimaryPlayer":
        player = pick(
            message,
            "charName",
            "char_name",
            "playerName",
            "player_name",
            "name",
            "Name",
            default="",
        )
        if player:
            state.primary_player = player
        return

    if msg_type != "CombatData":
        return

    encounter, combatants = normalize_combat_data(message)
    state.encounter = encounter
    state.combatants = combatants
    state.last_raw = message
    state.last_update = time.time()


def build_header(state: MeterState, ws_url: str, show_hps: bool, limit: int, bar_mode: str, lang: str) -> Panel:
    encounter = state.encounter
    title = pick(encounter, "title", "Title", "Encounter", default="...")
    duration = pick(encounter, "duration", "DURATION", "Duration", default="00:00")
    rdps = pick(encounter, "ENCDPS", "encdps", "dps", "DPS", default="-")
    rhps = pick(encounter, "ENCHPS", "enchps", "hps", "HPS", default="-")
    damage = pick(encounter, "damage", "Damage", default="-")

    age = time.time() - state.last_update if state.last_update else 9999
    if not state.connected:
        status = tr(lang, "status_disconnected")
    elif age > 5:
        status = tr(lang, "status_idle")
    else:
        status = tr(lang, "status_receiving")

    header = Text()
    header.append(tr(lang, "app_title"), style="bold")
    header.append(" — ")
    header.append(tr(lang, "byline"), style="dim")
    header.append(f"\n{status}  •  {tr(lang, 'rows')}: {limit}  •  {tr(lang, 'bars')}: {bar_mode}\n")
    header.append(
        f"{tr(lang, 'encounter')}: {title}  •  {tr(lang, 'time')}: {duration}  •  {tr(lang, 'raid_dps')}: {rdps}"
    )
    if show_hps:
        header.append(f"  •  {tr(lang, 'raid_hps')}: {rhps}")
    header.append(f"  •  {tr(lang, 'damage')}: {damage}\n")
    if state.primary_player:
        header.append(f"{tr(lang, 'player_detected')}: {state.primary_player}\n", style="dim")
    header.append(f"WS: {ws_url}", style="dim")
    if state.last_error:
        header.append(f"\n{tr(lang, 'error')}: {state.last_error}", style="bold")

    return Panel(header, box=box.ROUNDED)


def build_table(
    state: MeterState,
    limit: int,
    show_limit_break: bool,
    show_hps: bool,
    player_name: str,
    bar_mode: str,
    bar_width: int,
    lang: str,
) -> Table:
    table = Table(box=box.SIMPLE_HEAVY, expand=True)
    table.add_column(tr(lang, "col_rank"), justify="right", width=3)
    table.add_column(tr(lang, "col_job"), justify="center", width=5)
    table.add_column(tr(lang, "col_name"), no_wrap=True, ratio=2)
    table.add_column(tr(lang, "col_dps"), justify="right")
    table.add_column(tr(lang, "col_dps_bar"), ratio=2)
    if show_hps:
        table.add_column(tr(lang, "col_hps"), justify="right")
        table.add_column(tr(lang, "col_hps_bar"), ratio=2)
    table.add_column(tr(lang, "col_damage"), justify="right")
    table.add_column(tr(lang, "col_percent"), justify="right")
    table.add_column(tr(lang, "col_crit"), justify="right")
    table.add_column(tr(lang, "col_dh"), justify="right")
    table.add_column(tr(lang, "col_max_hit"), ratio=2)

    rows = []
    for name, data in state.combatants.items():
        if not show_limit_break and name.lower() in {"limit break", "lb"}:
            continue
        dps_value = get_dps(data)
        hps_value = get_hps(data)
        rows.append((dps_value, hps_value, name, data))

    rows.sort(key=lambda item: item[0], reverse=True)

    if not rows:
        empty_row = ["-", "-", tr(lang, "waiting_combatdata"), "-", "-"]
        if show_hps:
            empty_row.extend(["-", "-"])
        empty_row.extend(["-", "-", "-", "-", "-"])
        table.add_row(*empty_row)
        return table

    max_dps = max((row[0] for row in rows), default=0.0)
    max_hps = max((row[1] for row in rows), default=0.0)

    for idx, (dps_value, hps_value, raw_name, data) in enumerate(rows[:limit], start=1):
        job = pick(data, "Job", "job", "JOB", default="-")
        name = display_name(raw_name, data, state, player_name)
        dps = f"{dps_value:,.1f}" if dps_value else pick(data, "ENCDPS", "encdps", "DPS", default="-")
        hps = f"{hps_value:,.1f}" if hps_value else pick(data, "ENCHPS", "enchps", "HPS", default="-")
        damage = fmt_intish(data.get("damage") or data.get("Damage"))
        damage_pct = pick(data, "damage%", "damagePct", "Damage%", default="-")
        crit_pct = pick(data, "crithit%", "crit%", "CritHitPct", default="-")
        dh_pct = pick(data, "DirectHitPct", "directhit%", "DirectHit%", "dh%", default="-")
        max_hit = pick(data, "maxhit", "MaxHit", "max_hit", default="-")

        row: list[Any] = [
            str(idx),
            Text(job, style=job_style(job)),
            name,
            dps,
            make_bar(dps_value, max_dps, width=bar_width, mode=bar_mode, job=job),
        ]

        if show_hps:
            row.extend([hps, make_bar(hps_value, max_hps, width=bar_width, mode=bar_mode, job=job)])

        row.extend([damage, damage_pct, crit_pct, dh_pct, max_hit])
        table.add_row(*row)

    return table


def build_screen(
    state: MeterState,
    ws_url: str,
    limit: int,
    show_limit_break: bool,
    show_hps: bool,
    player_name: str,
    bar_mode: str,
    bar_width: int,
    lang: str,
):
    return Group(
        build_header(state, ws_url, show_hps, limit, bar_mode, lang),
        build_table(state, limit, show_limit_break, show_hps, player_name, bar_mode, bar_width, lang),
        Align.left(Text(tr(lang, "footer"), style="dim")),
    )


async def websocket_loop(state: MeterState, ws_url: str, dump: bool) -> None:
    events = ["CombatData", "ChangePrimaryPlayer"]

    while True:
        try:
            state.connected = False
            state.last_error = ""

            async with connect(ws_url, ping_interval=None) as websocket:
                state.connected = True
                await websocket.send(json.dumps({"call": "subscribe", "events": events}))

                async for raw in websocket:
                    try:
                        message = json.loads(raw)
                    except json.JSONDecodeError:
                        state.last_error = f"Non-JSON message: {raw[:80]}"
                        continue

                    if dump:
                        console.log(message)

                    update_from_message(state, message)

        except asyncio.CancelledError:
            raise
        except Exception as exc:
            state.connected = False
            state.last_error = f"{type(exc).__name__}: {exc}"
            await asyncio.sleep(2)


async def ui_loop(
    state: MeterState,
    ws_url: str,
    limit: int,
    show_limit_break: bool,
    show_hps: bool,
    player_name: str,
    bar_mode: str,
    bar_width: int,
    refresh_per_second: int,
    lang: str,
) -> None:
    with Live(
        build_screen(state, ws_url, limit, show_limit_break, show_hps, player_name, bar_mode, bar_width, lang),
        console=console,
        refresh_per_second=refresh_per_second,
        screen=True,
    ) as live:
        while True:
            live.update(build_screen(state, ws_url, limit, show_limit_break, show_hps, player_name, bar_mode, bar_width, lang))
            await asyncio.sleep(1 / max(refresh_per_second, 1))


def build_ws_url(args: argparse.Namespace) -> str:
    if args.ws:
        return args.ws

    path = args.path
    if not path.startswith("/"):
        path = "/" + path

    return f"ws://{args.host}:{args.port}{path}"


def parse_args() -> argparse.Namespace:
    lang_parser = argparse.ArgumentParser(add_help=False)
    lang_parser.add_argument("--lang", choices=SUPPORTED_LANGS, default="auto")
    known, _ = lang_parser.parse_known_args()
    lang = resolve_lang(known.lang)

    examples = """Examples:
  ffxiv-tui
  ffxiv-tui --host FFXIV_PC_IP
  ffxiv-tui --host FFXIV_PC_IP --alliance --bar-mode job --bar-width 10
  ffxiv-tui --host FFXIV_PC_IP --lang es
  ffxiv-tui --ws ws://127.0.0.1:10501/ws --dump
"""

    parser = argparse.ArgumentParser(
        description=tr(lang, "description"),
        parents=[lang_parser],
        epilog=examples,
        formatter_class=argparse.RawDescriptionHelpFormatter,
    )
    parser.add_argument("--ws", default="", help=tr(lang, "help_ws"))
    parser.add_argument("--host", default="127.0.0.1", help=tr(lang, "help_host"))
    parser.add_argument("--port", type=int, default=10501, help=tr(lang, "help_port"))
    parser.add_argument("--path", default="/ws", help=tr(lang, "help_path"))
    parser.add_argument("--limit", type=int, default=8, help=tr(lang, "help_limit"))
    parser.add_argument("--alliance", action="store_true", help=tr(lang, "help_alliance"))
    parser.add_argument("--refresh", type=int, default=4, help=tr(lang, "help_refresh"))
    parser.add_argument("--show-limit-break", action="store_true", help=tr(lang, "help_show_lb"))
    parser.add_argument("--show-hps", action="store_true", help=tr(lang, "help_show_hps"))
    parser.add_argument("--player-name", default="", help=tr(lang, "help_player_name"))
    parser.add_argument(
        "--bar-mode",
        choices=["job", "plain"],
        default="job",
        help=tr(lang, "help_bar_mode"),
    )
    parser.add_argument("--bar-width", type=int, default=18, help=tr(lang, "help_bar_width"))
    parser.add_argument("--dump", action="store_true", help=tr(lang, "help_dump"))
    args = parser.parse_args()
    args.lang = resolve_lang(args.lang)
    return args


async def main() -> None:
    args = parse_args()

    state = MeterState()
    limit = 24 if args.alliance else args.limit
    ws_url = build_ws_url(args)

    reader = asyncio.create_task(websocket_loop(state, ws_url, args.dump))
    ui = asyncio.create_task(
        ui_loop(
            state,
            ws_url,
            limit,
            args.show_limit_break,
            args.show_hps,
            args.player_name,
            args.bar_mode,
            args.bar_width,
            args.refresh,
            args.lang,
        )
    )

    try:
        await asyncio.gather(reader, ui)
    except KeyboardInterrupt:
        reader.cancel()
        ui.cancel()


if __name__ == "__main__":
    asyncio.run(main())
