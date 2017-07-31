# Urban Pulse

introduction...

[demo](http://vgc.poly.edu/projects/urban-pulse/)

## Installing prerequisites

The following are prerequisites for all systems:

1. Qt 5.8 (or later version)
2. C++11 compatible compiler
3. Java SE Development Kit 8
3. Node.js

### Linux (Ubuntu, Linux Mint)
1. Download Qt 5.8 (or later version) at [qt.io/download-open-source](https://www.qt.io/download-open-source/) and install it.
2. Install GCC 4.8 (or later version)

	```
	sudo apt-get install gcc-4.8 g++-4.8
	```
	
3. Install Java SE Development Kit 8:

	```
	sudo add-apt-repository ppa:webupd8team/java -y
	sudo apt-get update
	sudo apt-get install oracle-java8-installer
	```
Automatically set up the Java 8 environment variables:

	```
	sudo apt-get install oracle-java8-set-default
	```

4. Install Node.js:
	```
	sudo apt-get install nodejs npm
	```
	
### macOS
1. Download Homebrew, a package manager for macOS, at [brew.sh](https://brew.sh/) and install it.
2. Download Qt 5.5 (or later version) at [qt.io/download-open-source](https://www.qt.io/download-open-source/) and install it.
3. Install GCC through [XCode](https://developer.apple.com/xcode/) or brew:

	```
	brew install gcc@4.8
	```

4. Install Java SE Development Kit 8:

	```
	brew cask install java
	```

5. Install Node.js:

	```
	brew install nodejs npm
	```
	

### Windows 7, 8, 10
1. Download Qt 5.5 (or later version) at [qt.io/download-open-source](https://www.qt.io/download-open-source/) and install it. When selecting the Qt version to install, make sure to also select MingW for installation.
2. Download Java SE Development Kit 8 [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) and install it.
3. Make sure GCC is installed through MingW.
3. Download Node.js LTS at [nodejs.org/en/download/](https://nodejs.org/en/download/) and install it.

## Compiling the latest release

Open a command line interface and follow the steps:
```
cd ComputePulse
qmake ComputePulse.pro
```

This will generate a Makefile with the compilation steps.

To compile on Windows:
```
mingw32-make
```

To compile on Ubuntu, Linux Mint or macOS:
```
make
```


## Running Urban Pulse

The Urban Pulse framework takes as input a simple comma-separate value data file (i.e. CSV file), with some activity measurements. In this example, we are going to use the [Flickr](https://webscope.sandbox.yahoo.com/catalog.php?datatype=i&did=67) data set, which contains spatial and temporal information of pictures taken all over the world. In order to simplify the example, we have made available two CSV files, with latitude, longitude and epoch time of pictures taken in New York City ([flickr_nyc.csv](http://vgc.poly.edu/files/urban-pulse/data/flickr_nyc.csv)) and San Francisco ([flickr_sf.csv](http://vgc.poly.edu/files/urban-pulse/data/flickr_sf.csv)).

Below, the first few lines of the New York City data set:

```
40.740067,-73.985495,1338164610
40.748472,-73.985700,1335191997
40.742188,-73.987924,1352021337
```

In this example, each row is going to count as one measurement when computing the scalar function, since we are interested in the activity of pictures taken in NYC and San Francisco. The CSV file, however, can also contain a weight column (e.g. taxi fare), which can be considered when computing the function.

### Computing the pulses

With both `Scalars` and `Pulse` executables properly compiled, we are ready to compute the pulse features. The first step is to compute the scalar values for each city. Go to the folder containing the `Scalars` executable and run:

```
./Scalars --input ./flickr_nyc.csv --name flickr --space 0 1 --time 2 --bound 40.6746 -74.0492 40.8941 -73.8769 --timezone UTC --output ../vis/src/data/nyc/
```

The previous line takes as an input the `flickr_nyc.csv` file, attribute the name `flickr` to it, and considers the first and second columns as the spatial ones and the third column as the temporal one. The `bound` argument ignores the CSV lines with latitude and longitude coordinates falling outside the rectangle `40.6746 -74.0492 40.8941 -73.8769`. The `timezone` argument adjusts the temporal column to the given UTC time zone. Finally, the `output` argument dictates where the output files should be saved.

Similarly, we can also run `Scalars` for San Francisco:
```
./Scalars --input ./flickr_sf.csv --name flickr --space 0 1 --time 2 --bound 37.6542 -122.534 37.8361 -122.344 --timezone UTC --output ../vis/src/data/sf/
```

The second step is to generate the pulse features for each city:
```
./Pulse --name flickr --data ../vis/src/data/nyc/ --create
./Pulse --name flickr --data ../vis/src/data/nyc/ --combine
```

The above lines consider as an input the files outputted in the first step (`--data ../vis/src/data/nyc/`) and first creates and then combines the pulses into a JSON file saved in the supplied folder. 


### Web client

In order to visualize the pulses computed previously, the Urban Pulse framework also provides an interactive visualization interface. To run it, open a command line interface and navigate to the `vis` folder. A development server can be started by running `ng serve`. Open a web browser and navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

In order to load the pulses, you must supply a path to the folder storing the files (relative to the `vis/src` folder), the name of the data set, and a temporal filter. For instance, consider the following address:
```
http://localhost:4200/?data1=data/nyc,flickr,none&data2=data/sf,flickr,none
```
The argument `data1`indicates that the computed pulse files are located in the folder `data/nyc` (i.e. `vis/src/data/nyc`), named `flickr` and we want to visualize the pulses without any temporal filter. The argument `data2` follows the same idea.

Note that the data folder must be inside `vis/src` because `ng serve` will only serve files that are inside it.

explain interface...