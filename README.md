# HCHO
var date_1 ='2019-01-01'
var date_2 ='2021-12-31'

var col = ee.ImageCollection("COPERNICUS/S5P/OFFL/L3_HCHO")
.filterBounds(geometry)
.filterDate(date_1,date_2)
.select('tropospheric_HCHO_column_number_density')
.map(function(a) {
  return a.set('month', ee.Image(a).date().get('month'))
})

//print(col)

var months = ee.List(col.aggregate_array('month')).distinct()
//print(months)

var mc = months.map(function(x){
  return col.filterMetadata('month', 'equals',x).mean().set('month', x)
})

var final_image= ee.ImageCollection.fromImages(mc)

//print(final_image)

var chart =ui.Chart.image.series(final_image, geometry,ee.Reducer.mean(),5000,'month')
.setOptions({
  title: 'HCHO Concentration'
})
print(chart)
