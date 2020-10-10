# World Cities Transportation Transit Data

#### Adapted from Dr. Yatsenko


## Description

Original Data Source: https://www.citylines.co/data

- cities.csv : Cities including coordinates, start year, and other geographic information
- lines.csv : Lines including corresponding cities and systems
- stations.csv : Stations including corresponding line ids, names, geometry, and dates
- systems.csv : Systems and their corresponding city ids
- tracks.csv : Tracks including corresponding line ids, geometry, and dates
- station_lines.csv : Station id, line id, city, id and dates
- track_lines.csv: 	Line id, city id, and dates


## Prerequisites

```python
import csv
import numpy as np
import pandas as pd 
import matplotlib.pyplot as plt
import os 
import seaborn as sns
import pylab as pl
```

## Organizing the data

```python
def parse_linestring(geometry):
    """
    :param geometry: a string in the format "LINESTRING(x1 y1, x2 y2, ..., xn yn)"
    return numpy array of shape (n, 2) with coordinates
    """
    start = geometry.find('(')
    end = geometry.find(')')
    substring = geometry[start+1:end]
    return np.array(
        [[float(f) for f in pair.split(' ')] for pair in substring.split(',')])
def parse_point(coords):
    """
    :param coords: a string in the form "POINT(x, y)"
    :return: [x, y] as floats
    """
    start, end = coords.find('('), coords.find(')')
    return [float(p) for p in coords[start+1:end].split(' ')]
```
## Visuals Included


### Top 10 cities by line length
![Top_10_Cities](https://raw.githubusercontent.com/borovkk/worldcitiestransportationtransitdata/main/top10cities.png)
```python
# TOP 10 cities
fig, ax = plt.subplots(1, 1, figsize=(7, 5))
n = 10
colors = [plt.cm.rainbow(i/n) for i in range(n)]
ax.barh(np.r_[:n], list(systems.values())[-n:], color=colors)
ax.set_yticks(np.r_[:n])
ax.set_yticklabels(list(systems)[-n:])
ax.set_title(f'Top {n} cities')
ax.set(frame_on=False)
ax.grid(True)
ax.set_xlabel('Total length of all lines (km)');
```

### Top 10 countries by number of stations
![Top_10_Countries](https://raw.githubusercontent.com/borovkk/worldcitiestransportationtransitdata/main/top10countries.png)

```python
country_stations = stations.join(cities['country'], on='city_id')
count_stations=country_stations.groupby(['country']).count()['city_id'].sort_values()
count_stations.plot.barh()
```

### Track length over the years

![track_length_overyears](https://raw.githubusercontent.com/borovkk/worldcitiestransportationtransitdata/main/length_track_build_overyears.png)

```python
st_years = []
st_totals = []
year = 1834
kms = 0
while year < 2020:
    for num in stockholm_years[stockholm_years['opening'] == year]['length']:
        kms += num
    st_totals.append(kms/1000)
    st_years.append(year)
    year +=1
    
fig, ax = plt.subplots(1,1, figsize = (9,5))
ax.plot(st_years,st_totals,'-')
ax.set_title("Kilometers of Track Built 1834-2019", fontsize = 16)
plt.xlabel("Year")
plt.ylabel("Kilometers");
```

### Selected cities transit system map 
![transit_system_selected_cities](https://raw.githubusercontent.com/borovkk/worldcitiestransportationtransitdata/main/transit_system_selected_cities.png)

```python
codes = 1,69,15,147,71,252, 102, 206, 114, 99, 118, 78

fig, axx=plt.subplots(4,3, figsize=(12,12))

for code, ax in zip(codes,axx.flatten()):
    name = cities['name'][code]
    for line in tracks[tracks['city_id'] == code]['geometry']:
        xy = parse_linestring(line)
        ax.plot(xy[:,0], xy[:, 1])
    ax.set_title(f'{name}\nTransit Systems')
    ax.axis(False);
plt.savefig('map.png',dpi=300)
```

### Selected cities visualisation of the stations' location
![stations_location_viz](https://raw.githubusercontent.com/borovkk/worldcitiestransportationtransitdata/main/station_locations.png)

```python
codes = 1,69,15,147,71,252, 102, 206, 114, 110, 99, 78
fig, axx = plt.subplots(3,4,figsize = (16,16))

for code, ax in zip(codes,axx.flatten()):
    name = cities['name'][code]
    locations = []
    for geometry in stations[stations['city_id'] == code]['geometry']:
        locations.append(parse_point(geometry))
    locations = np.array(locations)
    ax.plot(locations[:,0], locations[:,1], '.', alpha = .4, color = 'blue')
    ax.set_title(f'{name}\n Station Locations')
    fig.suptitle("Stations", fontsize = 16)
```


## Author

* **Kristina Borovkova** - *Full Project* - [Link](https://github.com/borovkk/worldcitiestransportationtransitdata/blob/main/World_Cities_Transportation_Transit_Data.ipynb)
