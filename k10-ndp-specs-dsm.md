# DSM Delivery Specification
### Revision History
| Version | Date       | Author | Description                |
|---------|------------|--------|----------------------------|
| 0.1.0   | 2026-06-20 | Kavel10 | Initial draft              |
| 0.9.0   | 2026-06-28 | Kavel10 | Technical review           |
| 1.0.0   | 2026-06-29 | Kavel10 | First official release     |
| 1.1.0 | 2026-08-17 | Kavel10 | Added file naming, corrected delivery structure |
---
## 1. Scope

This document defines the delivery requirements for the Digital Surface Model (DSM) product.

The DSM product represents the elevation of the visible surface, including terrain, buildings, vegetation, infrastructure, and other above-ground objects.

## 2. Conformance

A DSM delivery conforms to this specification if all mandatory requirements in this document are met.

## 3. Delivery Method

| Property | Requirement |
|---|---|
| Delivery method | S3-compatible object storage |
| Access protocol | HTTPS / S3 API |
| Structure | Per product and per subarea |

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
DSM/
├── Area_01/
│   ├── TileIndex.gpkg
│   └── Tiles/
│       ├── DSM-X178800_Y511800-3cm.tif
│       ├── DSM-X179000_Y511800-3cm.tif
│       └── ...
└── Area_02/
    ├── TileIndex.gpkg
    └── Tiles/
        └── ...
```

## 5. Raster Requirements

| Property | Requirement |
|---|---|
| Raster type | Single-band elevation raster |
| Number of bands | 1 |
| Sample format | IEEE 754 Floating Point |
| Data type | Float32 |
| Bits per sample | 32 |
| Unit | Metres |
| Pixel reference | Cell centre |
| NoData value | -9999.0 |

Each pixel value shall represent the elevation of the visible surface at the centre of the pixel.

The NoData value shall only be used where no valid elevation value is available.

## 6. Surface Representation

The DSM shall represent the uppermost visible surface. The DSM has been created using Dense Image Matching techniques within Vexcel Ultramap software. 

The DSM shall include, where present:

- terrain;
- buildings;
- vegetation;
- infrastructure;
- other above-ground objects.

The treatment of water bodies shall be defined per project or product variant.

## 8. Tiling

| Property | Requirement |
|---|---|
| Tile size | 600 m × 600 m |
| Tile grid | CRS tiling grid * |
| Tile overlap | Not allowed |
| Gaps between tiles | Not allowed |



\* The DSM shall be partitioned into square tiles measuring 600 m × 600 m. Tile boundaries shall be aligned to the coordinate reference system such that each tile boundary coincides with an integer multiple of the tile size. Adjacent tiles shall share common boundaries without overlap or gaps. 
## 7. Spatial Resolution

| Product | Ground Sampling Distance |
|---|---:|
| DSM | 0.02 / 0.03 m |


Pixels shall be square.

The pixel size in X and Y direction shall equal the specified Ground Sampling Distance.

## 9. File Format

| Property | Requirement |
|---|---|
| Format | Cloud Optimized GeoTIFF |
| File extension | `.tif` |
| Internal tiling | Required |
| Internal block size | 512 × 512 pixels |
| BigTIFF | Automatic when required |

Each DSM tile shall be encoded as a Cloud Optimized GeoTIFF.

## 10. Compression

| Property | Requirement |
|---|---|
| Compression type | Lossless |
| Supported compression | DEFLATE or ZSTD |
| Predictor | 3 for Float32 data |

Lossy compression methods shall not be used for DSM elevation data.

## 11. Tile Index

Each delivery shall include a tile index.

The tile index shall contain one feature for each delivered DSM tile.

| Attribute | Description |
|---|---|
| `TileID` | Unique tile identifier |
| `FileName` | Raster file name |
| `RelativePath` | Relative path to the raster file |
| `Geometry` | Tile footprint polygon |

The tile index shall be delivered as GeoPackage (`.gpkg`).

## 12. Technical Summary

| Property | Requirement |
|---|---|
| Product | Digital Surface Model |
| Delivery | S3-compatible object storage |
| Structure | Per product and per subarea |
| Tile index | Required |
| Tile index format | GeoPackage |
| Tile size | 600 m × 600 m |
| Resolution | 0.02 m or 0.03 m |
| Format | Cloud Optimized GeoTIFF |
| Raster type | Single band |
| Data type | Float32 |
| Bits per sample | 32 |
| NoData | -9999.0 |
| Compression | DEFLATE or ZSTD |
| Block size | 512 × 512 pixels |
