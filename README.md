# d2-weapon-perk-scores

Weekly Destiny 2 weapon perk popularity. For each weapon, the file shows how often each perk
appears on the copies players finished activities with in the last 30 days. PvE and PvP are scored
separately, and copies from harder content count for more.

## Get the file

```bash
curl -fsSLO https://raw.githubusercontent.com/devinamar/d2-weapon-perk-scores/data/perk_scores.json
```

`raw.githubusercontent.com` allows cross-origin requests, so a browser app can fetch the file
directly. A new file replaces it every Monday from 08:30 UTC.

## Where the numbers come from

When an activity ends, its post-game carnage report (PGCR) lists who finished it and which weapons
got kills. Minutes later each of those players' profiles is read, which shows the copy they have
equipped and its perks.

Each kept copy counts as a vote, weighted by the activity:

| Category | Side | Weight | Hard |
| --- | --- | --- | --- |
| Master raid (Epic for The Desert Perpetual), or any raid run with Cutthroat Combat (Pantheon too) | PvE | 32 | yes |
| Grandmaster Vanguard Alert (the weekly fixed-difficulty Grandmaster strikes) | PvE | 16 | yes |
| Master dungeon | PvE | 8 | yes |
| Raid | PvE | 4 | |
| Dungeon | PvE | 2 | |
| Strikes and Portal (missions and Solo Ops at any tier among them) | PvE | 1 | |
| Trials of Osiris | PvP | 8 | yes |
| Competitive | PvP | 4 | yes |
| Regular playlist (Quickplay, Iron Banner and other public PvP) | PvP | 2 | |
| Gambit | PvP | 1 | |

Hard and normal content are scored apart. The two are then merged according to how well each one is
measured. The policy each file was scored under is in its `settings` and `categories`.

Only aggregates are published. The file holds no player names, membership IDs or item instance IDs.

## Format

```json
{
  "schema_version": 1,
  "generated_at": "2026-10-26T08:31:00Z",
  "settings": {"window_days": 30, "rescue_copies": 100, "raw_days": 14, "full_clear_weight": 0.75, "late_join_seconds": 30, "hard_weight": 0.75, "credibility_k": 30},
  "categories": [{"key": "raid_master", "label": "Master raid", "side": "pve", "weight": 32, "hard": true}],
  "pve": [],
  "pvp": [
    {"name": "Found Verdict (Episode: Heresy)", "type": "Shotgun", "item_hash": 694500607, "rank": 48,
     "players": 157, "active_players": 157, "family": "Found Verdict",
     "item_hashes": [694500607, 851296754], "hard": true, "hard_weight": 0.699,
     "groups": {"hard": {"copies": 47, "n_eff": 46.5}, "normal": {"copies": 113, "n_eff": 109.9}},
     "last_seen": "2026-10-23T22:13:09Z", "stale": false,
     "columns": [
       {"kind": "magazine", "n_eff": 88.3, "credibility": 0.75, "confidence": "medium",
        "hard": {"n_eff": 46.5, "credibility": 0.61}, "normal": {"n_eff": 109.9, "credibility": 0.79},
        "perks": [
          {"perk": "Accurized Rounds", "share": 0.786, "low": 0.69, "high": 0.859, "hard": 0.815, "normal": 0.718},
          {"perk": "Assault Mag", "share": 0.082, "low": 0.041, "high": 0.159, "hard": 0.065, "normal": 0.122}]}]}
  ]
}
```

- **Weapons and versions:**
  - Weapons are listed in rank order within each side (`pve`, `pvp`).
  - `family` is the name a weapon's versions share.
  - `item_hashes` lists the versions (Bungie item hashes) scored as this weapon, and `item_hash` is
    the version most of its copies are.
- **Counts and weights:**
  - `players` counts copies.
  - `groups` gives the hard and normal groups' copies and effective votes (`n_eff`).
  - `hard_weight` is hard content's share of the weapon's scores.
- **Columns:**
  - Each column is a perk socket, in socket order.
  - The trait pair (`traits`) comes before each trait on its own (`trait1`, `trait2`).
  - A column's `hard` or `normal` is null when that group has no votes there.
  - A fixed-roll weapon (most exotics) has no columns.
- **Perks:**
  - `share` is how often the perk appears in its column, and `low`/`high` bound it (Wilson
    interval).
  - `hard` and `normal` are the perk's shares in each group, which merge into `share`.

## Versioning

`schema_version` goes up when a key is renamed, removed or changes meaning. New keys can appear
without it changing. Check it before reading the file.

## Notes

This project isn't affiliated with or endorsed by Bungie. The data comes from the Bungie.net API.
