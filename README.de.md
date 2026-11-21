<h1 align="center">FLAtlas Server</h1>

<p align="center">
  <strong>Ein Dedicated Server für Freelancer mit Web-Verwaltung im Stil von FL Atlas.</strong>
</p>

<p align="center">
  <strong>Deutsch</strong>
  |
  <a href="README.md">English</a>
</p>

<p align="center">
  <img alt="Version 0.2.0" src="https://img.shields.io/badge/Version-0.2.0-00d5ff?style=for-the-badge">
  <img alt="Windows" src="https://img.shields.io/badge/Windows-Dienst%20%2B%20Konsole-0078D4?style=for-the-badge&logo=windows&logoColor=white">
  <img alt="Freelancer" src="https://img.shields.io/badge/Freelancer-Multiplayer-00A3FF?style=for-the-badge">
  <img alt="Web-Verwaltung" src="https://img.shields.io/badge/Web-Verwaltung-7B61FF?style=for-the-badge">
  <a href="https://github.com/flathack/FLAtlas-FL-Server/wiki"><img alt="Hilfe im Wiki" src="https://img.shields.io/badge/Hilfe-Wiki-2EA44F?style=for-the-badge"></a>
</p>

> The built program folder contains the original game's own files, for the private use of the person who owns the game, on their own computer. It must not be shared or published. This repository leaves those files out.

## Download

Releases erscheinen auf der GitHub-Releaseseite als `FLAtlas-Server-v<version>-win-x86.zip`. Das Paket enthält
nur die Programmdateien dieses Projekts; die wenigen Dateien, die der Server aus dem Spiel braucht, kopiert er
beim ersten Start aus deiner eigenen Installation. ZIP entpacken, `Connect game.cmd` ausführen (für einen
anderen Spielordner: `"Connect game.cmd" "D:\Spiele\Freelancer"`), dann `Start server.cmd`. Die Spieldaten liest er aus
`C:\Program Files (x86)\Microsoft Games\Freelancer` oder aus einer Mod-Installation deiner Wahl.

Unter Linux nimmst du `FLAtlas-Server-v<version>-linux-x86.tar.gz`: Es startet denselben Server unter Wine.
Entpacken, `bash connect-game.sh "/pfad/zu/Freelancer"` ausführen, dann `bash start-server.sh`, oder als
systemd-Dienst `sudo bash install-service.sh` (`--prefix /pfad/zum/praefix` für ein anderes Wine-Präfix,
weil `sudo` `WINEPREFIX` verwirft). Gib dem Dienst ein eigenes Wine-Präfix (zum Beispiel
`WINEPREFIX=~/.wine-flatlas wineboot -i`, dann `--prefix ~/.wine-flatlas`): Mit dem gemeinsamen `~/.wine`
kann ein Stopp des Dienstes auch ein Spiel beenden, das du dort spielst. Wine braucht 32-Bit-Unterstützung,
DirectPlay meist `winetricks directplay`. `journalctl -u flatlas-server` zeigt die Konsolenausgabe des
Servers (unter Wine noch nicht getestet); die Server-Protokolle stehen außerdem in der Verwaltung und im
Ordner `data`.

Spätere Versionen kommen als Update: Die Seite **Updates** sucht einmal am Tag nach Releases und installiert
für den Inhaber ein neues, nachdem sie die Prüfsummen kontrolliert hat. Unter Wine gibt es das eingebaute
Update nicht: Server beenden (`sudo systemctl stop flatlas-server` oder Strg+C), das neue Linux-Paket über
den alten Ordner entpacken (`tar -xzf <paket>.tar.gz --strip-components=1 -C /pfad/zum/ordner`; `data`
bleibt erhalten) und wieder starten. Unter **Updates > Changelogs** steht,
was sich in jeder Version geändert hat. Wie ein Release aussehen muss, steht in
[docs/RELEASING.md](docs/RELEASING.md).

## Hilfe

Das [Wiki von FLAtlas Server](https://github.com/flathack/FLAtlas-FL-Server/wiki) erklärt jede Seite der Web-Verwaltung mit Screenshot, dazu Installation, Rollen, alle Befehle und Fehlerbehebung. Es ist auf Englisch.

## Was er kann

FLAtlas Server ist aus dem Quellcode von Freelancer Decomp gebaut (Stand 1.1). Eine Verwaltung im Browser
startet und betreut mehrere Spielserver auf einem PC, als Windows-Dienst oder in einem Konsolenfenster.

- Mehrere Server nebeneinander, jeder mit eigenem Port, eigenen Konten, eigenem Spieldaten-Ordner und eigener Spielversion für Mods.
- Alle Spieler auf einer Universums- und Systemkarte, per Maus auf eine Basis oder eine freie Stelle teleportieren.
- Über 60 Chatbefehle für Spieler, Moderatoren und Administratoren, auch über eine Web-Konsole.
- Spielmechaniken, die du pro Server einschaltest: Abschussmeldungen, Todesstrafe, Kopfgeld, Duelle, automatischer Einkauf, Lager, Wegzoll, Arena, Garderobe.
- Kaufbeschränkungen nach Rang oder Ruf, Rennen gegen die Uhr und KI-Schiffe, die Administratoren spawnen.
- Backups, eine Zeitleiste früherer Kontostände, Charaktere zwischen Konten verschieben und kopieren.
- Anti-Cheat, Verbindungsqualität pro Spieler, Spielrollen, Deutsch und Englisch.

## Bereiche der Verwaltung

| Bereich | Inhalt |
| --- | --- |
| **Server** | Server anlegen, starten, stoppen, entfernen; Adresse und Port der Verwaltung |
| **Übersicht** | Status, Spielerzahl, Serverlast, letzte Meldungen |
| **Karte** | Universum und Systeme mit den Online-Spielern; Administratoren ziehen Spieler auf eine Basis oder eine freie Stelle |
| **Nachrichten** | An alle, ein System oder einen Spieler, Chatverlauf, Servernachricht, Ankündigungen, Team-Nachrichten |
| **Konsole** | Verwaltungsbefehle ausführen, Übersicht aller Befehle |
| **Serverprotokoll, Änderungsverlauf** | Was der Server meldet und wer was geändert hat |
| **Spieler online** | Ort, Schiff, Guthaben, Spielrolle, Ping und Paketverlust; Nachricht, Trennen, Sperren |
| **Konten** | Suchen, sperren, Notizen, Spielrolle, auf einen früheren Stand zurücksetzen, löschen |
| **Charaktere** | Guthaben ändern, zu einer Basis versetzen, reparieren, übertragen, zurücksetzen, löschen, jeweils mit Sicherung |
| **Spielstart** | Willkommensnachricht sowie Schiff, Ausrüstung, Basis und Credits neuer Charaktere |
| **Spielmechaniken** | Die zusätzlichen Regeln, jede einzeln schaltbar, mit Kopfgeldern, laufenden Kämpfen, Kaufregeln, Rennstrecken und gespawnten NPCs |
| **Backups** | Alle Konten sichern, täglich automatisch, Charakter oder Konto wiederherstellen, als ZIP herunterladen |
| **Anti-Cheat** | Meldungen, Regeln, Grenzwerte und Reaktion je Regel |
| **Einstellungen** | Name, Passwort, Port, Spielversion, Spieldaten-Ordner, Sprache im Spiel, Grenzwerte für die Verbindung |
| **Admin-Zugänge** | Zugänge und Rollen, gültig für alle Server |
| **Updates** | Neue Versionen aus den GitHub-Releases, Release-Notizen, Installation mit Prüfsummen |

## Erste Schritte

1. `FLAtlas-Server-v<version>-win-x86.zip` aus dem [neuesten Release](https://github.com/flathack/FLAtlas-FL-Server/releases/latest) laden und in einen Ordner deiner Wahl entpacken. Unter Linux nimmst du das `linux-x86.tar.gz` (siehe [Download](#download)).
2. Für den Dauerbetrieb `Install service.cmd` doppelklicken und die Windows-Abfrage bestätigen. Der Dienst
   `FLAtlasServer` startet dann mit Windows; `Remove service.cmd` nimmt ihn wieder heraus. Zum Ausprobieren
   öffnet `Start server.cmd` stattdessen ein Konsolenfenster.
3. Auf demselben PC **http://127.0.0.1:8080/** öffnen und den Inhaber-Zugang anlegen.
4. Im Spiel **Multiplayer** wählen. Erscheint der Server nicht, die LAN-Adresse des Server-PCs mit dem
   Spielport eingeben, standardmäßig **2302**. `127.0.0.1` funktioniert nicht, weil das Netzwerkverfahren des
   Spiels nur auf echten Netzwerkkarten lauscht.

Alle Spieler brauchen dieselben Spieldaten wie ihr Server.

## Systemvoraussetzungen

- **Betriebssystem:** Windows 10 oder neuer, oder Linux mit Wine (32-Bit-Unterstützung)
- **Spiel:** Ein installiertes Freelancer, dazu jede Mod-Installation, die ein Server nutzen soll
- **Netzwerk:** Der Spielport jedes Servers im LAN erreichbar

## Befehle

Spieler schreiben Befehle in den Spielchat. Namen mit Leerzeichen stehen in Anführungszeichen.

| Für | Befehle |
| --- | --- |
| Alle | `/help [befehl]`, `/online`, `/where`, `/pos`, `/time`, `/motd`, `/value` |
| Nachrichten | `/pm <spieler> <text>`, `/r <text>`, `/ignore <spieler>`, `/unignore <spieler\|all>`, `/ignorelist` |
| Spielen | `/givecash`, `/roll [höchstwert]`, `/coin`, `/droprep`, `/transfercode <code\|off>`, `/takechar <charakter> <code>` |
| Spielmechaniken | `/bounty`, `/bounties`, `/duel`, `/acceptduel`, `/cancelduel`, `/ffa`, `/acceptffa`, `/autobuy`, `/store`, `/unstore`, `/storage`, `/tax`, `/pay`, `/arena`, `/return`, `/wardrobe`, `/race` |
| Moderation | `/players`, `/playerinfo`, `/kick`, `/ban <spieler> <minuten> [grund]`, `/unban`, `/announce`, `/msg`, `/getrep`, `/factions`, `/listcargo` |
| Administration | `/setcash`, `/addcash`, `/beam`, `/setrep`, `/addcargo`, `/removecargo`, `/kill`, `/chase`, `/pull`, `/npc`, `/race create\|point\|radius\|delete` |

Im Spiel gelten die **Spielrollen**, die du unter Konten vergibst: Moderatoren nutzen die Moderationsbefehle,
Administratoren alle und dazu die Adminrechte des Originalspiels (Universum-Chat). In der Web-Konsole gelten die
Rollen der Verwaltung. `/help <befehl>` erklärt jeden Befehl. Die Befehle des Originalspiels bleiben erhalten.

Spieler holen eigene Charaktere selbst in ein anderes Konto: `/transfercode <code>` im alten Konto (gilt
24 Stunden), dann `/takechar "<name>" <code>` im neuen, während das alte Konto offline ist.

## Spielmechaniken

Unter **Spielmechaniken** schaltest du diese Regeln pro Server ein und aus:

- **Abschussmeldungen:** wer wen abgeschossen hat, mit dem Anteil am Rumpfschaden, im System oder an alle.
- **Todesstrafe** (standardmäßig aus): Ein Abschuss kostet einen Anteil vom Wert von Schiff und Fracht,
  höchstens das Guthaben; ein Teil davon geht an den Schützen. In der Arena gilt sie nicht.
- **Kopfgeld:** `/bounty` setzt Credits auf einen Spieler aus; wer ihn abschießt, bekommt sie.
- **Wetten:** `/duel` ist ein Kampf um einen Einsatz, `/ffa` Jeder gegen jeden im System. Der Sieger bekommt
  alle Einsätze; wer sich ausloggt oder das System verlässt, verliert.
- **Automatischer Einkauf:** `/autobuy alle an` kauft beim Andocken Munition, Nanobots und Schildbatterien
  nach, zum Preis aus `goods.ini`. Basisabhängige Preise und Reparaturen berücksichtigt er nicht.
- **Lager:** `/store`, `/unstore` und `/storage` lagern Fracht pro Konto und Basis.
- **Wegzoll:** `/tax` verlangt Credits von einem Spieler im selben System, `/pay` zahlt binnen einer Minute.
- **Arena** (standardmäßig aus): `/arena` bringt einen Spieler von einer Basis zur Arena-Basis, `/return` zurück.
- **Garderobe:** `/wardrobe list kopf` zeigt die Köpfe, `/wardrobe kopf <name>` oder `körper <name>` wechselt
  sie. Der Spieler wird dafür kurz getrennt, weil das Aussehen in der Charakterdatei steht.

## Kaufbeschränkungen, Rennen und NPCs

- **Kaufbeschränkungen:** Unter Spielmechaniken verlangt eine Ware oder ein Schiff (per Kürzel, zum Beispiel
  `li_elite` oder `li_gun01_mark03`) einen Mindestrang oder einen Mindestruf bei einer Fraktion. Der Server
  lehnt den Kauf ab und schickt dem Spieler seine echten Credits und seine Ausrüstung neu; nach einem
  abgelehnten Schiffskauf trennt er ihn kurz, weil das Spiel das neue Schiff schon anzeigt. Administratoren im
  Spiel sind ausgenommen.
- **Rennen:** Ein Administrator fliegt die Strecke ab und schreibt `/race create <name>` am Start und
  `/race point <name>` an jedem Kontrollpunkt; der letzte ist das Ziel. Spieler melden sich im All im System der
  Strecke mit `/race join <name>` an, `/race go` startet einen Countdown von zehn Sekunden. Der Server liest
  die Positionen einmal pro Sekunde und rechnet aus, wann ein Schiff dazwischen einen Kontrollpunkt passiert
  hat. `/race top <name>` und die Web-Seite zeigen die zehn besten Zeiten. `/race radius <name> <meter>`
  legt die Größe der Kontrollpunkte fest (Standard 600 m).
- **NPCs:** `/npc spawn <schiff> [anzahl] [fraktion]` setzt bis zu zehn KI-Schiffe neben den Administrator,
  ohne Fraktion als Nomaden; `/npc list` und `/npc clear` zeigen und entfernen sie, ebenso die Web-Seite. Sie
  fliegen mit dem Standardverhalten des Spiels für Jäger und sind nach einem Neustart des Servers weg.

## Mehrere Server, Mods und Versionen

- `FLAtlasServer.exe` ist die Verwaltung. Sie startet jeden Spielserver als eigenen Prozess mit eigenem
  Datenordner. Die Server haben keine eigene Oberfläche und antworten nur der Verwaltung auf diesem PC.
- Ein Neustart aus der Verwaltung warnt zuerst die Spieler und wartet 60 Sekunden; Wartezeit und Nachricht
  stellst du pro Server unter **Einstellungen > Neu starten** ein. Während der Wartezeit wird der Knopf zu „Sofort neu starten“, der ohne Wartezeit neu startet.
- Der Server früherer Versionen heißt `main` und behält den Ordner `data`; neue Server liegen unter
  `data\instances\<kennung>`. Stürzt ein Server ab, startet die Verwaltung ihn neu, höchstens dreimal in zehn Minuten.
- Jeder Server kann seine Spieldaten aus einer eigenen Freelancer-Installation lesen, zum Beispiel einer Kopie mit Mod.
- Das Spiel zeigt nur Server, deren Version zur eigenen `Freelancer.exe` passt. Mods nutzen das, deshalb
  meldet jeder Server eine einstellbare Spielversion wie `1.0.1223.11`.

## Daten, Backups und Sicherheit

- Alles liegt in `data`: Admin-Zugänge und Serverliste unter `manager`, für jeden Server die Konten unter
  `Accts\MultiPlayer`, Einstellungen und Protokolle unter `service` und Backups unter `backups`. Zum Sichern
  den ganzen Ordner mitnehmen. Er gehört nicht ins Git-Repo.
- Bei jedem Ausloggen legt der Server eine Kopie des Kontos ab, so lassen sich Charakter oder Konto auf einen
  früheren Stand zurücksetzen. Dazu kommt ein tägliches Backup.
- Die Verwaltung lauscht zuerst nur auf diesem PC. Sie nutzt unverschlüsseltes HTTP, deshalb nur in einem
  vertrauenswürdigen Netz öffnen (Adresse `0.0.0.0`), nie ins Internet. Passwörter liegen als PBKDF2-Prüfwert vor.
- Der Anti-Cheat meldet zunächst nur. Erst wenn eine Regel keine ehrlichen Spieler trifft, auf Verwerfen,
  Trennen oder Sperren stellen.
- IP-Adressen sehen nur Administratoren.

## Über dieses Repository

Dieses Repository ist die öffentliche Download- und Release-Seite von FLAtlas Server. Releases enthalten nur die Programmdateien des Projekts; die Dateien, die der Server aus dem Spiel braucht, kopiert `Connect game.cmd` (unter Linux `connect-game.sh`) aus deiner eigenen Installation. Was sich in jeder Version geändert hat, steht in [CHANGELOG.md](CHANGELOG.md), wie ein Release gebaut wird und was die Update-Suche prüft, in [docs/RELEASING.md](docs/RELEASING.md), die Herkunft aller Dateien in [CREDITS.md](CREDITS.md).

## Hilfe und Community

Erste Hilfe bieten das [Wiki](https://github.com/flathack/FLAtlas-FL-Server/wiki) und seine Seite [Troubleshooting](https://github.com/flathack/FLAtlas-FL-Server/wiki/Troubleshooting). Fragen, Fehlermeldungen und Ideen gern im Discord von FL Atlas:

[![FLAtlas Discord beitreten](https://img.shields.io/badge/FLAtlas-Discord%20Community-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/fY9qweRWGn)

Bitte nenne Serverversion, Spielversion und Mod und was das Serverprotokoll zeigt.

## Hinweise

FLAtlas Server baut auf [Freelancer Decomp](https://github.com/flathack/Freelancer-Decomp) auf, veröffentlicht
unter The Unlicense. Die Auswahl der Befehle orientiert sich an FLHook von The Starport; Code daraus ist nicht
übernommen.

Freelancer ist ein Spiel von Digital Anvil und Microsoft. FLAtlas Server ist ein unabhängiges
Community-Projekt und steht in keiner Verbindung zu Microsoft oder Digital Anvil. Du brauchst eine eigene Kopie
des Spiels.
