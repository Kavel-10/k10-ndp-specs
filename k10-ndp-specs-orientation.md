# AT/Orientation Data Delivery Specification

### Revision History

| Version | Date | Author | Description |
|---------|------------|--------|-------------|
| 0.1.0 | 2026-08-17 | Kavel10 | Initial draft |

---

## 1. Scope

This document defines the delivery requirements for the AT/Orientation Data product.

The AT/Orientation Data product contains the results of the Aerial Triangulation (AT) block adjustment: the exterior orientation of every image in the block, together with the accompanying statistics and reporting that document the quality of the adjustment. It is the reference orientation dataset used to produce the DSM and DSMOrtho products, and is delivered so that image position and attitude, image-to-name linkage, and adjustment quality can be independently verified downstream.

The AT/Orientation Data is generated in Vexcel Ultramap from the final, adjusted aerial block.

---

## 2. Conformance

An AT/Orientation Data delivery conforms to this specification if all mandatory requirements defined in this document are met.

A delivery shall only be produced once the AT run is final, "Statistics" was enabled during the last and final block adjustment, and all outputs listed in this document are fully generated and available. If any of these conditions is not met, the AT shall be re-run and/or re-exported before a delivery is produced.

---

## 3. Delivery Method

| Property | Requirement |
|---|---|
| Delivery method | S3-compatible object storage |
| Access protocol | HTTPS / S3 API |
| Structure | Per product, per block, per AT type |

---

## 4. AT Types

| AT Type | Requirement |
|---|---|
| `COMBINED` | The final AT is valid for both Nadir and Oblique combined. There is only one AT for the whole block. |
| `NADIR` | The final AT is only valid for the Nadir data. Files may contain Oblique entries, but these are not valid. |
| `OBLIQUE` | The final AT is only valid for the Oblique data. Files may contain Nadir entries. |

Each delivery is produced for a single AT type, reflecting the scope of the underlying block adjustment.

---

## 5. Delivery Structure

```text
Orientation/
└── <BlockName>/
    └── <AT_TYPE>/
        ├── ATReport.pdf
        ├── ATReport-Overview.csv
        ├── ATReport-Session#.csv
        ├── WorldPointStatistics.csv
        ├── ImageMeaStatistics.csv
        ├── GpsImuStatistics.csv
        ├── <ProjectName>.eo
        └── <ProjectName>_naming.txt
```

`<AT_TYPE>` shall be one of `COMBINED`, `NADIR` or `OBLIQUE`, as defined in Section 4. Only the files listed above shall be included in the delivery folder; no intermediate, outdated, or otherwise unrelated files shall be added.

---

## 6. Required Files

| File | Description | Source |
|---|---|---|
| `ATReport.pdf` | Formatted report of the aerial triangulation adjustment. | Ultramap "Create AT Report" |
| `ATReport-Overview.csv` | Overview statistics of the block adjustment. | Ultramap "Create AT Report" |
| `ATReport-Session#.csv` | Per-session statistics of the block adjustment. | Ultramap "Create AT Report" |
| `WorldPointStatistics.csv` | Statistics of the tie/control points in object space. | Ultramap "Create AT Report" |
| `ImageMeaStatistics.csv` | Statistics of the image measurements. | Ultramap "Create AT Report" |
| `GpsImuStatistics.csv` | Statistics of the GPS/IMU observations used in the adjustment. | Ultramap "Create AT Report" |
| `<ProjectName>.eo` | Exterior orientation of the adjusted, final block. Any filename is acceptable provided the extension is `.eo`. | Ultramap "Export Exterior Orientation" |
| `<ProjectName>_naming.txt` | Links internal Ultramap image IDs to delivered image names. Filename shall contain "naming" (case-insensitive). | Ultramap Studio "Edit Image Naming" |

The `ATReport.pdf` and statistics files (`ATReport-Overview.csv`, `ATReport-Session#.csv`, `WorldPointStatistics.csv`, `ImageMeaStatistics.csv`, `GpsImuStatistics.csv`) shall only be generated with Statistics enabled, shall not be renamed, and their content shall not be edited.

The `.eo` file shall be exported from the adjusted, final block, and its content shall not be changed. If filtering is required for downstream use, this shall be done on a copy of the file, not on the delivered file.

The naming file shall be exported from Ultramap Studio, and the image names it contains shall not be renamed or edited.

---

## 7. Technical Summary

| Property | Requirement |
|---|---|
| Product | AT/Orientation Data |
| Delivery | S3-compatible object storage |
| Structure | Per product, per block, per AT type |
| AT types | `COMBINED`, `NADIR`, `OBLIQUE` |
| Source system | Vexcel Ultramap |
| Report | `ATReport.pdf` |
| Statistics | `ATReport-Overview.csv`, `ATReport-Session#.csv`, `WorldPointStatistics.csv`, `ImageMeaStatistics.csv`, `GpsImuStatistics.csv` |
| Exterior orientation | `.eo` file, from adjusted final block |
| Image naming linkage | Naming file (filename contains "naming") |
| File integrity | No renaming or editing of statistics, `.eo` content, or image names |
