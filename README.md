[![TeamCity Build Status](http://halfpastfour.am:8111/app/rest/builds/buildType:(id:PHPChartJS_UnitTesting)/statusIcon)](http://halfpastfour.am:8111/viewType.html?buildTypeId=PHPChartJS_UnitTesting)
[![Build Status](https://travis-ci.org/halfpastfouram/PHPChartJS.svg?branch=master)](https://travis-ci.org/halfpastfouram/PHPChartJS)
[![Code Climate](https://codeclimate.com/github/halfpastfouram/PHPChartJS/badges/gpa.svg)](https://codeclimate.com/github/halfpastfouram/PHPChartJS)
[![Test Coverage](https://codeclimate.com/github/halfpastfouram/PHPChartJS/badges/coverage.svg)](https://codeclimate.com/github/halfpastfouram/PHPChartJS/coverage)

# PHPChartJS
PHP OOP library for [ChartJS 2.4](http://www.chartjs.org/)

PHPChartJS acts as an interface between the ChartJS library and the server side code. Set up a chart in no time and have every aspect of the graph managable from your PHP code. This interface is set up to provide code completion in every scenario so you never have to guess or lookup what options are available for the chosen chart. The library is entirely object oriented.

This library is still in active development and aims to implement all options ChartJS has to offer. Check the [Configuration milestone](https://github.com/halfpastfouram/PHPChartJS/milestone/1) to view the progress of implementing all existing options.

## Example use
````php
<?php
use Halfpastfour\PHPChartJS\Chart\Bar;

$bar = new Bar();
$bar->setId( "myBar" );

// Set labels
$bar->getLabels()->exchangeArray( [ "M", "T", "W", "T", "F", "S", "S" ] );

// Add apples
$apples = $bar->createDataSet();
$apples->setLabel( "apples" )
  ->setBackgroundColor( "rgba( 0, 150, 0, .5 )" )
  ->data()->exchangeArray( [ 12, 19, 3, 17, 28, 24, 7 ] );
$bar->addDataSet( $apples );

// Add oranges as well
$oranges = $bar->createDataSet();
$oranges->setLabel( "oranges" )
  ->setBackgroundColor( "rgba( 255, 153, 0, .5 )" )
  ->data()->exchangeArray( [ 30, 29, 5, 5, 20, 3 ] );
  
// Add some extra data
$oranges->data()->append( 10 );

$bar->addDataSet( $oranges );

// Render the chart
echo $bar->render();
````
The result generated by rendering the chart will look something like this:

````html
<canvas id="myBar"></canvas>
<script>
window.onload=(function(oldLoad){return function(){
if( oldLoad ) oldLoad();
var ctx = document.getElementById( "myBar" ).getContext( "2d" );
var chart = new Chart( ctx, {"type":"bar","data":{"labels":["M","T","W","T","F","S","S"],"datasets":[{"data":[12,19,3,17,28,24,7],"label":"apples","backgroundColor":"rgba( 0, 150, 0, .5 )"},{"data":[30,29,5,5,20,3,10],"label":"oranges","backgroundColor":"rgba( 255, 153, 0, .5 )"}]}} );
}})(window.onload);
</script>
````

### Callbacks
You can provide javascript callbacks with ease:

````php
$myCallback = "function( item ){ console.log( item ); }";
$bar->options()->tooltips()->callbacks()->setAfterBody( $myCallback );
````

### Rendering

Rendering the chart creates some HTML and some JavaScript. The JavaScript contains a JSON object providing the necessary
configuration for ChartJS. Every part of the configuration can be cast to an array or a JSON object.

Render isolated part of the configuration:

````php
$json = $myChart->options()->scales()->jsonSerialize();
````

Or return an array containing the set configuration:

````php
$options = $myChart->options()->scales()->getArrayCopy();
````


### Pretty mode
If you're not a fan of the long lines of code that are being generated you can force the rendering to be done in *pretty mode*, see the following example.

````php
// Render in pretty mode
$bar->render( true );
````

Want to see more? Fork this project and take a look at the examples in the test folder to explore the different options.

## Configuration options
Every option that is supported by ChartJS will be made available in this library.

### Layers
ChartJS requires you to build a JSON object containing the configuration options you want to set for the current chart.
These options are spread over multiple layers inside the configuration object. Accessing these layers with PHPChartJS is
 super easy.

Let's say we wanted to access the chart's animation subtree within the options subtree:
````php
$animation = $myChart->options()->animation();
````
You can now adjust any of ChartJS's animation settings by using the getters and setters provided in that particular class.

## Collections
If ChartJS requires an array with certain items as subsets in a configuration option that array will be represented by a
collection in PHPChartJS. The collection can always by accessed directly to add, remove and replace values.

In some cases a specific object with a predetermined list of options is required in a collection. In these cases methods
will be provided to create a new instance of said object and adding it to the collection.

Datasets are stored in a collection:

````php
// Create new dataset
$dataset = $myChart->createDataSet();
... (add data to the dataset)
$myChart->addDataSet( $dataset );
````

But the actual data in a dataset is also stored inside a collection:

````php
// Create new dataset
$dataset = $myChart->createDataSet();

// Add some data 
$dataset->data()->append( 1 )->append( 2 );

// Prepend some data
$dataset->data()->prepend( 0 );

// Replace data at certain position
$dataset->data()->offsetSet( 1, 3 );

// Retrieve data from certain position
$value = $dataset->data()->offsetGet( 1 ); // 3

// Add a lot of data at once whilst returning the old values
$oldData = $dataset->data()->exchangeArray( [ 1, 2, 3 ] ); // array(3) { [0]=> int(1) [1]=> int(2) [2]=> int(3) }

$myChart->addDataSet( $dataset );
````

### Control over collections
You can traverse all objects that extend the `Collection` class. To give you more flexibility, all collections in this project extends the `Collection\ArrayAccess` class which provides direct access as if you were talking to an array. This class also provides an iterator that can be used in loops or even manually.

#### Array access example

````php
$data = $myChart->getDataSet()->data();
$data[] = 0;
$data[5] = 12;
````

#### Traversing

````php
foreach( $myChart->getDataSet()->data() as $key => $value ) {
    var_dump( $key, $value );
}
````

#### Manual traversing

````php
$data = $myChart->getDataSet()->data();
$iterator = $data->getIterator();

// Jump forward to next position
$iterator->next();
var_dump( $iterator->current() );

// Go back one position
$iterator->previous();
var_dump( $iterator->getKey(), $iterator->current() );

// Receive the list of keys in the dataset.
var_dump( $iterator->calculateKeyMap() );
````

## Installation

### Using composer
    $ composer require halfpastfouram/phpchartjs dev-master

### Development
This project uses composer, which should be installed on your system. Most
Linux systems have composer available in their PHP packages.
Alternatively you can download composer from [getcomposer.org](http://getcomposer.org).

If you use the PhpStorm IDE then you can simply init composer through the IDE. However,
full use requires the commandline. See PhpStorm help, search for composer.

To start development, do `composer install` from the project directory. 

**Remark** Do not use `composer update` unless you changed the dependency requirements in composer.json.
The difference is that `composer install` will use composer.lock read-only, 
while `composer update` will update your composer.lock file regardless of any change.
As the composer.lock file is committed to the repo, other developers might conclude 
dependencies have changed, while they have not.
