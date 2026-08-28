# DSMOrtho Delivery Specification

### Revision History

| Version | Date | Author | Description |
|---------|------------|--------|-------------|
| 0.1.0 | 2026-07-01 | Kavel10 | Initial draft |
| 0.9.0   | 2026-06-28 | Kavel10 | Technical review           |
| 1.0.0   | 2026-06-29 | Kavel10 | First official release     |
| 1.1.0 | 2026-08-17 | Kavel10 | Added file naming, corrected delivery structure |
---

## 1. Scope

This document defines the delivery requirements for the DSMOrtho product.

The DSMOrtho product is a true orthophoto generated from a Digital Surface Model (DSM). Every pixel is orthorectified using the DSM surface and therefore represents the visible upper surface of the Earth, including buildings, vegetation, infrastructure and other above-ground objects.

The product is intended for visualization, interpretation and GIS analysis.

---

## 2. Conformance

A DSMOrtho delivery conforms to this specification if all mandatory requirements defined in this document are met.

---

## 3. Delivery Method

| Property | Requirement |
|---|---|
| Delivery method | S3-compatible object storage |
| Access protocol | HTTPS / S3 API |
| Structure | Per product and per subarea |
---

## 4. Naming & Delivery Structure

Tile filenames are generated automatically, containing the coordinate of the tile's lower-left corner (e.g. `X129600_Y457200`).

| Property | Requirement                                                                                   |
|---|-----------------------------------------------------------------------------------------------|
| Filename pattern | `<ProductType>-X<X>_Y<Y>-<Resolution>cm.tif`                                                  |
| Example | `DSMOrtho-X129600_Y457200-3cm.tif`                                                            |
| `<ProductType>` | `DSMOrtho`                                                                                    |
| `X<X>_Y<Y>` | Lower-left corner coordinate of the tile |
| `<Resolution>` | Ground Sampling Distance in centimetres                                                       |

Data will be delivered according to the folliwing folder structure:
```text
DSMOrtho/
├── Area_01/
│   ├── TileIndex.gpkg
│   └── Tiles/
│       ├── DSMOrtho-X178800_Y511800-3cm.tif
│       ├── DSMOrtho-X179000_Y511800-3cm.tif
│       └── ...
└── Area_02/
    ├── TileIndex.gpkg
    └── Tiles/
        └── ...
```

---

## 5. Raster Requirements

| Property              | Requirement              |
|-----------------------|--------------------------|
| Raster type           | Orthorectified RGB image |
| Number of bands       | 4 (RGBA)                 |
| Sample format         | Unsigned Integer         |
| Data type             | UInt8                    |
| Bits per sample       | 8                        |
| Total colour depth    | 24 bit                   |
| Pixel reference       | Cell centre              |
| NoData value          | 0                        |
| NoData interpretation | Black (0,0,0)            |
| Alpha Mask            | Included                 |

Each pixel shall represent the (true) orthorectified radiometric value at the centre of the pixel.

NoData pixels (RGB=0,0,0) can occur inside the valid image extent.

---

## 6. Orthorectification

The DSMOrtho shall be generated using the corresponding Digital Surface Model (DSM).

Orthorectification shall use the upper visible surface represented by the DSM.

The product shall therefore correctly represent:

- terrain;
- buildings;
- vegetation;
- bridges;
- infrastructure;
- other above-ground objects.

Objects shall appear in their true planimetric position.

---

## 7. Spatial Resolution

| Product | Ground Sampling Distance |
|---|---:|
| DSMOrtho | 0.02 m or 0.03 m |

Pixels shall be square.

The pixel size in the X and Y directions shall equal the specified Ground Sampling Distance.

---

## 8. Tiling

| Property | Requirement |
|---|---|
| Tile size | 600 m × 600 m |
| Tile grid | CRS tiling grid * |
| Tile overlap | Not allowed |
| Gaps between tiles | Not allowed |

\* The DSMOrtho shall be partitioned into square tiles measuring 600 m × 600 m. Tile boundaries shall be aligned to the coordinate reference system such that each tile boundary coincides with an integer multiple of the tile size. Adjacent tiles shall share common boundaries without overlap or gaps.

---

## 9. File Format

| Property | Requirement |
|---|---|
| Format | Cloud Optimized GeoTIFF |
| File extension | `.tif` |
| Internal tiling | Required |
| Internal block size | 512 × 512 pixels |
| BigTIFF | Automatic when required |

Each DSMOrtho tile shall be encoded as a Cloud Optimized GeoTIFF.

---

## 10. Compression

| Property | Requirement |
|---|-------------|
| Compression type | Lossy       |
| Compression | JPEG        |
| JPEG quality | 85 %        |
| Photometric interpretation | RGB         |

JPEG compression is used as it provides an efficient balance between storage size and visual quality for orthophotography.
(An uncompressed orthophotomosaic for The Netherlands would be ~140TB, with JPEG compression this is reduced to ~35TB)
---

## 11. Radiometric Requirements

| Property | Requirement                 |
|---|-----------------------------|
| Colour space | RGB                         |
| Colour depth | 8 bit per channel           |
| Histogram | Continuous without clipping |
| Colour balancing | Required within subblocks   |
| Visible seams | Not permitted               |

Adjacent subblocks shall exhibit consistent radiometric appearance.

Colour balancing shall avoid abrupt transitions between neighbouring tiles within a subblock.

---

## 12. Tile Index

Each delivery shall include a tile index.

The tile index shall contain one feature for each delivered DSMOrtho tile.

| Attribute | Description |
|---|---|
| `TileID` | Unique tile identifier |
| `FileName` | Raster file name |
| `RelativePath` | Relative path to the raster file |
| `Geometry` | Tile footprint polygon |

The tile index shall be delivered as GeoPackage (`.gpkg`).

---

## 13. Technical Summary

| Property | Requirement                  |
|---|------------------------------|
| Product | DSMOrtho                     |
| Delivery | S3-compatible object storage |
| Structure | Per product and per subarea  |
| Tile index | Required                     |
| Tile index format | GeoPackage                   |
| Tile size | 600 m × 600 m                |
| Resolution | 0.02 m or 0.03 m             |
| Format | Cloud Optimized GeoTIFF      |
| Bands | RGB                          |
| Data type | UInt8                        |
| Bits per sample | 8                            |
| Colour depth | 24 bit                       |
| Compression | JPEG (85%)                   |
| Block size | 512 × 512 pixels             |
| NoData | RGB (0,0,0)                  |