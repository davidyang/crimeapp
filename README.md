 
 
#Crimen
New York City's Crime Map

##Crimes are clustered:
<img src="http://i.imgur.com/o0Ze7xb.png">
###### API DATA
```javascript
exports.crimeData = function(req, res) {
    return Crime.find().limit(200000).exec(function(err, crimes){
        if(!err){
            var mapOutput =  _.map(crimes, function(element){
                return{
                    id: element._id,
                    latitude: element.latitude,
                    longitude: element.longitude,
                    type: element.type,
                    month: element.month,
                    year: element.year
                };
            });
            return res.json(mapOutput);
        } else{
            res.send(500);
        }
    });
};
```
######API ROUTE
```javascript
  // Server API Routes
app.route('/api/getCrimeData').get(api.crimeData);
```
######AJAX CALL
```javascript
$http.get('/api/getCrimeData').success(function(crimeData){
    var markers =  _.map(crimeData, function(element){
        var myLatlng = new google.maps.LatLng(element.latitude, element.longitude);

        var marker = new google.maps.Marker({
            position: myLatlng,
            title: element.type,
            icon: circle
        });

        var infoContent = '<div style="width: 135px; height: 50px; font-size: 12px; font-family: Courier; color: black"><b>' + element.type + '<br>' + 'MONTH: ' + element.month + '<br>' + 'YEAR: ' + element.year +'<b></div>';

        bindInfoWindow(marker, map, infoWindow, infoContent);
        return marker;
    });

    var markerCluster = new MarkerClusterer(map, markers, {
        maxZoom: 16
    });
});
```

##Search for crimes by address, with autocomplete feature:
<img src="http://i.imgur.com/34fdKKX.png">
######MARKUP
```html
<span id="main-addInput" class="height100">
 <input id="main-search" type="text" value="Enter Address" ng-autocomplete ng-model="autocomplete" options="options" details="details"/>
</span>
```
######WATCH FOR CHANGE IN COORDINATES TRIGGERED BY CHANGE OF ADDRESS BY THE USER
```javascript
$scope.$watch('details', function(details) {
    $scope.map.center = {
        latitude: details.geometry.location.lat(),
        longitude: details.geometry.location.lng()
    };
    $scope.map.zoom = 16;
});
```

##View historical crime data in the form of dynamic charts:
<img src="http://i.imgur.com/XU55QIR.png">
######SIMPLE CHART USING JAVASCRIPT
```javascript
var chartObject = {
    burglaries: {
        title: "Burglaries (2000 - 2013)",
        data: [38352,32763,31275,29110,26976,24117,23143,21762,20725,19430,18600,18720,19168,17429],
        name: "BURGLARIES"
    },
    murders: {
        title: "Murders (2000 - 2013)",
        data: [673,649,587,597,570,539,596,496,523,471,536,515,419,335],
        name: "MURDERS"
    },
    felonyAssaults: {
        title: "Felony Assaults (2000 - 2013)",
        data: [25924,23453,21147,19139,18622,17750,17309,17493,16284,16773,16956,18482,19381,20297],
        name: "FELONY ASSAULTS"
    },
    rapes: {
        title: "Rapes (2000 - 2013)",
        data: [2068,1981,2144,2070,1905,1858,1525,1351,1299,1205,1373,1420,1445,1378],
        name: "RAPES"
    },
    robberies: {
        title: "Robberies (2000 - 2013)",
        data: [32562,28202,27229,25989,24373,24722,23739,21809,22401,18601,19486,19717,20144,19128],
        name: "ROBBERIES"
    },
    grandLarcenies: {
        title: "Grand Larcenies (2000 - 2013)",
        data: [49631,46329,45771,46751,48763,48243,46625,44924,44242,39580,37835,38501,42497,45368],
        name: "GRAND LARCENIES"
    },
    GLA: {
        title: "Grand Larcenies of Motor Vehicles (2000 - 2013)",
        data: [35442, 29531, 26656, 23413, 20884, 18246, 15745, 13174, 12482, 10670, 10329, 9314, 8093, 7400],
        name: "GRAND LARCENIES OF MOTOR VEHICLES"
    }
};

$scope.showIntro = true;
var chartExists = false;
$scope.generateChart = function(crimeType){
    // $('.crime-introduction').remove();
    $scope.showIntro = false;
    if(chartExists === true){
        $('#crime-highcharts').highcharts().destroy();
    }
    $('#crime-highcharts').highcharts({
        chart: {
            type: 'column',
        },
        colors: ['#AA0036'],
        title: {
            text: chartObject[crimeType]['title']
        },
        xAxis: {
            categories: ['2000', '2001', '2002', '2003', '2004', '2005', '2006', '2007', '2008', '2009', '2010', '2011', '2012', '2013']
        },
        yAxis: {
            title: {
                text: chartObject[crimeType]['name']
            }
        },
        series: [{
            name: chartObject[crimeType]['name'],
            data: chartObject[crimeType]['data']
        }]
    });
    chartExists === true;
};
```
