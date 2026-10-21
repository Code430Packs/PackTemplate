# Regions

One file per district: the importer's inputs for `packs/<district>/`. See the game's
`regions/README.md` for every field. For a city repository:

- **Pin the city's origin** in every district: `"origin": {"lat": ..., "lon": ...}`, the same
  for all of them, so their coordinates line up and stories survive re-exports.
- **Give each district a border** on the chunk grid of that origin, `"border": {"chunks": [x0,
  z0, x1, z1]}` (256 m chunks, x east, z south), instead of a bbox. Districts must not overlap;
  the importer refuses one that does.

```json
{
  "name": "glebe",
  "origin": {"lat": 45.4200027, "lon": -75.6950007},
  "border": {"chunks": [-6, 6, 6, 14]},
  "pack_id": "code430.ottawa-glebe",
  "pack_name": "Ottawa Glebe",
  "dem": "hrdem"
}
```
