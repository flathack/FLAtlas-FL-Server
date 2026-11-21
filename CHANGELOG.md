# Changelog

All notable changes to FLAtlas Server. Each release has one section `## v<version> - <date>`;
`tools/make-release.ps1` takes that section as the notes of the GitHub release, and the web
administration shows this file under **Updates > Changelogs**.

## v0.2.0 - 2026-11-21

### Added
- Restart with a grace period: a restart waits 60 seconds by default and warns the players with a
  message. Both are set per server under **Settings > Restart**; `{seconds}` in the message becomes
  the seconds left, and a reminder goes out ten seconds before. Without players online the server
  restarts at once. While it waits, the button becomes Force restart, which restarts at once.
- **Updates > Changelogs** shows this changelog in the web administration.
- Linux release: a package that runs the Windows server under Wine, with start, connect and
  systemd scripts. Ctrl+C and `systemctl stop` stop all servers in order. Under Wine the
  built-in update is not available; update by hand with the new Linux package (see
  `docs/RELEASING.md`). The service installer warns when the service would share the user's
  `~/.wine`; a Wine prefix of its own is recommended.

### Changed
- English is the default language everywhere: server messages, logs, scripts and everything in
  the game. Only the web administration can be switched to German.
- The setting for the language of command answers in the game is gone; the game always answers
  in English.
- The start scripts have English names: `Start server.cmd`, `Connect game.cmd`,
  `Install service.cmd`, `Remove service.cmd` and `Build server.cmd`. The new version removes the
  old German-named files at its first start.

### Fixed
- The server hung at full load when a single network message was larger than 1 KB, e.g. the
  character list after creating a character with a fully equipped ship in a mod.
- The server crashed when a player entered a base whose mission offers involve a faction that
  lists an npc ship missing from the npc ship files (seen with mods).
- `/setcash` on online players, lost duel and free-for-all stakes on a server stop, payouts above
  the credit limit, and several restore and restart issues found in two code reviews.

## v0.1.0 - 2026-11-19

### Added
- First release: a dedicated server for Freelancer built from the Freelancer Decomp source (1.1),
  with a web administration that runs several game servers on one PC, as a Windows service or in
  a console.
- Players, accounts, characters, backups and restoring earlier states, new player profile,
  anti-cheat, change history, admin logins and team messages.
- Universe and system map with drag-and-drop teleport.
- Chat commands for players, moderators and administrators, game roles and connection limits.
- Game mechanics: kills, death penalty, bounties, duels, free-for-all, automatic purchase,
  storage, tolls, arena, wardrobe, purchase restrictions, races and NPC spawning.
- Update check and installation from GitHub releases.
