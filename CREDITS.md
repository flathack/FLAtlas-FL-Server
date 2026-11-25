# Herkunft der Dateien

## Quellcode

Der Code unter `src\` stammt aus dem Projekt **Freelancer Decomp**
(https://github.com/flathack/Freelancer-Decomp, Stand `0d5fc5e`), veröffentlicht unter The
Unlicense. Die mitgelieferten Bibliotheken unter `src\zlib\vendor` und `src\qhull\vendor` behalten
ihre eigenen Lizenzen. `upstream-docs\` enthält Notizen desselben Projekts.

Für diesen Build neu: `src\FLService\`, `src\FLProbe\`, `src\Server\server_anticheat.cpp`,
`src\include\server_anticheat.h`, `launcher\`, `tools\`. Entfernt: das Fensterprogramm `src\FLServer`
und `src\Resources\serverresources`. Angepasst: `src\Server\IServerImpl.cpp`, `server_chat.cpp`,
`server_savegame.cpp`, `Server.vcxproj`, `src\RemoteClient\RemoteClient_main.cpp`, `RemoteClient.vcxproj`,
`src\Common\common_misc.cpp`, `src\Directory.Build.*` und `src\Freelancer.sln`. Für die Verbindungsmessung
kamen `src\DALib\CDPServer.cpp`, `src\RemoteClient\RemoteClient_transport.cpp`, `src\include\dalib_net.h`
und `src\include\remoteclient.h` dazu, für Fracht `src\include\common_server.h`, für Schaden und
Abschüsse `src\Server\server_gameobject.cpp`.

Welche Befehle ein Freelancer-Server braucht, orientiert sich an FLHook von The Starport
(https://github.com/TheStarport/FLHook). Es wurde kein Code daraus übernommen.

Vier Fehlerbehebungen in `src\Server` gehen auf FLSharp von BC46 zurück
(https://github.com/BC46/FLSharp, MIT-Lizenz): kein Rauswurf mehr nach dem Rückkauf des eigenen
Schiffs, kein Absturz bei unbekanntem Schiff in einer Charakterdatei, Munition pro Lauf bei
Werfern mit mehreren Läufen und kein Absturz bei einer fehlenden DLL aus `freelancer.ini`. FLSharp
patcht die fertige `server.dll`; hier sind die Stellen im Quellcode neu geschrieben, mit dem
Kommentar `FLAtlas Server` markiert. Die Originaltexte unter
`src\Resources` bleiben lokal und gehören nicht ins Git-Repo.

## Gestaltung

Farben, Formen, Banner und Icon stammen aus FL Atlas von flathack. Die Schrift Inter liegt unter
`src\FLService\web\fonts` mit ihrer Lizenz, der SIL Open Font License 1.1.

## Originaldateien des Spiels

Quelle ist immer die eigene Installation: `C:\Program Files (x86)\Microsoft Games\Freelancer`.
`tools\configure.ps1` kopiert beim Bauen diese Dateien:

| Quelle | Ziel | Zweck |
|---|---|---|
| `EXE\resources.dll` | `bin\resources.dll` | Namen von Systemen, Basen und Schiffen |
| `EXE\dacomsrv.ini` | `bin\dacomsrv.ini` | Engine-Einstellungen |
| `EXE\mpnewcharacter.fl` | `bin\MPNewCharacter.fl` | Vorlage für neue Charaktere |
| `EXE\newplayer.fl` | `bin\NewPlayer.fl` | Vorlage für neue Spieler |
| `EXE\freelancer.ini` | `bin\freelancer.ini` | Spieleinstellungen, mit angepassten Pfaden |

Nur gelesen, nicht kopiert: `DATA\` (alle Spieldaten) sowie `EXE\InfoCards.dll`, `EXE\MiscText.dll`,
`EXE\NameResources.dll`, `EXE\EquipResources.dll`, `EXE\OfferBribeResources.dll` und
`EXE\MiscTextInfo2.dll`. Der automatische Test lädt außerdem die Module aus `EXE\` (unter anderem
`remoteserver.dll`, `common.dll`, `dalib.dll`, `dacom.dll`).

## Bauwerkzeuge (nur zum Bauen, in `.deps\`)

- DirectX 8.1 SDK (Header und Bibliotheken) aus dem Internet Archive, `dx81sdk_full`
