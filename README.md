
# What's the Weather Like?

 1. Showcase Temperature (F) vs. Latitude (deg)
 2. Showcase Humidity (% H20) vs. Latitude (deg)
 3. Showcase Cloudiness (% coverage) vs. Latitude (deg)
 4. Showcase Wind Speed (mph) vs. Latitude (deg)
 
These plots are based on 500 unique cities, randomly selected.  Compete city API log in [api_calls.log](./api_calls.log).  Full data in [weather.csv](./weather.csv).

Plotted using Matplotlib and Seaborn.  Uses [citypy](https://github.com/wingchen/citipy) for ease of selecting cities.


```python
from citipy import citipy
from matplotlib import pyplot as plt

import numpy as np
import pandas as pd
import requests
import seaborn as srn

import json
import logging
import time

srn.set()
```


```python
logger = logging.getLogger('API calls')
logger.setLevel(logging.INFO)
fh = logging.FileHandler('api_calls.log')
formatter = logging.Formatter('%(asctime)s - %(message)s')
fh.setFormatter(formatter)
logger.addHandler(fh)
```


```python
np.random.seed(125)
lats = np.random.randint(-90, 90, size=500)
longs = np.random.randint(-180, 180, size=500)
coords = pd.DataFrame({"latitude": lats, "longitude": longs})
coords.head()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>latitude</th>
      <th>longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>67</td>
      <td>-117</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-3</td>
      <td>11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-23</td>
      <td>-146</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20</td>
      <td>-19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-47</td>
      <td>6</td>
    </tr>
  </tbody>
</table>


```python
plt.hist(coords['latitude'])
plt.show()
```


![png](output_4_0.png)



```python
plt.hist(coords['longitude'])
plt.show()
```


![png](output_5_0.png)



```python
from secrets import API_KEY

def get_weather_data(coords, time_between=1):
    """Hits OpenWeatherMap API to get weather data for coordinates.
    Coords is a dataframe of 2 columns: Latitude and Longitude.
    Returns a list of json responses.
    """
    results = []
    for ind, row in coords.iterrows():
        lat, lon = row['latitude'], row['longitude']
        query = f"http://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&APPID={API_KEY}"
        clean_url = query.rpartition("&")[0]
        city = citipy.nearest_city(lat, lon)
        logger.info(f"Call {ind}: {city.city_name} ({clean_url})")
        result = requests.get(query)
        results.append(result.json())
        time.sleep(time_between)
    return results

```


```python
test = pd.DataFrame({"latitude": [37], "longitude": [-122]})
test_results = get_weather_data(test)
test_results
```




    [{'base': 'stations',
      'clouds': {'all': 1},
      'cod': 200,
      'coord': {'lat': 37, 'lon': -122},
      'dt': 1522341300,
      'id': 5381421,
      'main': {'humidity': 76,
       'pressure': 1021,
       'temp': 287.78,
       'temp_max': 289.15,
       'temp_min': 286.15},
      'name': 'Pasatiempo',
      'sys': {'country': 'US',
       'id': 399,
       'message': 0.004,
       'sunrise': 1522331815,
       'sunset': 1522376913,
       'type': 1},
      'visibility': 16093,
      'weather': [{'description': 'clear sky',
        'icon': '01d',
        'id': 800,
        'main': 'Clear'}],
      'wind': {'deg': 331.003, 'speed': 1.32}}]




```python
full_results = get_weather_data(coords)
full_results[:3]
```




    [{'base': 'stations',
      'clouds': {'all': 8},
      'cod': 200,
      'coord': {'lat': 67, 'lon': -117},
      'dt': 1522343296,
      'id': 5994339,
      'main': {'grnd_level': 993.36,
       'humidity': 69,
       'pressure': 993.36,
       'sea_level': 1052.95,
       'temp': 246.398,
       'temp_max': 246.398,
       'temp_min': 246.398},
      'name': 'Kugluktuk',
      'sys': {'country': 'CA',
       'message': 0.1637,
       'sunrise': 1522328891,
       'sunset': 1522377527},
      'weather': [{'description': 'clear sky',
        'icon': '02d',
        'id': 800,
        'main': 'Clear'}],
      'wind': {'deg': 277.003, 'speed': 1.92}},
     {'base': 'stations',
      'clouds': {'all': 68},
      'cod': 200,
      'coord': {'lat': -3, 'lon': 11},
      'dt': 1522343290,
      'id': 2397141,
      'main': {'grnd_level': 997.01,
       'humidity': 96,
       'pressure': 997.01,
       'sea_level': 1022.27,
       'temp': 296.998,
       'temp_max': 296.998,
       'temp_min': 296.998},
      'name': 'Province de la Nyanga',
      'rain': {'3h': 5.7375},
      'sys': {'country': 'GA',
       'message': 0.0039,
       'sunrise': 1522300682,
       'sunset': 1522344180},
      'weather': [{'description': 'moderate rain',
        'icon': '10d',
        'id': 501,
        'main': 'Rain'}],
      'wind': {'deg': 142.003, 'speed': 0.97}},
     {'base': 'stations',
      'clouds': {'all': 88},
      'cod': 200,
      'coord': {'lat': -23, 'lon': -146},
      'dt': 1522343292,
      'id': 0,
      'main': {'grnd_level': 1028.95,
       'humidity': 100,
       'pressure': 1028.95,
       'sea_level': 1029,
       'temp': 300.348,
       'temp_max': 300.348,
       'temp_min': 300.348},
      'name': '',
      'sys': {'message': 0.0042, 'sunrise': 1522338680, 'sunset': 1522381510},
      'weather': [{'description': 'overcast clouds',
        'icon': '04d',
        'id': 804,
        'main': 'Clouds'}],
      'wind': {'deg': 79.5029, 'speed': 5.77}}]


```python
# Save the data out in case the world blows up
with open("weather.json", "w") as outfile:
    json.dump(full_results, outfile)

```


```python
def k_to_f(temp):
    """Converts a Kelvin temperature to Fahrenheit"""
    return temp * 9/5 - 459.67

def mps_to_mph(speed):
    """converts meters per second to miles per hour"""
    return speed * 2.23694
```


```python
important_json_data = []
for point in full_results:
    lat = point['coord']['lat']
    long = point['coord']['lon']
    temp = k_to_f(point['main']['temp'])
    humidity = point['main']['humidity']
    cloudiness = point['clouds']['all']
    wind = mps_to_mph(point['wind']['speed'])
    important_json_data.append([
        lat, long, temp, humidity, cloudiness, wind
    ])
weather_df = pd.DataFrame(important_json_data)
weather_df.columns = ["lat", "long", "temp", "humidity", "clouds", "wind"]
weather_df.head()
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lat</th>
      <th>long</th>
      <th>temp</th>
      <th>humidity</th>
      <th>clouds</th>
      <th>wind</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>67</td>
      <td>-117</td>
      <td>-16.1536</td>
      <td>69</td>
      <td>8</td>
      <td>4.294925</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-3</td>
      <td>11</td>
      <td>74.9264</td>
      <td>96</td>
      <td>68</td>
      <td>2.169832</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-23</td>
      <td>-146</td>
      <td>80.9564</td>
      <td>100</td>
      <td>88</td>
      <td>12.907144</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20</td>
      <td>-19</td>
      <td>67.3664</td>
      <td>100</td>
      <td>8</td>
      <td>9.775428</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-47</td>
      <td>6</td>
      <td>44.7764</td>
      <td>97</td>
      <td>8</td>
      <td>13.354532</td>
    </tr>
  </tbody>
</table>


```python
# Commented out to avoid overwriting data
weather_df.to_csv("weather.csv")
```

## 1. Temperature vs. Latitude


```python
plt.scatter(weather_df.temp, weather_df.lat)
plt.xlabel("Temperature (F)")
plt.ylabel("Latitude (degrees)")
plt.title("Temperature vs. Latitude")
plt.show()
```


![png](output_14_0.png)


There is a very clear correlation between temperature and latitude, with the warmest temperatures averaging approximately 80 degrees F at the equator and dropping off below zero at the poles.

## 2. Humidity vs. Latitude


```python
plt.scatter(weather_df.humidity, weather_df.lat)
plt.xlabel("Humidity (%)")
plt.ylabel("Latitude (degrees)")
plt.title("Humidity vs. Latitude")
plt.show()
```


![png](output_17_0.png)


There doesn't seem to be much of a correlation between humidity and latitude.  I might have expected the equator to be much more humid than the poles based on the ability of air to absorb moisture at higher temperatures.  A couple Google results suggest there may be an issue with the Open Weather API's humidity data.  More study probably required.

## 3. Cloudiness vs. Latitude


```python
plt.scatter(weather_df.clouds, weather_df.lat)
plt.xlabel("Cloudiness (%)")
plt.ylabel("Latitude (degrees)")
plt.title("Cloudiness vs. Latitude")
plt.show()
```


![png](output_20_0.png)



```python
plt.scatter(weather_df.long, weather_df.clouds)
plt.xlabel("Longitude (degrees)")
plt.ylabel("Cloudiness (%)")
plt.title("Longitude vs. Cloudiness")
plt.show()
```


![png](output_21_0.png)


There doesn't seem to be much of a correlation between cloudiness and latitude -- or longitude, for that matter.  It seems pretty evenly spread out.

## 4. Wind Speed vs. Latitude


```python
plt.scatter(weather_df.wind, weather_df.lat)
plt.xlabel("Wind Speed (mph (abs))")
plt.ylabel("Latitude (degrees)")
plt.title("Wind Speed vs. Latitude")
plt.show()
```


![png](output_24_0.png)


![Global wind patterns](wind-patterns.png)

There seems to be some correlation between wind speed and latitude.  I've included a theoretical global wind pattern image from wikipedia [link](https://en.wikipedia.org/wiki/Global_wind_patterns) for comparison.  The green areas are supposed to be areas of low wind speed generally.  It seems possible that these patterns are reflected in our data, with spikes in wind speed at -50 and 50 degrees latitude and slowdowns at approximately -75, 0, and 75 degrees.  Neat!

## Appendix


```python
# API Log data
with open("api_calls.log", "r") as f:
    print(f.read())

```

    2018-03-29 09:59:17,279 - Call 0: santa cruz (http://api.openweathermap.org/data/2.5/weather?lat=37&lon=-122)
    2018-03-29 10:08:07,981 - Call 0: yellowknife (http://api.openweathermap.org/data/2.5/weather?lat=67&lon=-117)
    2018-03-29 10:08:09,581 - Call 1: tchibanga (http://api.openweathermap.org/data/2.5/weather?lat=-3&lon=11)
    2018-03-29 10:08:10,901 - Call 2: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-23&lon=-146)
    2018-03-29 10:08:12,228 - Call 3: nouadhibou (http://api.openweathermap.org/data/2.5/weather?lat=20&lon=-19)
    2018-03-29 10:08:13,542 - Call 4: cape town (http://api.openweathermap.org/data/2.5/weather?lat=-47&lon=6)
    2018-03-29 10:08:14,990 - Call 5: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-31&lon=-150)

    ...