# NetCDF Location Weather Adapter
Python based web service that serves weather forecasts from NetCDF files, allowing for a fairly efficient reading of the NetCDF file. Provide latitude and longitude, get IPMDecisions formatted weather data back. Using the Met Norway software [Fimex](https://github.com/metno/fimex) to read and interpolate the data.

Current usage: Pulled from GitHub to build a Docker image of the following location based weather forecasting services:

* [IPM Decisions Weather Adapter for Deutsche Wetterdienst (DWD)](https://github.com/H2020-IPM-Decisions/Location-Weather-Adapter-DWD)
* [IPM Decisions Weather Adapter for Deutsche Wetterdienst (DWD) covering EU](https://github.com/H2020-IPM-Decisions/Location-Weather-Adapter-DWD_EU)
* [IPM Decisions Weather Adapter for MeteoFrance](https://github.com/H2020-IPM-Decisions/Location-Weather-Adapter-MeteoFrance)

&copy; 2021 NIBIO and Met Norway

Authors: Tor-Einar Skog (NIBIO) and Frank Thomas Tveter (Met Norway)

## License
```
 Copyright (c) 2021 NIBIO <https://www.nibio.no/> and Met Norway <https://www.met.no/>
 
 This program is free software: you can redistribute it and/or modify
 it under the terms of the GNU Affero General Public License as published by
 the Free Software Foundation, either version 3 of the License, or
 (at your option) any later version.
 
 This program is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU Affero General Public License for more details.
 
 You should have received a copy of the GNU Affero General Public License
 along with this program.  If not, see <http://www.gnu.org/licenses/>.
 
```

## Getting started
### Software requirements
* Ubuntu Linux, tested with v 18 and up
* Python3
* [Flask](https://flask.palletsprojects.com/en/1.1.x/)
* Apache2 with mod_wsgi (for production deployment)
* [Fimex](https://github.com/metno/fimex)

#### Installing Fimex
Example using Ubuntu

``` bash
add-apt-repository ppa:met-norway/fimex
apt-get update
apt-get install --assume-yes fimex-1.6-bin fimex-1.6-share libfimex-1.6-0 python3-pyfimex0-1.6
```
#### Setting up virtual environment and install Python packages

``` bash
python3 -m venv my_venv
pip3 install -r requirements.xt
```

### Additional adaptations
#### Assembling NetCDF file from provider and creating your own interpolator
It expects a file `all.nc` to reside in the `weather_data` folder. This file must be generated by service specific code. E.g. Deutsche Wetterdienst (DWD) has their own set of GRIB2 files that we download and convert into. 

### Running the app
There are two processes that need to run
1. The web server (either Flask's dev server or e.g. Apache2 with mod_wsgi)
2. The gatekeeper process

#### Running gatekeeper
``` bash
source [path_to_venv]/bin/activate
python3 app/gatekeeper.py
```

#### Using Flask's dev web server
``` bash
source [path_to_venv]/bin/activate
export FLASK_APP = app.py
flask run
```
#### Using Apache2 with mod_wsgi
Example Apache2 configuration

``` apache
        WSGIDaemonProcess weather_adapter user=ipmdecisions group=ipmdecisions threads=5
        WSGIProcessGroup weather_adapter
        WSGIApplicationGroup %{GLOBAL}
        WSGIScriptAlias / /opt/ipmdecisions/netcdf_weather_adapter/wsgi.py
        <Directory /opt/ipmdecisions/netcdf_weather_adapter>
                Require all granted
        </Directory>
```