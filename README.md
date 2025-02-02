# Rio-Tiler-PDS: A rio-tiler plugin for Public Datasets

<p align="center">
  <img src="https://user-images.githubusercontent.com/10407788/91102350-ffa75400-e636-11ea-8374-3450a72745c9.png" style="max-width: 800px;" alt="rio-tiler-pds"></a>
</p>
<p align="center">
  <em>A rio-tiler plugin to read from publicly-available datasets.</em>
</p>
<p align="center">
  <a href="https://github.com/cogeotiff/rio-tiler-pds/actions?query=workflow%3ACI" target="_blank">
      <img src="https://github.com/cogeotiff/rio-tiler-pds/workflows/CI/badge.svg" alt="Test">
  </a>
  <a href="https://codecov.io/gh/cogeotiff/rio-tiler-pds" target="_blank">
      <img src="https://codecov.io/gh/cogeotiff/rio-tiler-pds/branch/master/graph/badge.svg" alt="Coverage">
  </a>
  <a href="https://pypi.org/project/rio-tiler-pds" target="_blank">
      <img src="https://img.shields.io/pypi/v/rio-tiler-pds?color=%2334D058&label=pypi%20package" alt="Package version">
  </a>
  <a href="https://pypistats.org/packages/rio-tiler-pds" target="_blank">
      <img src="https://img.shields.io/pypi/dm/rio-tiler-pds.svg" alt="Downloads">
  </a>
  <a href="https://github.com/cogeotiff/rio-tiler-pds/blob/master/LICENSE.txt" target="_blank">
      <img src="https://img.shields.io/github/license/cogeotiff/rio-tiler-pds.svg" alt="Lincense">
  </a>
</p>

**Important** This is the new module for rio-tiler missions specific (ref: https://github.com/cogeotiff/rio-tiler/issues/195)

---

**Documentation**: <a href="https://cogeotiff.github.io/rio-tiler-pds/" target="_blank">https://cogeotiff.github.io/rio-tiler-pds/</a>

**Source Code**: <a href="https://github.com/cogeotiff/rio-tiler-pds" target="_blank">https://github.com/cogeotiff/rio-tiler-pds</a>

---

## Installation

You can install rio-tiler-pds using pip

```bash
$ pip install -U pip
$ pip install rio-tiler-pds
```

or install from source:

```bash
$ pip install -U pip
$ pip install git+https://github.com/cogeotiff/rio-tiler-pds.git
```

## Datasets

| Data                                      | Level/Product                               | Format                     | Owner                      | Region       | Bucket Type        |
| ----------------------------------------- | ------------------------------------------- | -------------------------- | -------------------------- | ------------ | ------------------ |
| [Sentinel 2][s2_l1c_jp2]                  | L1C                                         | JPEG2000                   | Sinergise / AWS            | eu-central-1 | **Requester-pays** |
| [Sentinel 2][s2_l2a_jp2]                  | L2A                                         | JPEG2000                   | Sinergise / AWS            | eu-central-1 | **Requester-pays** |
| [Sentinel 2][s2_l2a_cog]                  | L2A                                         | COG                        | Digital Earth Africa / AWS | us-west-2    | Public             |
| [Sentinel 1][s1_l1c_cog]                  | L1C                                         | COG (Internal GCPS)        | Sinergise / AWS            | eu-central-1 | **Requester-pays** |
| [Landsat 8*][l8_l1_cog]                   | L1                                          | GTiff (External Overviews) | Planet / AWS               | us-west-2    | Public             |
| [Landsat Collection 2][landsat_c2_cog]    | L1,L2                                       | COG                        | USGS / AWS                 | us-west-2    | **Requester-pays** |
| [CBERS 4/4A][cbers_cog]                   | L2/L4                                       | COG                        | AMS Kepler / AWS           | us-east-1    | **Requester-pays** |
| [MODIS (modis-pds)][modis_pds]            | MCD43A4, MOD09GQ, MYD09GQ, MOD09GA, MYD09GA | GTiff (External Overviews) | -                          | us-west-2    | Public             |
| [MODIS (astraea-opendata)][modis_astraea] | MCD43A4, MOD11A1, MOD13A1, MYD11A1 MYD13A1  | COG                        | Astraea / AWS              | us-west-2    | **Requester-pays** |

[s2_l1c_jp2]: https://registry.opendata.aws/sentinel-2/
[s2_l2a_jp2]: https://registry.opendata.aws/sentinel-2/
[s2_l2a_cog]: https://registry.opendata.aws/sentinel-2-l2a-cogs/
[s1_l1c_cog]: https://registry.opendata.aws/sentinel-1/
[l8_l1_cog]: https://registry.opendata.aws/landsat-8/
[landsat_c2_cog]: https://www.usgs.gov/core-science-systems/nli/landsat/landsat-commercial-cloud-data-access
[cbers_cog]: https://registry.opendata.aws/cbers/
[modis_pds]: https://docs.opendata.aws/modis-pds/readme.html
[modis_astraea]: https://registry.opendata.aws/modis-astraea/

`*` Landsat 8 Collection 1 reader has been deprecated because the `landsat-pds` will be deleted on July 1st 2021. For new applications, using Collection 2 is suggested.

**Adding more dataset**:

If you know of another publicly-available dataset that can easily be described
with a "scene id", please feel free to [open an
issue](https://github.com/cogeotiff/rio-tiler-pds/issues/new).

## Warnings

#### Requester-pays Buckets

On AWS, `sentinel2`, `sentinel1`, `cbers` and `modis` (in astraea-opendata) datasets are stored in [_requester
pays_](https://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html)
buckets. This means that the cost of GET and LIST requests and egress fees for
downloading files outside the AWS region will be charged to the _accessing
users_, not the organization hosting the bucket. For `rio-tiler` and
`rio-tiler-pds` to work with such buckets, you'll need to set
`AWS_REQUEST_PAYER="requester"` in your shell environment.

#### Partial reading on Cloud hosted dataset

When reading data, `rio-tiler-pds` performs _partial_ reads when possible. Hence
performance will be best on data stored as [Cloud Optimized GeoTIFF
(COG)](http://cogeo.org). It's important to note that **Sentinel-2 scenes hosted
on AWS are not in Cloud Optimized format but in JPEG2000**. Partial reads from
JPEG2000 files are inefficient, and GDAL (the library underlying `rio-tiler-pds`
and `rasterio`) will need to make **many GET requests** and transfer a lot of
data. This will be both slow and expensive, since AWS's JPEG2000 collection of
Sentinel 2 data is stored in a requester pays bucket.

Ref: [Do you really want people using your data](https://medium.com/@_VincentS_/do-you-really-want-people-using-your-data-ec94cd94dc3f) blog post.

## Overview

### Readers

Each dataset has its own submodule (e.g sentinel2: `rio_tiler_pds.sentinel.aws`)

```python
from rio_tiler_pds.landsat.aws import L8Reader, LandsatC2Reader
from rio_tiler_pds.sentinel.aws import S1L1CReader
from rio_tiler_pds.sentinel.aws import (
    S2JP2Reader,  # JPEG2000
    S2COGReader,   # COG
)

from rio_tiler_pds.cbers.aws import CBERSReader
from rio_tiler_pds.modis.aws import MODISPDSReader, MODISASTRAEAReader
```

All Readers are subclass of [`rio_tiler.io.BaseReader`](https://github.com/cogeotiff/rio-tiler/blob/f917d0eaf27f8644f3bb18856a63fe45eeb4a2ef/rio_tiler/io/base.py#L17) and inherit its properties/methods.

#### Properties
- **bounds**: Scene bounding box
- **crs**: CRS of the bounding box
- **geographic_bounds**: bounding box in geographic projection (e.g WGS84)
- **minzoom**: WebMercator MinZoom (e.g 7 for Landsat8)
- **maxzoom**: WebMercator MaxZoom (e.g 12 for Landsat8)

#### Methods

- **info**: Returns band's simple info (e.g nodata, band_descriptions, ....)
- **statistics**: Returns band's statistics (percentile, histogram, ...)
- **tile**: Read web mercator map tile from bands
- **part**: Extract part of bands
- **preview**: Returns a low resolution preview from bands
- **point**: Returns band's pixel value for a given lon,lat
- **feature**: Extract part of bands

#### Other
- **bands** (property): List of available bands for each dataset

### Scene ID

All readers take scene id as main input. The **scene id** is used internaly by the reader to derive the full path of the data.

e.g: Landsat on AWS

Because the Landsat AWS PDS follows a regular schema to store the data (`s3://{bucket}/c1/L8/{path}/{row}/{scene}/{scene}_{band}.TIF"`), we can easily reconstruct the full band's path by parsing the scene id.

```python
from rio_tiler_pds.landsat.aws import L8Reader
from rio_tiler_pds.landsat.utils import sceneid_parser

sceneid_parser("LC08_L1TP_016037_20170813_20170814_01_RT")

> {
  'sensor': 'C',
  'satellite': '08',
  'processingCorrectionLevel': 'L1TP',
  'path': '016',
  'row': '037',
  'acquisitionYear': '2017',
  'acquisitionMonth': '08',
  'acquisitionDay': '13',
  'processingYear': '2017',
  'processingMonth': '08',
  'processingDay': '14',
  'collectionNumber': '01',
  'collectionCategory': 'RT',
  'scene': 'LC08_L1TP_016037_20170813_20170814_01_RT',
  'date': '2017-08-13'
}

with L8Reader("LC08_L1TP_016037_20170813_20170814_01_RT") as landsat:
    print(landsat._get_band_url("B1"))

> s3://landsat-pds/c1/L8/016/037/LC08_L1TP_016037_20170813_20170814_01_RT/LC08_L1TP_016037_20170813_20170814_01_RT_B1.TIF
```

Each dataset has a specific scene id format:

!!! note "Scene ID formats"

    - Landsat
        - link: [rio_tiler_pds.landsat.utils.sceneid_parser](https://github.com/cogeotiff/rio-tiler-pds/blob/e4421d3cf7c23b7b3552b8bb16ee5913a5483caf/rio_tiler_pds/landsat/utils.py#L35-L56)
        - regex: `^L[COTEM]0[0-9]_L\d{1}[A-Z]{2}_\d{6}_\d{8}_\d{8}_\d{2}_(T1|T2|RT)$`
        - example: `LC08_L1TP_016037_20170813_20170814_01_RT`

    - Sentinel 1 L1C
        - link: [rio_tiler_pds.sentinel.utils.s1_sceneid_parser](https://github.com/cogeotiff/rio-tiler-pds/blob/e4421d3cf7c23b7b3552b8bb16ee5913a5483caf/rio_tiler_pds/sentinel/utils.py#L98-L121)
        - regex: `^S1[AB]_(IW|EW)_[A-Z]{3}[FHM]_[0-9][SA][A-Z]{2}_[0-9]{8}T[0-9]{6}_[0-9]{8}T[0-9]{6}_[0-9A-Z]{6}_[0-9A-Z]{6}_[0-9A-Z]{4}$`
        - example: `S1A_IW_GRDH_1SDV_20180716T004042_20180716T004107_022812_02792A_FD5B`

    - Sentinel 2 JPEG2000 and Sentinel 2 COG
        - link: [rio_tiler_pds.sentinel.utils.s2_sceneid_parser](https://github.com/cogeotiff/rio-tiler-pds/blob/e4421d3cf7c23b7b3552b8bb16ee5913a5483caf/rio_tiler_pds/sentinel/utils.py#L25-L60)
        - regex: `^S2[AB]_[0-9]{2}[A-Z]{3}_[0-9]{8}_[0-9]_L[0-2][A-C]$` or `^S2[AB]_L[0-2][A-C]_[0-9]{8}_[0-9]{2}[A-Z]{3}_[0-9]$`
        - example: `S2A_29RKH_20200219_0_L2A`, `S2A_L1C_20170729_19UDP_0`, `S2A_L2A_20170729_19UDP_0`

    - CBERS
        - link: [rio_tiler_pds.cbers.utils.sceneid_parser](https://github.com/cogeotiff/rio-tiler-pds/blob/e4421d3cf7c23b7b3552b8bb16ee5913a5483caf/rio_tiler_pds/cbers/utils.py#L28-L43)
        - regex: `^CBERS_(4|4A)_\w+_[0-9]{8}_[0-9]{3}_[0-9]{3}_L\w+$`
        - example: `CBERS_4_MUX_20171121_057_094_L2`, `CBERS_4_AWFI_20170420_146_129_L2`, `CBERS_4_PAN10M_20170427_161_109_L4`, `CBERS_4_PAN5M_20170425_153_114_L4`, `CBERS_4A_WPM_20200730_209_139_L4`

    - MODIS (PDS and Astraea)
        - link: [rio_tiler_pds.modis.utils.sceneid_parser](https://github.com/cogeotiff/rio-tiler-pds/blob/c533d38330f46738c46cb9927dbe91b299dc643d/rio_tiler_pds/modis/utils.py#L29-L42)
        - regex: `^M[COY]D[0-9]{2}[A-Z0-9]{2}\.A[0-9]{4}[0-9]{3}\.h[0-9]{2}v[0-9]{2}\.[0-9]{3}\.[0-9]{13}$`
        - example: `MCD43A4.A2017006.h21v11.006.2017018074804`

### Band Per Asset/File

`rio-tiler-pds` Readers assume that bands (e.g eo:band in STAC) are stored in separate files.

```bash
$ aws s3 ls landsat-pds/c1/L8/013/031/LC08_L1TP_013031_20130930_20170308_01_T1/

LC08_L1TP_013031_20130930_20170308_01_T1_B1.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B10.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B11.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B2.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B3.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B4.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B5.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B6.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B7.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B8.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_B9.TIF
LC08_L1TP_013031_20130930_20170308_01_T1_BQA.TIF
```

When reading data or metadata, readers will merge them.

e.g
```python
with S2COGReader("S2A_L2A_20170729_19UDP_0") as sentinel:
    img = sentinel.tile(77, 89, 8, bands=("B01", "B02")
    assert img.data.shape == (2, 256, 256)

    stats = sentinel.statistics(bands=("B01", "B02"))
    print(stats)
    >> {
      'B01': BandStatistics(
        min=2.0,
        max=17132.0,
        mean=2183.7570706659685,
        count=651247.0,
        sum=1422165241.0,
        std=3474.123975478363,
        median=370.0,
        majority=238.0,
        minority=2.0,
        unique=15112.0,
        histogram=[
          [476342.0, 35760.0, 27525.0, 24852.0, 24379.0, 23792.0, 20891.0, 13602.0, 3891.0, 213.0],
          [2.0, 1715.0, 3428.0, 5141.0, 6854.0, 8567.0, 10280.0, 11993.0, 13706.0, 15419.0, 17132.0]
        ],
        valid_percent=62.11,
        masked_pixels=397329.0,
        valid_pixels=651247.0,
        percentile_2=179.0,
        percentile_98=12465.0
      ),
      'B02': BandStatistics(
        min=1.0,
        max=15749.0,
        mean=1941.2052554560712,
        count=651247.0,
        sum=1264204099.0,
        std=3130.545395156859,
        median=329.0,
        majority=206.0,
        minority=11946.0,
        unique=13904.0,
        histogram=[
          [479174.0, 34919.0, 27649.0, 25126.0, 24913.0, 24119.0, 20223.0, 12097.0, 2872.0, 155.0],
          [1.0, 1575.8, 3150.6, 4725.4, 6300.2, 7875.0, 9449.8, 11024.6, 12599.4, 14174.199999999999, 15749.0]
        ],
        valid_percent=62.11,
        masked_pixels=397329.0,
        valid_pixels=651247.0,
        percentile_2=134.0,
        percentile_98=11227.079999999958
      )}

      print(stats["B01"].min)
      >> 2.0
```

## Changes

See [CHANGES.md](https://github.com/cogeotiff/rio-tiler-pds/blob/master/CHANGES.md).

## Contribution & Development

See [CONTRIBUTING.md](https://github.com/cogeotiff/rio-tiler/blob/master/CONTRIBUTING.md)

## License

See [LICENSE.txt](https://github.com/cogeotiff/rio-tiler-pds/blob/master/LICENSE.txt)

## Authors

The rio-tiler project was begun at Mapbox and has been transferred in January 2019.

See [AUTHORS.txt](https://github.com/cogeotiff/rio-tiler-pds/blob/master/AUTHORS.txt) for a listing of individual contributors.
