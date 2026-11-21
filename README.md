<h1 align="center">FLAtlas Server</h1>

<p align="center">
  <strong>A dedicated Freelancer server with a web administration in the style of FL Atlas.</strong>
</p>

<p align="center">
  <a href="README.de.md">Deutsch</a>
  |
  <strong>English</strong>
</p>

<p align="center">
  <img alt="Version 0.2.0" src="https://img.shields.io/badge/Version-0.2.0-00d5ff?style=for-the-badge">
  <img alt="Windows" src="https://img.shields.io/badge/Windows-Service%20%2B%20Console-0078D4?style=for-the-badge&logo=windows&logoColor=white">
  <img alt="Freelancer" src="https://img.shields.io/badge/Freelancer-Multiplayer-00A3FF?style=for-the-badge">
  <img alt="Web administration" src="https://img.shields.io/badge/Web-Administration-7B61FF?style=for-the-badge">
  <a href="https://github.com/flathack/FLAtlas-FL-Server/wiki"><img alt="Help in the wiki" src="https://img.shields.io/badge/Help-Wiki-2EA44F?style=for-the-badge"></a>
</p>

> The built program folder contains the original game's own files, for the private use of the person who owns the game, on their own computer. It must not be shared or published. This repository leaves those files out.

## Download

Releases are published on the GitHub releases page as `FLAtlas-Server-v<version>-win-x86.zip`. The package
only holds this project's program files; the few files the server needs from the game are copied from your
own installation on first start. Extract the ZIP, run `Connect game.cmd` (for another game folder:
`"Connect game.cmd" "D:\Games\Freelancer"`), then `Start server.cmd`. You can also build it yourself
(see [Building](#building)). The game data is read from `C:\Program Files (x86)\Microsoft Games\Freelancer`
or from any mod installation you choose.

On Linux, take `FLAtlas-Server-v<version>-linux-x86.tar.gz`: it runs the same server under Wine. Extract it,
run `bash connect-game.sh "/path/to/Freelancer"`, then `bash start-server.sh`, or `sudo bash install-service.sh`
for a systemd service (`--prefix /path/to/prefix` for another Wine prefix, since `sudo` drops `WINEPREFIX`).
Give the service a Wine prefix of its own (for example `WINEPREFIX=~/.wine-flatlas wineboot -i`, then
`--prefix ~/.wine-flatlas`): with the shared `~/.wine`, stopping the service can also end a game you run there.
Wine needs 32-bit support, and DirectPlay usually needs `winetricks directplay`. `journalctl -u flatlas-server`
shows the server's console output (not yet tested under Wine); the server logs are also in the web
administration and in the `data` folder.

Later versions come as updates: the **Updates** page checks the releases once a day and installs a new one
for the owner after checking its hashes. Under Wine the built-in update is not available: stop the server
(`sudo systemctl stop flatlas-server` or Ctrl+C), extract the new Linux package over the old folder
(`tar -xzf <package>.tar.gz --strip-components=1 -C /path/to/folder`; `data` is kept) and start it again. **Updates > Changelogs** lists what changed in each version. How a release has to look is described in [docs/RELEASING.md](docs/RELEASING.md).

## Help

The [FLAtlas Server wiki](https://github.com/flathack/FLAtlas-FL-Server/wiki) explains every page of the web administration with a screenshot, plus installation, roles, all commands and troubleshooting.

## What It Does

FLAtlas Server is built from the source code of the Freelancer Decomp project (version 1.1). One
administration in the browser starts and looks after several game servers on one PC, as a Windows service or
in a console window.

- Run several servers side by side, each with its own port, accounts, game data folder and game version for mods.
- See every player on a universe and system map and drag them onto a base or a free spot to teleport them.
- Over 60 chat commands for players, moderators and administrators, also from a web console.
- Game mechanics you switch on per server: kill messages, death penalty, bounties, duels, automatic purchase, storage, tolls, arena, wardrobe.
- Purchase restrictions by rank or reputation, races against the clock, and AI ships administrators spawn.
- Backups, a timeline of earlier account states, moving and copying characters between accounts.
- Anti-cheat, connection quality per player, game roles, German and English.

## Included Areas

| Area | Contents |
| --- | --- |
| **Servers** | Create, start, stop and remove servers; address and port of the administration |
| **Overview** | Status, player count, server load, latest messages |
| **Map** | Universe and systems with the players online; administrators drag players to a base or a free spot |
| **Messages** | To everyone, a system or a player, chat history, server message, announcements, team messages |
| **Console** | Run administration commands, list of all commands |
| **Server log, change history** | What the server reports and who changed what |
| **Players online** | Location, ship, credits, game role, ping and packet loss; message, disconnect, ban |
| **Accounts** | Search, ban, notes, game role, reset to an earlier state, delete |
| **Characters** | Change credits, move to a base, repair, transfer, reset, delete, each with a backup |
| **New players** | Welcome message plus ship, equipment, base and credits of new characters |
| **Game mechanics** | The optional rules, each on or off, with bounties, running fights, purchase rules, race tracks and spawned NPCs |
| **Backups** | Back up all accounts, daily automatic backups, restore a character or account, download as ZIP |
| **Anti-cheat** | Reports, rules, limits and the reaction per rule |
| **Settings** | Name, password, port, game version, game data folder, language in the game, connection limits |
| **Admin logins** | Logins and roles, valid for all servers |
| **Updates** | New versions from the GitHub releases, release notes, installation with hash checks |

## Getting Started

1. Build the server (see [Building](#building)).
2. To run it permanently, double-click `Install service.cmd` and confirm the Windows prompt. The service
   `FLAtlasServer` then starts with Windows; `Remove service.cmd` removes it. For a quick try,
   `Start server.cmd` opens a console window instead.
3. Open **http://127.0.0.1:8080/** on the same PC and create the owner login.
4. In the game, choose **Multiplayer**. If the server does not appear, enter the LAN address of the server PC
   with the game port, **2302** by default. `127.0.0.1` does not work, because the game's network layer only
   listens on real network adapters.

All players need the same game data as their server.

## System Requirements

- **Operating system:** Windows 10 or newer, or Linux with Wine (32-bit support)
- **Game:** An installed Freelancer, plus any mod installation a server should use
- **For building:** Visual Studio 2022 Build Tools (C++), Rust and 7-Zip
- **Network:** The game port of each server reachable in the LAN

## Commands

Players type commands into the game chat. Names with spaces go in quotes.

| For | Commands |
| --- | --- |
| Everyone | `/help [command]`, `/online`, `/where`, `/pos`, `/time`, `/motd`, `/value` |
| Messages | `/pm <player> <text>`, `/r <text>`, `/ignore <player>`, `/unignore <player\|all>`, `/ignorelist` |
| Playing | `/givecash`, `/roll [max]`, `/coin`, `/droprep`, `/transfercode <code\|off>`, `/takechar <character> <code>` |
| Game mechanics | `/bounty`, `/bounties`, `/duel`, `/acceptduel`, `/cancelduel`, `/ffa`, `/acceptffa`, `/autobuy`, `/store`, `/unstore`, `/storage`, `/tax`, `/pay`, `/arena`, `/return`, `/wardrobe`, `/race` |
| Moderators | `/players`, `/playerinfo`, `/kick`, `/ban <player> <minutes> [reason]`, `/unban`, `/announce`, `/msg`, `/getrep`, `/factions`, `/listcargo` |
| Administrators | `/setcash`, `/addcash`, `/beam`, `/setrep`, `/addcargo`, `/removecargo`, `/kill`, `/chase`, `/pull`, `/npc`, `/race create\|point\|radius\|delete` |

In the game the **game roles** apply, which you grant under Accounts: moderators get the moderation commands,
administrators all of them plus the original game's admin rights (universe chat). In the web console the roles
of the administration apply. `/help <command>` explains each one. The original game's commands keep working.

Players move their own characters to another of their accounts: `/transfercode <code>` on the old account
(valid for 24 hours), then `/takechar "<name>" <code>` on the new one, while the old account is offline.

## Game Mechanics

Under **Game mechanics** you switch these rules on or off for each server:

- **Kill messages:** who shot down whom, with the share of the hull damage, in the system or to everyone.
- **Death penalty** (off by default): a death costs a share of the value of ship and cargo, at most the
  credits; part of it goes to the shooter. It does not apply in the arena.
- **Bounties:** `/bounty` puts credits on a player; whoever shoots them down gets them.
- **Betting:** `/duel` is a fight for a stake, `/ffa` a free-for-all in the system. The winner takes all
  stakes; logging out or leaving the system loses.
- **Automatic purchase:** `/autobuy all on` restocks ammo, nanobots and shield batteries on docking, at the
  price in `goods.ini`. Base-specific prices and repairs are not covered.
- **Storage:** `/store`, `/unstore` and `/storage` keep cargo per account and base.
- **Tolls:** `/tax` demands credits from a player in the same system, `/pay` pays within a minute.
- **Arena** (off by default): `/arena` takes a player from a base to the arena base, `/return` back.
- **Wardrobe:** `/wardrobe list head` lists heads, `/wardrobe head <name>` or `body <name>` changes them. The
  player is disconnected for a moment, because the look is stored in the character file.

## Purchase Restrictions, Races And NPCs

- **Purchase restrictions:** under Game mechanics, a good or ship (by nickname, such as `li_elite` or
  `li_gun01_mark03`) can require a minimum rank or a minimum reputation with a faction. The server refuses
  the purchase and sends the player their real credits and equipment again; after a refused ship purchase it
  disconnects them for a moment, because the game client already shows the new ship. Administrators in the
  game are exempt.
- **Races:** an administrator flies the track and types `/race create <name>` at the start and
  `/race point <name>` at each checkpoint; the last one is the finish. Players join with `/race join <name>`
  in space in that system, `/race go` starts a ten-second countdown. The server samples positions once a
  second and works out when a ship passed a checkpoint in between. `/race top <name>` and the web page show
  the ten best times. `/race radius <name> <metres>` sets the size of the checkpoints (600 m by default).
- **NPCs:** `/npc spawn <ship> [count] [faction]` puts up to ten AI ships next to the administrator, as Nomads
  without a faction; `/npc list` and `/npc clear` list and remove them, the web page too. They fly with the
  game's default fighter behaviour and are gone after a server restart.

## Several Servers, Mods And Versions

- `FLAtlasServer.exe` is the administration. It starts each game server as its own process with its own data
  folder. The servers have no interface of their own and only answer the administration on this PC.
- A restart from the administration first warns the players and waits 60 seconds; grace period and message
  are set per server under **Settings > Restart**. While it waits, the button becomes Force restart, which restarts at once.
- The server of older versions is called `main` and keeps the folder `data`; new servers live in
  `data\instances\<id>`. A crashed server is restarted, at most three times in ten minutes.
- Each server can read its game data from its own Freelancer installation, for example a copy with a mod.
- The game only lists servers whose version matches its own `Freelancer.exe`. Mods use that, so each server
  can announce a game version such as `1.0.1223.11`.

## Data, Backups And Security

- Everything lives in `data`: admin logins and the server list under `manager`, and for each server its
  accounts under `Accts\MultiPlayer`, settings and logs under `service` and backups under `backups`. Keep the
  whole folder when backing up. It does not belong in Git.
- At every logout the server keeps a copy of the account, so a character or account can be reset to an
  earlier state. A daily backup is kept as well.
- The administration first only listens on this PC. It uses plain HTTP, so open it (address `0.0.0.0`) only in
  a trusted network, never to the internet. Passwords are stored as PBKDF2 hashes.
- The anti-cheat only reports at first. Switch a rule to blocking, kicking or banning once it hits no honest players.
- IP addresses are only shown to administrators.

## Building

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\build.ps1
.\bin\FLAtlasServer.exe --self-test report.json
```

`Build server.cmd` or `cargo build` does the same. The self-test starts the administration in a temporary
folder, creates servers and lets a test client play into space while it operates the web administration. It
covers 87 steps. Notes for developers and coding agents are in [AGENTS.md](AGENTS.md), the origin of all files
in [CREDITS.md](CREDITS.md).

| Folder | Contents |
| --- | --- |
| `src\FLService` | Administration, game server process, web server, commands, game mechanics, web interface |
| `src\Server\server_anticheat.cpp` | Anti-cheat and the service hooks inside the server |
| `src\Server`, `src\Common`, `src\Content` | Game logic from the decomp |
| `src\RemoteClient`, `src\DALib` | Network |
| `src\FLProbe` | Automatic test client |
| `upstream-docs` | Technical notes from the decomp project |

## Help And Community

Start with the [wiki](https://github.com/flathack/FLAtlas-FL-Server/wiki) and its [Troubleshooting](https://github.com/flathack/FLAtlas-FL-Server/wiki/Troubleshooting) page. Questions, bug reports and ideas are welcome on the FL Atlas Discord:

[![Join the FLAtlas Discord](https://img.shields.io/badge/FLAtlas-Discord%20Community-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/fY9qweRWGn)

Please say which server version, game version and mod you used, and what the server log shows.

## Notes

FLAtlas Server is based on [Freelancer Decomp](https://github.com/flathack/Freelancer-Decomp), released under
The Unlicense. The choice of commands follows FLHook by The Starport; no code was taken from it.

Freelancer is a game by Digital Anvil and Microsoft. FLAtlas Server is an independent community project and is
not affiliated with or endorsed by Microsoft or Digital Anvil. You need your own copy of the game.
