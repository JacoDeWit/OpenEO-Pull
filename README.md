# OpenEO-Pull
This script downloads cloud-masked Sentinel‑2 L2A band composites for any parts within a shapefile, covered by a grid of tiles.

What it does
1. Sets the processing parameters
   - Reads any shapefile.
   - Processes imagery from datestart to dateend.
   - Downloads bands B03, B08, B8A and B11.
     * Can be customized to any other bands
   - Uses a 40 m output resolution in EPSG:3395.
     * Can be changed to fit any need
     * Main reason it was resampled to coarser resolution is to cover larger areas with more ease
     * Less download and processing times
   - Allows scenes with up to 40% cloud cover as candidates.
2. Connects to CDSE openEO
   It authenticates to the Copernicus Data Space openEO backend. If a download has a connection error or timeout, it retries up to five times, with increasing delays.
3. Reprojects the shapefile and builds a grid
   The shapefile boundary is transformed to EPSG:3395. The script finds its bounding box and lays out 100 km × 100 km square tiles across it. Tiles that do not intersect the shapefile are skipped.
4. Builds a cloud-masked temporal composite for each tile
   For each intersecting tile, it loads the four reflectance bands and the Scene Classification Layer (SCL) for the date range. It creates a dilated cloud mask from selected SCL classes, applies the mask, then calculates the median over time for each band.
5. Downloads each band as a separate GeoTIFF
   It resamples each band to 40 m using nearest-neighbour resampling, submits a batch job, downloads the result, and saves it in the tile’s folder as B03.tif, B08.tif, B8A.tif or B11.tif.
6. Skips completed tiles
   If all four band files already exist for a tile, the script skips that tile. You can also restrict processing to particular numbered tiles using SELECTED_TILES.

The output folder structure is:
C:\Work\Sentinel\Sentinel2_Tiles\shapefile\2025-01-01\
    tile_001\
        B03.tif
        B08.tif
        B8A.tif
        B11.tif
