# Releases und Updates

FLAtlas Server sucht neue Versionen unter den GitHub-Releases eines Repositorys, standardmäßig
`flathack/FLAtlas-FL-Server` (einstellbar unter **Updates**). Damit ein Release als Update erkannt und
installiert wird, muss es genau so aussehen, wie hier beschrieben. `tools\make-release.ps1` baut es so.

## Was ein Release enthält

| Teil | Form | Beispiel |
| --- | --- | --- |
| Tag | `v<major>.<minor>.<patch>`, optional mit Vorabzusatz | `v0.2.0`, `v0.3.0-beta.1` |
| Paket | `FLAtlas-Server-v<version>-win-x86.zip` | `FLAtlas-Server-v0.2.0-win-x86.zip` |
| Prüfsumme | `<paket>.sha256`, eine Zeile `<sha256 klein>  <dateiname>` | `FLAtlas-Server-v0.2.0-win-x86.zip.sha256` |
| Linux-Paket | `FLAtlas-Server-v<version>-linux-x86.tar.gz` mit eigener `.sha256` | `FLAtlas-Server-v0.2.0-linux-x86.tar.gz` |
| Notizen | Text des Releases auf GitHub, Markdown: der Abschnitt dieser Version aus `CHANGELOG.md` | wird in der Verwaltung angezeigt |

- Die Update-Suche lädt nur das `win-x86`-ZIP. Unter Wine gibt es das eingebaute Update nicht (Wine hat
  kein `tar.exe`); die Verwaltung lehnt es dort mit „Under Wine the built-in update is not available. Please
  update by hand with the Linux package.“ ab. Das Linux-Paket ist für die Erstinstallation und für das
  Update von Hand (siehe [Linux von Hand aktualisieren](#linux-von-hand-aktualisieren)).
- Releases ohne ZIP und `.sha256` übergeht die Suche. Entwürfe ebenfalls, Vorabversionen nur, wenn
  **Auch Vorabversionen anbieten** eingeschaltet ist.
- Die Version im Tag muss gleich `ProductVersion` in `src\FLService\ServiceCore.h`, `ProductVersion` in
  `src\FLService\FLService.rc` und `version` in `Cargo.toml` sein. `FILEVERSION` in `FLService.rc` bleibt
  `1,0,1254,11`, sonst findet das Spiel den Server nicht.

## Inhalt des Pakets

Das ZIP spiegelt den Programmordner. Pfade mit `/`, relativ zu dem Ordner, der `bin` enthält.

```
update.json
bin/FLAtlasServer.exe
bin/*.dll                       nur die aus diesem Repo gebauten
bin/probe/FreeLancer.exe        Testspieler für den Selbsttest
tools/configure.ps1, tools/install-service.ps1, tools/remove-service.ps1
Start server.cmd, Connect game.cmd, Install service.cmd, Remove service.cmd
README.md, README.de.md, CREDITS.md, LICENSE, CHANGELOG.md
```

Das Linux-Paket enthält einen Ordner `FLAtlas-Server-v<version>-linux-x86/` mit denselben Programmdateien
(`bin/`, Texte, `CHANGELOG.md`), aber ohne `.cmd`-Dateien, `tools/` und `update.json`. Dafür liegen die
Skripte aus `linux/` im Ordner: `start-server.sh`, `connect-game.sh`, `install-service.sh` und
`remove-service.sh`. Der Server bleibt ein Windows-Programm und läuft unter Wine; ein Neustart endet
mit Code 10 (`FLATLAS_SUPERVISED=1`), den das Startskript beziehungsweise systemd mit einem neuen Start
beantwortet. `make-release.ps1` schreibt die Skripte immer mit LF-Zeilenenden ins Paket.

Das `.tar.gz` schreibt `make-release.ps1` selbst (ustar, `Write-TarGz`), nicht mit dem `tar.exe` von
Windows, das jede Datei mit 0666/0777 ablegt. Ordner haben 0755, Dateien 0644, `*.sh` und `*.exe` 0755;
Besitzer ist 0/0 (`root`), wie bei Release-Archiven üblich: Wer als normaler Benutzer entpackt, bekommt
die Dateien ohnehin selbst, und als `root` entpackt kann sie nur `root` ändern. Prüfen mit
`tar -tvzf FLAtlas-Server-v<version>-linux-x86.tar.gz`. `install-service.sh` nimmt außerdem Gruppe und
anderen das Schreibrecht auf alles im Programmordner, was für alle beschreibbar ist, und meldet das.

Zum Beenden schicken die Skripte kein Signal: Auf SIGTERM beendet Wine das Programm sofort, ohne dass die
Spielserver geordnet herunterfahren. Stattdessen legen sie die Datei `data/manager/stop.request` an (der
Ordner `data` neben `bin`). Die Verwaltung prüft jede Sekunde darauf, beendet dann alle Server der Reihe
nach und endet mit Code 0. `start-server.sh` tut das bei Strg+C (ein zweites Strg+C beendet sofort), der
systemd-Dienst in `ExecStop=`, das bis zu 50 Sekunden auf das Ende wartet; erst danach sendet systemd
SIGTERM an das, was noch läuft. Vor jedem Start löschen beide eine liegengebliebene Anfrage.

**Nie im Paket:** Dateien des Originalspiels (`resources.dll`, `freelancer.ini`, `dacomsrv.ini`,
`MPNewCharacter.fl`, `NewPlayer.fl`, überhaupt `*.fl`), `bin/probe/probe-dacom.ini` (der Selbsttest erzeugt sie
aus der `dacomsrv.ini` des Spiels, sie enthält einen lokalen Pfad), der Ordner `data`, `.pdb`/`.ilk`/`.lib`/`.exp`.
Die Spieldateien holt `tools\configure.ps1` auf dem Ziel-PC aus dessen eigener Installation;
`Start server.cmd` und die Dienst-Einrichtung rufen es auf, wenn `bin\freelancer.ini` fehlt.

### update.json

```json
{
  "format": 1,
  "product": "FLAtlas Server",
  "version": "0.2.0",
  "minimumVersion": "0.1.0",
  "platform": "win-x86",
  "files": [
    { "path": "bin/FLAtlasServer.exe", "size": 3146240, "sha256": "…" }
  ]
}
```

| Feld | Bedeutung |
| --- | --- |
| `format` | Immer `1`. Ein späteres Format bekommt eine neue Zahl, alte Versionen lehnen es dann ab. |
| `product` | Genau `FLAtlas Server`. |
| `version` | Gleich dem Tag ohne `v`. |
| `minimumVersion` | Älteste Version, die direkt auf diese aktualisieren darf. Ältere bekommen eine Meldung, erst ein früheres Update zu installieren. |
| `platform` | `win-x86` (der Server ist ein 32-Bit-Programm wie das Spiel). |
| `files` | Jede Datei, die das Update ersetzt oder neu anlegt, mit Größe und SHA-256 (klein geschrieben). Dateien, die hier fehlen, bleiben unverändert. |

## Was beim Update passiert

Das gilt nur für Windows. Unter Wine lehnt die Verwaltung die Installation ab; dort wird von Hand
aktualisiert (siehe [Linux von Hand aktualisieren](#linux-von-hand-aktualisieren)).

1. **Suchen:** Die Verwaltung fragt `https://api.github.com/repos/<repository>/releases` ab, einmal am Tag
   (abschaltbar) oder über **Jetzt suchen**. Angezeigt wird das neueste passende Release, das neuer ist als
   die laufende Version.
2. **Installieren** (nur Inhaber): ZIP und `.sha256` werden geladen, die Prüfsumme verglichen und das ZIP
   mit dem `tar.exe` von Windows nach `data\manager\updates\v<version>` entpackt. Danach prüft der Server
   `update.json`: Produkt, Version, Plattform, Mindestversion, dass jeder Pfad unter `bin/`, `tools/` oder
   im Programmordner selbst liegt und keine Spieldatei ist, und Größe und SHA-256 jeder Datei.
3. **Ersetzen:** Alle Spielserver werden beendet. Jede vorhandene Datei wird zu `<name>.old` umbenannt
   (Windows erlaubt das auch bei laufenden Programmen), die neue an ihre Stelle kopiert. Scheitert eine
   Datei, werden alle schon ersetzten zurückgestellt und die Server wieder gestartet.
4. **Neustart:** Die Verwaltung startet neu, als Dienst über die Wiederherstellungsregel des Dienstes, in
   der Konsole als neues Fenster. Beim nächsten Start löscht sie die `.old`-Dateien.

Läuft der Server als Dienst, braucht das Dienstkonto Schreibrechte auf den Programmordner.
`tools\install-service.ps1` vergibt sie; ein Dienst, der mit einer älteren Version eingerichtet wurde, muss
dafür einmal neu eingerichtet werden (`Install service.cmd`).

## Changelog

`CHANGELOG.md` im Repo-Wurzelordner ist die einzige Quelle für Änderungsnotizen. Sie ist Englisch, wie
alles außer der Oberfläche. Jede Version hat genau einen Abschnitt, die neueste oben:

```markdown
## v0.2.0 - 2026-11-21

### Added
- …

### Changed
- …

### Fixed
- …
```

- Die Überschrift muss `## v<version> - <JJJJ-MM-TT>` sein. Danach sucht `make-release.ps1`, und die
  Oberfläche teilt die Datei so in Versionen auf.
- Erlaubt sind Überschriften, Listen mit `-`, Absätze, `` `Code` `` und `**fett**`. Mehr zeigt die
  Oberfläche nicht an.
- `FLService.rc` bettet `CHANGELOG.md` in `FLAtlasServer.exe` ein. Die Verwaltung liefert sie unter
  `/changelog.md` aus und zeigt sie unter **Updates > Changelogs**, die installierte Version markiert.
  Die Notizen eines neueren Releases zeigt dieselbe Seite aus dem Text des GitHub-Releases.
- `make-release.ps1` bricht ab, wenn der Abschnitt der Release-Version fehlt oder leer ist. Es schreibt ihn
  nach `dist\notes-v<version>.md` und nimmt ihn als Notiz des GitHub-Releases. GitHub, Paket und
  Oberfläche zeigen also denselben Text.

## Ein Release machen

1. Version in `ServiceCore.h`, `FLService.rc` (`FileVersion`, `ProductVersion`) und `Cargo.toml` erhöhen,
   die Badges in `README.md` und `README.de.md` mitziehen.
2. Den Abschnitt der neuen Version in `CHANGELOG.md` schreiben (siehe oben), mit dem Datum des Releases.
3. Bauen und den Selbsttest laufen lassen; alle Schritte müssen bestehen.
4. Pakete bauen:
   ```powershell
   powershell -NoProfile -ExecutionPolicy Bypass -File .\tools\make-release.ps1
   ```
   Das legt `dist\FLAtlas-Server-v<version>-win-x86.zip`, `dist\FLAtlas-Server-v<version>-linux-x86.tar.gz`,
   ihre `.sha256` und `dist\notes-v<version>.md` an. Mit `-SkipBuild` nimmt es
   das vorhandene `bin`, mit `-MinimumVersion` setzt es `minimumVersion`. In jedem Fall bricht es ab, wenn
   die `ProductVersion` von `bin\FLAtlasServer.exe` nicht die Release-Version ist (zum Beispiel ein altes
   `bin` mit `-SkipBuild`).
5. Veröffentlichen, mit dem Changelog-Abschnitt als Notiz (das Skript gibt den Befehl mit vollen Pfaden aus):
   ```powershell
   gh release create v0.2.0 dist\FLAtlas-Server-v0.2.0-win-x86.zip dist\FLAtlas-Server-v0.2.0-win-x86.zip.sha256 dist\FLAtlas-Server-v0.2.0-linux-x86.tar.gz dist\FLAtlas-Server-v0.2.0-linux-x86.tar.gz.sha256 --repo flathack/FLAtlas-FL-Server --title "FLAtlas Server v0.2.0" --notes-file dist\notes-v0.2.0.md
   ```

Das Paket entsteht lokal, nicht auf GitHub Actions: Der Bau braucht ein installiertes Freelancer.

## Erstinstallation aus einem Release

### Windows

ZIP in einen Ordner entpacken, `Connect game.cmd` (oder gleich `Start server.cmd`) ausführen, dann
`http://127.0.0.1:8080/` öffnen. Ein anderer Spielordner: `"Connect game.cmd" "D:\Spiele\Freelancer"`.

### Linux

Voraussetzungen: Wine mit 32-Bit-Unterstützung und eine Freelancer-Installation, die Wine lesen kann
(zum Beispiel in `~/.wine/drive_c`). Unter Wine braucht DirectPlay meist die Windows-Fassung:
`winetricks directplay`.

```bash
tar -xzf FLAtlas-Server-v0.2.0-linux-x86.tar.gz
cd FLAtlas-Server-v0.2.0-linux-x86
bash connect-game.sh "/pfad/zu/Freelancer"   # ohne Angabe: ~/.wine/drive_c/Program Files (x86)/Microsoft Games/Freelancer
bash start-server.sh                         # oder als Dienst: sudo bash install-service.sh
```

Danach `http://127.0.0.1:8080/` öffnen. `WINEPREFIX` wählt ein anderes Wine-Präfix. Das Linux-Paket ist
nur mit dem Windows-Bau erstellt, nicht automatisch unter Linux getestet.

- `sudo` verwirft `WINEPREFIX`. Für ein anderes Präfix beim Dienst `sudo bash install-service.sh --prefix
  /pfad/zum/praefix` oder `sudo WINEPREFIX="$WINEPREFIX" bash install-service.sh`. Der Dienst läuft als
  der Benutzer, der `sudo` aufruft; er braucht Schreibrechte auf den Programmordner und `bin`
  (sonst bricht das Skript mit einem `chown`-Hinweis ab).
- Eigenes Wine-Präfix für den Dienst: systemd beendet ihn mit `KillMode=control-group`. Teilt er sich
  das Präfix (Standard `~/.wine`) mit einem Spiel, das der Benutzer dort spielt, teilen sich beide einen
  `wineserver`, und ein Stopp oder Neustart des Dienstes kann das Spiel mit beenden. Ohne `--prefix`
  mit `~/.wine` warnt `install-service.sh` davor, installiert aber weiter. Besser:
  ```bash
  WINEPREFIX=~/.wine-flatlas wineboot -i
  WINEPREFIX=~/.wine-flatlas bash connect-game.sh "/pfad/zu/Freelancer"
  sudo bash install-service.sh --prefix ~/.wine-flatlas
  ```
  (`winetricks directplay` dann ebenfalls mit diesem `WINEPREFIX`.)
- Protokolle: Mit `FLATLAS_SUPERVISED=1` (Dienst und `start-server.sh`) gibt die Verwaltung ihre
  Konsolenzeilen auf stdout aus; `journalctl -u flatlas-server` zeigt so die Konsolenausgabe des Servers.
  Unter Wine ist das noch nicht getestet. Die Protokolle der Spielserver stehen außerdem in der
  Verwaltung und als Dateien im Ordner `data`.

### Linux von Hand aktualisieren

Das eingebaute Update gibt es unter Wine nicht. Ein neues Linux-Paket wird über den alten Ordner entpackt;
`data/` und die Spieldateien in `bin` (`freelancer.ini` usw.) sind nicht im Paket und bleiben erhalten:

```bash
sudo systemctl stop flatlas-server        # oder Strg+C in start-server.sh
tar -xzf FLAtlas-Server-v0.3.0-linux-x86.tar.gz --strip-components=1 -C /pfad/zum/alten/ordner
sudo systemctl start flatlas-server       # oder wieder bash start-server.sh
```

Den Ordner nicht wechseln: Der Dienst zeigt auf den alten Ordner, und dort liegt `data`.
