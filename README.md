# Code 430 pack template

Start a new pack for [Code 430](https://github.com/Code430Game/Code430Game) from this
repository: **Use this template** on GitHub, then follow the part for your kind of pack.

| Kind | Repository name | Topic | What it holds |
|---|---|---|---|
| Location | `Location-<City><Subdivision><Country>`, ISO 3166 codes: `Location-OttawaOnCa`, `Location-PortlandOrUs` | `code430-location` | A city: one map pack per district under `packs/`, built from OpenStreetMap |
| Story | `Story-<Name>` | `code430-story` | Missions, dialogue, gangs and their turf, and placements on a map |
| Ads | `Ads-<Name>` | `code430-ads` | Billboards |
| Characters | `Characters-<Name>` | `code430-characters` | Palettes and presets |

Check the name is free on GitHub before you take it, and add the topic so others can find the
pack.

## Layout
```
packs/<folder>/        one pack each (pack.json and its files)
regions/<folder>.json  location packs: the importer's inputs for packs/<folder>/
code430.json           the game commit this repository validates against
LICENSE.md             the pack's licence (ODbL 1.0 for OpenStreetMap data)
.github/workflows/pack.yml
```

## A location (a city)
A location repository is a city, and each district is its own pack: Centretown, then the
Glebe beside it. Districts share the city's pinned origin and sit on its 256 m chunk grid
without overlapping, so they can be played together later (see `regions/README.md`).

Build a district from a checkout of the game at the commit in `code430.json`:

```
cd <game>/tools
uv run python -m osm_import --region <this repo>/regions/<district>.json \
  --pack-out <this repo>/packs/<district> --pack-version 0.1.0 --build-stamp
```

Re-exports are deterministic, so git stores only what changed. The validator warns when a file
passes 40 MB and fails at 90 MB (GitHub refuses files over 100 MB): split the district before
that.

## A story
A story depends on a map (`"dependencies": [{"id": "code430.ottawa-centretown", "version":
"^0.2"}]`) and never copies or edits it. It may replace the map's ads by id, and places
characters, items and cars at latitude and longitude (`placements.v0.json`, coming in the game soon), so it survives the map's re-exports.

**Privacy.** Don't publish real people's names or where they live. Put characters near a park,
a shop or a landmark instead. A personal story with friends' homes is for sharing offline: a
folder or a private repository installs the same way.

## Versions
Bump `version` in `pack.json` for every release that changes the pack:

- **major:** an id is removed or renamed, the map origin moves, or `engine_api` changes;
- **minor:** new ids, nothing removed;
- **patch:** other changes.

While a pack is `0.x`, the minor number counts as the major one (`0.2.x` to `0.3.0` for a
breaking change, `0.2.0` to `0.2.1` otherwise). CI says which bump a change needs.

## Checks and releases
On every push and pull request, CI validates each pack with the game's pack tools and checks
its version against the pack's last release. To release a pack, tag `<folder>-v<version>`:

```
git tag centretown-v0.2.0 && git push origin centretown-v0.2.0
```

CI then publishes `centretown-v0.2.0.zip` and its sha256, which the game's lock file and
`scripts/install-pack.sh` use. Run the same checks locally from the game checkout:

```
cd <game>/tools
uv run python -m pack_tools <this repo>/packs
uv run python -m pack_tools version check <this repo>/packs/<folder> --ref <folder>-v<last>
```

CI checks out the public game repository at the pinned commit, so pull requests from forks run
the same checks.
