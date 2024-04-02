/**
 * Function to mask clouds using the Sentinel-2 QA band
 * @param {ee.Image} image Sentinel-2 image
 * @return {ee.Image} cloud masked Sentinel-2 image
 */
function maskS2clouds(image) {
  var qa = image.select('QA60');

  // Bits 10 and 11 are clouds and cirrus, respectively.
  var cloudBitMask = 1 << 10;
  var cirrusBitMask = 1 << 11;

  // Both flags should be set to zero, indicating clear conditions.
  var mask = qa.bitwiseAnd(cloudBitMask).eq(0)
      .and(qa.bitwiseAnd(cirrusBitMask).eq(0));

  return image.updateMask(mask).divide(10000);
}

var image = ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED')
                  .filterDate('2021-01-01', '2021-01-30')
                  // Pre-filter to get less cloudy granules.
                  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE',20))
                  .map(maskS2clouds)
                  .mean()
                  .clip(ROI);

var visualization = {
  min: 0.0,
  max: 0.3,
  bands: ['B4', 'B3', 'B2'],
};

Map.addLayer(image, visualization, 'sentinel2');

var image1 = ee.ImageCollection('COPERNICUS/S2_SR_HARMONIZED')
                  .filterDate('2024-01-01', '2024-01-30')
                  // Pre-filter to get less cloudy granules.
                  .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE',20))
                  .map(maskS2clouds)
                  .mean()
                  .clip(ROI);

var visualization = {
  min: 0.0,
  max: 0.3,
  bands: ['B4', 'B3', 'B2'],
};

Map.addLayer(image1, visualization, 'sentinel2_24');

// NDVI

var S2_ndvi_21 = image.normalizedDifference(['B8', 'B4']).rename('S2_ndvi_21');
Map.addLayer(S2_ndvi_21,{},'S2_ndvi_21');

var S2_ndvi_24 = image1.normalizedDifference(['B8', 'B4']).rename('S2_ndvi_24');
Map.addLayer(S2_ndvi_24,{},'S2_ndvi_24');

var diffndvi = S2_ndvi_24.subtract(S2_ndvi_21);
Map.addLayer(diffndvi);

//NDWI

var S2_ndwi = image.normalizedDifference(['B3', 'B11']).rename('S2_NDWI');
Map.addLayer(S2_ndwi, imageVisParam9, 'NDWI');


//NDBI

var S2_ndbi = image.normalizedDifference(['B11', 'B8']).rename('S2_NDBI');
Map.addLayer(S2_ndbi,imageVisParam13, 'NDBI');

// Export the image, specifying the CRS, transform, and region.
Export.image.toDrive({
 image:S2_ndvi_21.float(),
 description: 'NDVI21',
 region: ROI,
 scale : 10,
 maxPixels: 1e13
});

Export.image.toDrive({
 image:S2_ndvi_24.float(),
 description: 'NDVI24',
 region: ROI,
 scale : 10,
 maxPixels: 1e13
});

Export.image.toDrive({
 image:diffndvi.float(),
 description: 'NDvVIdiff',
 region: ROI,
 scale : 10,
 maxPixels: 1e13
});
