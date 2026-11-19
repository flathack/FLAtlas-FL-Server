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
| Notizen | Text des Releases auf GitHub, Markdown | wird in der Verwaltung angezeigt |

- Releases ohne beide Dateien übergeht die Suche. Entwürfe ebenfalls, Vorabversionen nur, wenn
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
Server starten.cmd, Spiel verbinden.cmd, Dienst installieren.cmd, Dienst entfernen.cmd
README.md, README.de.md, CREDITS.md, LICENSE
```

**Nie im Paket:** Dateien des Originalspiels (`resources.dll`, `freelancer.ini`, `dacomsrv.ini`,
`MPNewCharacter.fl`, `NewPlayer.fl`, überhaupt `*.fl`), `bin/probe/probe-dacom.ini` (der Selbsttest erzeugt sie
aus der `dacomsrv.ini` des Spiels, sie enthält einen lokalen Pfad), der Ordner `data`, `.pdb`/`.ilk`/`.lib`/`.exp`.
Die Spieldateien holt `tools\configure.ps1` auf dem Ziel-PC aus dessen eigener Installation;
`Server starten.cmd` und die Dienst-Einrichtung rufen es auf, wenn `bin\freelancer.ini` fehlt.

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
dafür einmal neu eingerichtet werden (`Dienst installieren.cmd`).

## Ein Release machen

1. Version in `ServiceCore.h`, `FLService.rc` (`FileVersion`, `ProductVersion`) und `Cargo.toml` erhöhen.
2. Bauen und den Selbsttest laufen lassen; alle Schritte müssen bestehen.
3. Paket bauen:
   ```powershell
   powershell -NoProfile -ExecutionPolicy Bypass -File .\tools\make-release.ps1
   ```
   Das legt `dist\FLAtlas-Server-v<version>-win-x86.zip` und die `.sha256` an. Mit `-SkipBuild` nimmt es
   das vorhandene `bin`, mit `-MinimumVersion` setzt es `minimumVersion`. In jedem Fall bricht es ab, wenn
   die `ProductVersion` von `bin\FLAtlasServer.exe` nicht die Release-Version ist (zum Beispiel ein altes
   `bin` mit `-SkipBuild`).
4. Notizen schreiben und veröffentlichen:
   ```powershell
   gh release create v0.2.0 dist\FLAtlas-Server-v0.2.0-win-x86.zip dist\FLAtlas-Server-v0.2.0-win-x86.zip.sha256 --repo flathack/FLAtlas-FL-Server --title "FLAtlas Server v0.2.0" --notes-file notes.md
   ```

Das Paket entsteht lokal, nicht auf GitHub Actions: Der Bau braucht ein installiertes Freelancer.

## Erstinstallation aus einem Release

ZIP in einen Ordner entpacken, `Spiel verbinden.cmd` (oder gleich `Server starten.cmd`) ausführen, dann
`http://127.0.0.1:8080/` öffnen. Ein anderer Spielordner: `Spiel verbinden.cmd "D:\Spiele\Freelancer"`.
