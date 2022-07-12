# -https-github.com-giswqs-geemap
Classification
Map.setCenter(29.27, -20.501,11);
var selection = L9.filterBounds(ROI)
.filterDate("2021-01-01","2022-01-01")
.filterMetadata("CLOUD_COVER", "less_than", 1)
.mean()
.clip(ROI);  
//Map.addLayer(selection, {bands:["B4","B3","B2"]});
var training_points = Mine_dump.merge(Mine_area).merge(Buil_up).merge(Shrub_veg).merge(Farming).merge(Shrub_veg).merge(Rocky_mountains).merge(Farming).merge(Road).merge(water);
//print(training_points);
var training_data = selection.sampleRegions({
                    collection:training_points,
                    properties: ['LC'],
                    scale: 30})
//print(training_data);
var classifier = ee.Classifier.smileCart()
var classifier = classifier.train({features: training_data,
classProperty: "LC",
inputProperties: ["B2","B3","B4","B5","B6","B7","B10","B11"]});
var classified_image = selection.classify(classifier); 

Map.addLayer(classified_image, {min: 0, max: 9, palette: ['grey','white','red','yellow','lime','brown','green','black','blue']},
'classified image');

print(classifier.confusionMatrix()); 
var array = ee.Array([[3,	0,	0,	0,	0,	0,	0,	0,	0],
                      [0,	4,	0,	0,	0,	0,	0,	0,	0],
                      [0,	0,	5456,	0,	0,	0,	0,	0,	0],
                      [0,	0,	0,	12594,	0,	0,	0,	0,	0],
                      [0,	0,	0,	0,	23734,	0,	0,	0,	0],
                      [0,	0,	0,	0,	0,	11897,	0,	0,	0],
                      [0,	0,	0,	0,	0,	0,	58020,	0,	0],
                      [0,	0,	2,	0,	0,	1,	30,	2158,	0],
                      [0,	0,	0,	0,	0,	0,	0,	0,	725]]);
var confusionMatrix = ee.ConfusionMatrix(array);

print("Constructed confusion matrix", confusionMatrix);
// Calculate overall accuracy.
print("Overall accuracy", confusionMatrix.accuracy());
// Calculate consumer's accuracy, also known as user's accuracy or
// specificity and the complement of commission error (1 − commission error).
print("Consumer's accuracy", confusionMatrix.consumersAccuracy());

// Calculate producer's accuracy, also known as sensitivity and the
// compliment of omission error (1 − omission error).
print("Producer's accuracy", confusionMatrix.producersAccuracy());

// Calculate kappa statistic.
print('Kappa statistic', confusionMatrix.kappa());

