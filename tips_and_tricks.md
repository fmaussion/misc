# Tips and tricks

Some pieces of code I need from time to time

## Xarray: shift longitudes

This is useful when the coordinates are 0-360 and you want -180 -> 180

```python
dss = ds.roll(longitude=240)  # this only works with ERA
ps = np.where(dss.longitude >= 180.)
lon = dss['longitude'].copy(deep=True).values
lon[ps] = lon[ps] - 360.
dss['longitude'].values = lon
```

## Xarray: encode data

```python
enc = {
    'msl':{'zlib':True, 'complevel':5, 'least_significant_digit':2},
    'u10':{'zlib':True, 'complevel':5, 'least_significant_digit':2},
    'v10':{'zlib':True, 'complevel':5, 'least_significant_digit':2},
      }
dss.to_netcdf('myfile.nc', encoding=enc)
```

## Geopandas: read only part of a shapefile

```python
import geopandas as gpd
import fiona

def filter(filename, colname, colvalue):
    """Filter the rows accordind to colname.value == colvalue"""
    with fiona.open(filename) as source:
        for i, feature in enumerate(source):
            if feature['properties'][colname] == colvalue:
                yield feature
                
gdf = gpd.GeoDataFrame.from_features(filter('/path/to/a/big_file.shp', 'Continent', 'Asia'))
```
