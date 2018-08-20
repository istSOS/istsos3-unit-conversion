# istsos3-unit-conversion

## Installation guide 
1. Clone this Repo. 
`git clone https://github.com/ChristophBerg/postgresql-unit.git`

2. `cd postgresql-unit`

3. Change postgresql-server-dev-(10, 9.5 etc.) according to your installation configuration
```
sudo apt-get install build-essential postgresql-server-dev-10 flex bison
make PG_CONFIG=/usr/lib/postgresql/10/bin/pg_config
sudo make install PG_CONFIG=/usr/lib/postgresql/10/bin/pg_config
```

4. For more detail postgresql-unit [https://github.com/ChristophBerg/postgresql-unit](https://github.com/ChristophBerg/postgresql-unit).

5. Clone istsos3 main repo.
`git clone https://github.com/istSOS/istsos3.git`
Clone unit conversion plugins repo.
`https://github.com/istSOS/istsos3-unit-conversion.git`
extract plugins and paste in istsos3/istsos/plugins

6. Install istsos3 Using main repo. documentation
   [https://github.com/istSOS/istsos3/blob/master/README.md](https://github.com/istSOS/istsos3/blob/master/README.md) .

7. `cd istsos3`

8. Start server using `python examples/server_tornado.py` 

9. Install Postman for Post request easily [https://www.getpostman.com/](https://www.getpostman.com/).

10. listen at `http://localhost:8887/rest` in Postman.

11. Insert sensor with one observable property sensor in istsos3 using 
    [http://istsos.org/en/v3.0.0-Beta/index.html](http://istsos.org/en/v3.0.0-Beta/index.html)
                                             OR
``` 
{
    "action": "CREATE_SENSOR",
    "data": {
        "name": "belin",
        "fixed": true,
        "procedure": "belin",
        "procedure_description_format": ["http://www.opengis.net/sensorML/1.0.1"],
        "observable_properties": [
            {
                "name": "temperature",
                "definition": "urn:ogc:def:parameter:x-istsos:1.0:temperature",
                "uom": "°C",
                "type": "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement"
            }
        ],
        "observation_types": [
            "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement"
        ],
        "foi_type": "http://www.opengis.net/def/samplingFeatureType/OGC-OM/2.0/SF_SamplingPoint",
        "sampled_foi": {
            "identifier": "belin_foi",
            "name": "belin_foi",
            "type": "http://www.opengis.net/def/samplingFeatureType/OGC-OM/2.0/SF_SamplingPoint",
            "shape": {
                "type": "Point",
                "coordinates": [
                    0.0,
                    0.0,
                    0.0
                ]
            }
        }
    }
}
```

    Insert Observation any valid data like bellinzona with one observable property
```
{
"action": "INSERT_OBSERVATIONS",
    "data": [{
        "offering": "belin",
        "procedure": "belin",
        "type": "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement",
        "featureOfInterest": "beline_foi",
        "phenomenonTime": {
            "timeInstant": {
                "instant": "2003-08-05T00:00:00+02:00"
            }
        },
        "resultTime": {
            "timeInstant": {
                "instant": "2003-08-05T00:00:00+02:00"
            }
        },
        "result": 28.3,
        "observedProperty": {
            "name": "temperature",
            "def": "urn:ogc:def:parameter:x-istsos:1.0:temperature",
            "uom": "°C",
            "type": "http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement"
        }
    }]
}
```
---
---
## Using PostgreSQL_unit
1. Fetch Observation of any offering from istsos3 and change `in_unit` variable according to specific unit below script 
2. `in_unit` Lookup Table [Lookup Table](https://github.com/rahulworld/Support-of-Unit-Of-Measure-conversion-in-istSOS3/wiki/Lookup-Table-POSTGRESQL_UNIT_CONVERSION).
If you want to define complex unit then you can add units as user defined unit in lookup table for making units conversion easy.
 `istsos3/istsos/plugins/unit_conv_post/lookUpTable.py`

```
{
    "action": "UNIT_CONVERSION_USING_POSTGRESQL_UNIT",
    "data": {
        "offerings": ["belin"],
        "observedProperties": [
            "urn:ogc:def:parameter:x-istsos:1.0:temperature"
        ],
        "download_file": {
	    	"file_name": "testing3",
	    	"location": "istsos/plugins/unit_con_post/download_file/"
    	},
        "in_unit":"degK",
	    "operation": {
	    	"type":"mul",
	    	"qty": "10",
	    	"unit": "degK"
	    },
	    "responseFormat": "application/json;subtype='array'"
    }
}
```
#### Take Data as `array`, `array2`, `vega` and `default data` format as user specific unit after change subtype above script in `responseFormat`.

```
Remember if istsos3 give error there is no unit extension then create manually in istsos3 database in postgresql database using.
``` 
```
create extension unit;  
```

### Unit Conversion in other specific units and perform operation with magnitude (`addition`, `subtraction`, `multiplication` and `division`) in other specific units will work.

### Some  Specific `Units` using `UNIT_CONVERSION_USING_POSTGRESQL_UNIT` action.

| Properties | types |
| ------ | ------ |
| Temperature | °C, °K, °F, °R |
| Length | mm, cm, in, ft, m, km, mi, fathom |
| Time | ns, ms, s, min, h, d, week, month, year |
| Frequency | Hz, mHz, kHz, MHz, GHz, THz, rpm, deg/s, radian/s |
| Speed | mm/s, cm/s, m/s, m/h, in/s, ft/s, mi/h, knot |
| Area | mm^2, cm^2, m^2, ha, km^2, in^2, ft^2, acre, mi^2 |
| Volume | mm^3, cm^3, m^3, in^3, ft^3, ml, l, kl, tsp, cup, qt, gal, yd^3 |
| Mass | mcg, mg, g, kg, oz, lb, mt, t |
| Flow | mm^3/s, cm^3/s, cl/s, dl/s, ml/s, l/s, l/min/ l/h, kl/s, in^3/s, ft^3/s, tsp/s, cup/s, qt/s, gal/s, gal/min, yd^3/s |
| Pace | s/m, min/m, s/ft, min/km |
| Storage | byte, B, kB, KB, MB, GB, TB |
| Volumetric Analysis | ppm, ppb, ppt |
| Volt | mV, V, kV |
| Current | A, mA, kA |
| Power | mW, W, kW, MW, GW |
| Energy | mWh, Wh, MWh, GWh, J, kJ |
| Angle | deg, radian, arcmin, arcsec |
| Capacitance | pC, nC, μC, mC, C |
| Force | N, kN, lbf |
| Acceleration | gravity, m/s^2 |

### `NOTE`: For `Regional` and `more units` visit
##### [https://github.com/ChristophBerg/postgresql-unit/blob/master/definitions.units](https://github.com/ChristophBerg/postgresql-unit/blob/master/definitions.units). 

##### `NOTE:`Temprature conversion don't use `°C` directly in operations because of it behaves difference between temprature as `1 °K` so perform operations using `°K`.

## Operations 
### Addition
```
{
    "action": "UNIT_CONVERSION_USING_POSTGRESQL_UNIT",
    "data": {
        "offerings": ["belin"],
        "observedProperties": [
            "urn:ogc:def:parameter:x-istsos:1.0:temperature"
        ],
        "in_unit":"°K",
	    "operation": {
	    	"type":"add",
	    	"qty": "10",
	    	"unit": "°K"
	    },
	    "responseFormat": "application/json;subtype='array'"
    }
}
```
##### `Note:` if `qty` is `None` it will be `0 (add, sub)`, `1 (mul, div)`.
#
##### `Note:` if `unit` is `None` it will be `default uom`.
#
##### Case 1: only `in_unit` exits.
1. Only unit conversion take place and final uom will be `in_unit`.
##### Case 2: `in_unit`, `operation`, `unit` all are exits.
1. First addition operation perform `data.add(qty, unit)`.
2. Then unit conversion take place and final uom will be `in_unit`.
##### Case 3: only `operation`, `unit` are exits.
1. First addition operation perform `data.add(qty, unit)`.

### Subtration
##### `Note` operation `type` : `sub` only change same as addition.
#
### Multiplication
##### `Note` operation `type` : `mul`.
#
##### Case 1: only `in_unit` exits.
1. Only unit conversion take place and final uom will be `in_unit`.
##### Case 2: `in_unit`, `operation`, `unit` all are exits.
1. First multiplication operation perform `data.mul(qty, unit)`.
2. Then unit conversion take place and final uom will be `in_unit * unit`.
##### Case 3: `in_unit`, `operation` are exits.
1. First multiplication operation perform `data.mul(qty, in_unit)`.
2. Then unit conversion take place and final uom will be `in_unit * in_unit`.
##### Case 4: only `operation`, `unit` are exits.
1. Only multiplication take place and final uom will be `default_uom * unit`.

### Division
##### `Note` operation `type` : `div` only change same as multiplication.
#
## Download CSV after unit conversion and operations
```
{
    "action": "UNIT_CONVERSION_USING_POSTGRESQL_UNIT",
    "data": {
        "offerings": ["belin"],
        "observedProperties": [
            "urn:ogc:def:parameter:x-istsos:1.0:temperature"
        ],
        "download_file": {
	    	"file_name": "testing3",
	    	"location": "istsos/plugins/unit_con_post/download_file/"
    	},
        "in_unit":"°K",
	    "operation": {
	    	"type":"mul",
	    	"qty": "10",
	    	"unit": "°F"
	    },
	    "responseFormat": "application/json;subtype='array'"
    }
}
```
### Download 
##### CSV file download after unit conversion and operations in files with all meta data and converted uom.
- `file_name` not exits then file_name will be `default offering`.
- `location` not exits then will be download on `istsos3` root folder.
 ---
 ---
## Using Pint
1. Fetch Observation of any offering from istsos3 and change `in_unit` variable according to specific unit below script 
2. `in_unit` Lookup Table [Lookup Table](https://github.com/rahulworld/Support-of-Unit-Of-Measure-conversion-in-istSOS3/wiki/Lookup-Table-POSTGRESQL_UNIT_CONVERSION).
If you want to define complex unit then you can add units as user defined unit in lookup table for making units conversion easy.
 `istsos3/istsos/plugins/unit_conv_pint/lookUpTable.py`


`Note:` Temprature conversion don’t use `degC` directly in operations because of it behaves difference between temprature as `1 °K` so perform operations using `°K`.
### Installation
`sudo pip3 install pint`

Use this post script for pint only for array type data.
```
{
    "action": "UNIT_CONVERSION_USING_PINT",
    "data": {
        "offerings": ["belin"],
        "observedProperties": [
            "urn:ogc:def:parameter:x-istsos:1.0:temperature"
        ],
        "download_file": {
	    	"file_name": "pint1",
	    	"location": "istsos/plugins/unit_con_post/download_file/"
    	},
    	"operation": {
	    	"type":"mul",
	    	"qty": "1",
	    	"unit": "degK"
	    },
        "responseFormat": "application/json;subtype='array'",
        "in_unit":"degK"
    }
}
```

### Some  Specific `Units` using `UNIT_CONVERSION_USING_PINT` action.

| Properties | types |
| ------ | ------ |
| Temperature | degC, degK, degF, degR |
| Length | mm, cm, in, ft, m, km, mi, fathom |
| Time | ns, ms, s, min, hour, day, week, month, year |
| Frequency | Hz, mHz, kHz, MHz, GHz, THz, rpm, deg/s, rad/s |
| Speed | mm/s, cm/s, m/s, m/h, in/s, ft/s, mi/h, knot |
| Area | mm^2, cm^2, m^2, ha, km^2, in^2, ft^2, acre, mi^2 |
| Volume | mm^3, cm^3, m^3, in^3, ft^3, ml, l, kl, tsp, cup, qt, gal, yd^3 |
| Mass | mg, g, kg, oz, lb, mt, t |
| Flow | mm^3/s, cm^3/s, cl/s, dl/s, ml/s, l/s, l/min/ l/h, kl/s, in^3/s, ft^3/s, tsp/s, cup/s, qt/s, gal/s, gal/min, yd^3/s |
| Pace | s/m, min/m, s/ft, min/km |
| Storage | bit, byte, B, kB, MB, GB, TB |
| Volt | mV, V, kV |
| Current | A, mA, kA |
| Power | mW, W, kW, MW, GW |
| Energy | mWh, Wh, MWh, GWh, J, kJ |
| Angle | deg, radian, arcmin, arcsec |
| Capacitance | pC, nC, μC, mC, C |
| Force | N, kN, lbf |
| Acceleration | gravity, m/s^2 |

### `NOTE`: For `Regional` and `more units` visit
##### [https://github.com/hgrecco/pint/blob/master/pint/default_en.txt](https://github.com/hgrecco/pint/blob/master/pint/default_en.txt). 

## `NOTE:` All procedure for pint action `UNIT_CONVERSION_USING_PINT` is same as  action `UNIT_CONVERSION_USING_POSTGRESQL_UNIT`.

##### Take Data as `array` format as user specific unit after change subtype above script in `responseFormat`.

## `Thank You`

