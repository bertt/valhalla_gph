# valhalla_gph

Sample [Valhalla](https://valhalla.github.io/valhalla/) routing graph tiles (`.gph`) and GeoJSON exports of them, for exploring the tile format.

## What are Valhalla tiles?

Valhalla is an open source routing engine. Instead of one big routing database it stores the road network as a set of binary **graph tiles** (`.gph`). Each tile holds the nodes (intersections), directed edges (road segments per travel direction), street names, shapes, speeds, elevation and turn restrictions for one area. A router only loads the tiles it needs.

Tiles live on a fixed latitude/longitude grid with three levels (0 = highways, 4° tiles; 1 = arterial roads, 1° tiles; 2 = local roads, 0.25° tiles). The tile id is `row * columns + col`, and the path is `<level>/<aaa>/<bbb>/<ccc>.gph`, for example `2/000/818/660.gph`.

## Specification

There is no standalone specification of the binary `.gph` format. Valhalla documents the tiling scheme and treats the C++ headers as the definition of the layout (little-endian structs that are memory-mapped as-is):

- [Tile Specifications](https://valhalla.github.io/valhalla/concepts/tiles/): hierarchy levels, tile sizes, ids and paths (official docs)
- [`graphtileheader.h`](https://github.com/valhalla/valhalla/blob/3.1.4/valhalla/baldr/graphtileheader.h): the 272-byte tile header and the offsets to all sections
- [`nodeinfo.h`](https://github.com/valhalla/valhalla/blob/3.1.4/valhalla/baldr/nodeinfo.h): intersections (32 bytes)
- [`directededge.h`](https://github.com/valhalla/valhalla/blob/3.1.4/valhalla/baldr/directededge.h): road segments per direction (48 bytes)
- [`edgeinfo.h`](https://github.com/valhalla/valhalla/blob/3.1.4/valhalla/baldr/edgeinfo.h): shape, names, OSM way id and elevation per segment
- [`graphtile.h`](https://github.com/valhalla/valhalla/blob/3.1.4/valhalla/baldr/graphtile.h): how the sections are laid out and read

The tiles here are version 3.1.4. Field-by-field notes, checked against these tiles, are in [ROUTING.md](https://github.com/bertt/WahooDesktop/blob/main/docs/ROUTING.md#graph-tile-format).

## What is in this repository

Three level-2 sample tiles around Utrecht (the Netherlands), copied from the storage of a Wahoo ELEMNT BOLT2 bike computer, which uses Valhalla for on-device routing. The tiles were decompressed from the device's `.gph.lzma` files.

| Tile | Area (0.25° x 0.25°) | Size |
|---|---|---|
| `utrecht/2/000/818/659.gph` | 4.75-5.00 E, 52.00-52.25 N (west of Utrecht) | 9.5 MB |
| `utrecht/2/000/818/660.gph` | 5.00-5.25 E, 52.00-52.25 N (Utrecht city) | 27.5 MB |
| `utrecht/2/000/818/661.gph` | 5.25-5.50 E, 52.00-52.25 N (east of Utrecht) | 16.9 MB |

The `utrecht` folder has Valhalla's directory layout, so it can be used as a `tile_dir` (`mjolnir.tile_dir`) in a Valhalla configuration.

## GeoJSON export

For each tile there is a GeoJSON file with one line per road segment (both directions of a road are merged): `utrecht/818659.geojson`, `utrecht/818660.geojson` and `utrecht/818661.geojson`. Properties include the street name, road class, use (road, cycleway, footway, ...), surface, speed, length, mean elevation, slopes, bridge/tunnel flags and the OSM way id. Open them in [QGIS](https://qgis.org/); the files are 25-73 MB, which is too large for most web viewers such as [geojson.io](https://geojson.io/).

The files were made with `wahoo_cli routing-export` from Wahoo Desktop:

```
wahoo_cli routing-export 818660
```

## Links

- [Wahoo Desktop](https://github.com/bertt/WahooDesktop), the app and CLI that made the exports
- [Routing tile format notes](https://github.com/bertt/WahooDesktop/blob/main/docs/ROUTING.md): header, tiling scheme, edge info and elevation, as found in these tiles
- [Valhalla](https://github.com/valhalla/valhalla) and its [documentation](https://valhalla.github.io/valhalla/)

## Notes

The tile data is derived from OpenStreetMap (© OpenStreetMap contributors, [ODbL](https://www.openstreetmap.org/copyright)). The tiles are shared here as samples for research and education; they are not an official release.
