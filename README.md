
# What's the Weather Like?

 1. Showcase Temperature (F) vs. Latitude (deg)
 2. Showcase Humidity (% H20) vs. Latitude (deg)
 3. Showcase Cloudiness (% coverage) vs. Latitude (deg)
 4. Showcase Wind Speed (mph) vs. Latitude (deg)
 
These plots are based on 500 unique cities, randomly selected.  Compete city API log in [city.log](./city_log.txt).  Full data in [weather.csv](./weather.csv).

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
fh = logging.FileHandler('city.log')
fh.setLevel(logging.INFO)
ch = logging.StreamHandler()
ch.setLevel(logging.ERROR)
logger.addHandler(fh)
logger.addHandler(ch)
```


```python
lats = np.random.randint(-90, 90, size=500)
longs = np.random.randint(-180, 180, size=500)
coords = pd.DataFrame({"latitude": lats, "longitude": longs})
coords.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
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
      <td>76</td>
      <td>169</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12</td>
      <td>86</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>175</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-6</td>
      <td>-129</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8</td>
      <td>-70</td>
    </tr>
  </tbody>
</table>
</div>




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
        query = f"http://api.openweathermap.org/data/2.5/weather?lat={row['latitude']}&lon={row['longitude']}&APPID={API_KEY}"
        city = citipy.nearest_city(row['latitude'], row['longitude'])
        logger.info(f"Call {ind}: {city.city_name} ({query})")
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
      'dt': 1522184280,
      'id': 5381421,
      'main': {'humidity': 45,
       'pressure': 1023,
       'temp': 293.87,
       'temp_max': 295.15,
       'temp_min': 291.15},
      'name': 'Pasatiempo',
      'sys': {'country': 'US',
       'id': 512,
       'message': 0.164,
       'sunrise': 1522159177,
       'sunset': 1522204017,
       'type': 1},
      'visibility': 16093,
      'weather': [{'description': 'clear sky',
        'icon': '01d',
        'id': 800,
        'main': 'Clear'}],
      'wind': {'deg': 200, 'speed': 3.6}}]




```python
# Commenting out so I don't accidentally hit the API 500 more times.
# full_results = get_weather_data(coords)
# full_results[:3]
```




    [{'base': 'stations',
      'clouds': {'all': 36},
      'cod': 200,
      'coord': {'lat': 76, 'lon': 169},
      'dt': 1522188523,
      'id': 0,
      'main': {'grnd_level': 1025.9,
       'humidity': 88,
       'pressure': 1025.9,
       'sea_level': 1025.86,
       'temp': 251.87,
       'temp_max': 251.87,
       'temp_min': 251.87},
      'name': '',
      'sys': {'message': 0.0049, 'sunrise': 1522086642, 'sunset': 1522137091},
      'weather': [{'description': 'scattered clouds',
        'icon': '03n',
        'id': 802,
        'main': 'Clouds'}],
      'wind': {'deg': 299.501, 'speed': 1.32}},
     {'base': 'stations',
      'clouds': {'all': 0},
      'cod': 200,
      'coord': {'lat': 12, 'lon': 86},
      'dt': 1522188531,
      'id': 0,
      'main': {'grnd_level': 1023.87,
       'humidity': 100,
       'pressure': 1023.87,
       'sea_level': 1023.88,
       'temp': 301.095,
       'temp_max': 301.095,
       'temp_min': 301.095},
      'name': '',
      'sys': {'message': 0.1637, 'sunrise': 1522109722, 'sunset': 1522153624},
      'weather': [{'description': 'clear sky',
        'icon': '01n',
        'id': 800,
        'main': 'Clear'}],
      'wind': {'deg': 171.001, 'speed': 1.97}},
     {'base': 'stations',
      'clouds': {'all': 36},
      'cod': 200,
      'coord': {'lat': 31, 'lon': 175},
      'dt': 1522188526,
      'id': 0,
      'main': {'grnd_level': 1023.87,
       'humidity': 100,
       'pressure': 1023.87,
       'sea_level': 1023.88,
       'temp': 290.695,
       'temp_max': 290.695,
       'temp_min': 290.695},
      'name': '',
      'sys': {'message': 0.003, 'sunrise': 1522088086, 'sunset': 1522132569},
      'weather': [{'description': 'scattered clouds',
        'icon': '03n',
        'id': 802,
        'main': 'Clouds'}],
      'wind': {'deg': 273.501, 'speed': 12.37}}]




```python
# Save the data out in case the world blows up
# Commenting out so I don't accidentally overwrite my data
# with open("weather.json", "w") as outfile:
#     json.dump(full_results, outfile)

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




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
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
      <td>76</td>
      <td>169</td>
      <td>-6.304</td>
      <td>88</td>
      <td>36</td>
      <td>2.952761</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12</td>
      <td>86</td>
      <td>82.301</td>
      <td>100</td>
      <td>0</td>
      <td>4.406772</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>175</td>
      <td>63.581</td>
      <td>100</td>
      <td>36</td>
      <td>27.670948</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-6</td>
      <td>-129</td>
      <td>77.801</td>
      <td>100</td>
      <td>0</td>
      <td>11.788674</td>
    </tr>
    <tr>
      <th>4</th>
      <td>8</td>
      <td>-70</td>
      <td>93.686</td>
      <td>44</td>
      <td>32</td>
      <td>6.867406</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Commented out to avoid overwriting data
# weather_df.to_csv("weather.csv")
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
with open("city.log", "r") as f:
    print(f.read())

```

    Call 0: pevek (http://api.openweathermap.org/data/2.5/weather?lat=76&lon=169&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 1: kattivakkam (http://api.openweathermap.org/data/2.5/weather?lat=12&lon=86&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 2: nikolskoye (http://api.openweathermap.org/data/2.5/weather?lat=31&lon=175&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 3: atuona (http://api.openweathermap.org/data/2.5/weather?lat=-6&lon=-129&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 4: barinas (http://api.openweathermap.org/data/2.5/weather?lat=8&lon=-70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 5: limbe (http://api.openweathermap.org/data/2.5/weather?lat=4&lon=9&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 6: zhaoyang (http://api.openweathermap.org/data/2.5/weather?lat=33&lon=120&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 7: arraial do cabo (http://api.openweathermap.org/data/2.5/weather?lat=-41&lon=-28&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 8: arraial do cabo (http://api.openweathermap.org/data/2.5/weather?lat=-34&lon=-28&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 9: bredasdorp (http://api.openweathermap.org/data/2.5/weather?lat=-53&lon=18&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 10: kamenka (http://api.openweathermap.org/data/2.5/weather?lat=73&lon=45&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 11: nanga eboko (http://api.openweathermap.org/data/2.5/weather?lat=5&lon=13&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 12: matveyevka (http://api.openweathermap.org/data/2.5/weather?lat=52&lon=56&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 13: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-77&lon=-140&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 14: hamilton (http://api.openweathermap.org/data/2.5/weather?lat=30&lon=-64&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 15: lompoc (http://api.openweathermap.org/data/2.5/weather?lat=22&lon=-128&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 16: kargopol (http://api.openweathermap.org/data/2.5/weather?lat=62&lon=38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 17: miri (http://api.openweathermap.org/data/2.5/weather?lat=5&lon=113&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 18: ulladulla (http://api.openweathermap.org/data/2.5/weather?lat=-39&lon=157&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 19: olavarria (http://api.openweathermap.org/data/2.5/weather?lat=-37&lon=-61&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 20: east london (http://api.openweathermap.org/data/2.5/weather?lat=-41&lon=35&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 21: port alfred (http://api.openweathermap.org/data/2.5/weather?lat=-83&lon=40&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 22: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=-88&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 23: belushya guba (http://api.openweathermap.org/data/2.5/weather?lat=89&lon=47&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 24: qaanaaq (http://api.openweathermap.org/data/2.5/weather?lat=89&lon=-78&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 25: college (http://api.openweathermap.org/data/2.5/weather?lat=75&lon=-147&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 26: bluff (http://api.openweathermap.org/data/2.5/weather?lat=-64&lon=156&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 27: angra (http://api.openweathermap.org/data/2.5/weather?lat=38&lon=-27&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 28: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-51&lon=-126&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 29: kaputa (http://api.openweathermap.org/data/2.5/weather?lat=-8&lon=29&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 30: puerto escondido (http://api.openweathermap.org/data/2.5/weather?lat=13&lon=-98&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 31: vila velha (http://api.openweathermap.org/data/2.5/weather?lat=-31&lon=-23&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 32: marsa matruh (http://api.openweathermap.org/data/2.5/weather?lat=29&lon=28&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 33: ponta do sol (http://api.openweathermap.org/data/2.5/weather?lat=19&lon=-31&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 34: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=-35&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 35: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-66&lon=-65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 36: quatre cocos (http://api.openweathermap.org/data/2.5/weather?lat=-18&lon=66&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 37: kadykchan (http://api.openweathermap.org/data/2.5/weather?lat=62&lon=147&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 38: atuona (http://api.openweathermap.org/data/2.5/weather?lat=-3&lon=-120&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 39: amderma (http://api.openweathermap.org/data/2.5/weather?lat=89&lon=66&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 40: souillac (http://api.openweathermap.org/data/2.5/weather?lat=-38&lon=65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 41: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-86&lon=122&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 42: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=135&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 43: saskylakh (http://api.openweathermap.org/data/2.5/weather?lat=87&lon=113&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 44: liverpool (http://api.openweathermap.org/data/2.5/weather?lat=42&lon=-63&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 45: vigrestad (http://api.openweathermap.org/data/2.5/weather?lat=56&lon=4&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 46: urucara (http://api.openweathermap.org/data/2.5/weather?lat=0&lon=-59&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 47: hilo (http://api.openweathermap.org/data/2.5/weather?lat=11&lon=-155&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 48: srednekolymsk (http://api.openweathermap.org/data/2.5/weather?lat=68&lon=155&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 49: puerto carreno (http://api.openweathermap.org/data/2.5/weather?lat=6&lon=-65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 50: carnarvon (http://api.openweathermap.org/data/2.5/weather?lat=-19&lon=103&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 51: bealanana (http://api.openweathermap.org/data/2.5/weather?lat=-15&lon=49&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 52: hasaki (http://api.openweathermap.org/data/2.5/weather?lat=32&lon=154&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 53: sabang (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=96&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 54: belushya guba (http://api.openweathermap.org/data/2.5/weather?lat=77&lon=56&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 55: barentsburg (http://api.openweathermap.org/data/2.5/weather?lat=86&lon=6&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 56: saryozek (http://api.openweathermap.org/data/2.5/weather?lat=44&lon=78&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 57: bengkulu (http://api.openweathermap.org/data/2.5/weather?lat=-21&lon=92&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 58: tsihombe (http://api.openweathermap.org/data/2.5/weather?lat=-53&lon=50&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 59: nantucket (http://api.openweathermap.org/data/2.5/weather?lat=38&lon=-67&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 60: diffa (http://api.openweathermap.org/data/2.5/weather?lat=15&lon=13&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 61: port alfred (http://api.openweathermap.org/data/2.5/weather?lat=-81&lon=44&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 62: kavaratti (http://api.openweathermap.org/data/2.5/weather?lat=11&lon=73&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 63: palabuhanratu (http://api.openweathermap.org/data/2.5/weather?lat=-12&lon=104&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 64: palmeirais (http://api.openweathermap.org/data/2.5/weather?lat=-6&lon=-43&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 65: yellowknife (http://api.openweathermap.org/data/2.5/weather?lat=88&lon=-98&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 66: lompoc (http://api.openweathermap.org/data/2.5/weather?lat=24&lon=-133&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 67: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-68&lon=-57&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 68: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-75&lon=-134&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 69: albany (http://api.openweathermap.org/data/2.5/weather?lat=-53&lon=123&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 70: ostersund (http://api.openweathermap.org/data/2.5/weather?lat=63&lon=15&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 71: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-41&lon=-156&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 72: riyadh (http://api.openweathermap.org/data/2.5/weather?lat=21&lon=47&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 73: albany (http://api.openweathermap.org/data/2.5/weather?lat=-76&lon=102&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 74: iqaluit (http://api.openweathermap.org/data/2.5/weather?lat=58&lon=-71&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 75: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-68&lon=-38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 76: mahebourg (http://api.openweathermap.org/data/2.5/weather?lat=-38&lon=73&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 77: albany (http://api.openweathermap.org/data/2.5/weather?lat=-50&lon=123&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 78: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-52&lon=-143&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 79: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-84&lon=-31&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 80: bredasdorp (http://api.openweathermap.org/data/2.5/weather?lat=-69&lon=15&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 81: kibala (http://api.openweathermap.org/data/2.5/weather?lat=-11&lon=15&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 82: brae (http://api.openweathermap.org/data/2.5/weather?lat=64&lon=-1&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 83: bathsheba (http://api.openweathermap.org/data/2.5/weather?lat=17&lon=-53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 84: dawlatabad (http://api.openweathermap.org/data/2.5/weather?lat=36&lon=64&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 85: albany (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=96&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 86: barentsburg (http://api.openweathermap.org/data/2.5/weather?lat=86&lon=7&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 87: vestmannaeyjar (http://api.openweathermap.org/data/2.5/weather?lat=60&lon=-22&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 88: necochea (http://api.openweathermap.org/data/2.5/weather?lat=-44&lon=-56&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 89: sechura (http://api.openweathermap.org/data/2.5/weather?lat=-7&lon=-82&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 90: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-1&lon=-108&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 91: chokurdakh (http://api.openweathermap.org/data/2.5/weather?lat=71&lon=149&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 92: barrow (http://api.openweathermap.org/data/2.5/weather?lat=85&lon=-149&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 93: baruun-urt (http://api.openweathermap.org/data/2.5/weather?lat=46&lon=114&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 94: voznesenye (http://api.openweathermap.org/data/2.5/weather?lat=61&lon=35&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 95: samusu (http://api.openweathermap.org/data/2.5/weather?lat=-6&lon=-164&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 96: kodiak (http://api.openweathermap.org/data/2.5/weather?lat=55&lon=-146&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 97: kirakira (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=162&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 98: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=-114&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 99: mahebourg (http://api.openweathermap.org/data/2.5/weather?lat=-48&lon=78&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 100: alenquer (http://api.openweathermap.org/data/2.5/weather?lat=0&lon=-55&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 101: rafaela (http://api.openweathermap.org/data/2.5/weather?lat=-30&lon=-62&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 102: vila franca do campo (http://api.openweathermap.org/data/2.5/weather?lat=42&lon=-21&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 103: chhabra (http://api.openweathermap.org/data/2.5/weather?lat=25&lon=77&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 104: portland (http://api.openweathermap.org/data/2.5/weather?lat=-51&lon=135&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 105: nisia floresta (http://api.openweathermap.org/data/2.5/weather?lat=-5&lon=-31&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 106: castro (http://api.openweathermap.org/data/2.5/weather?lat=-43&lon=-105&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 107: makat (http://api.openweathermap.org/data/2.5/weather?lat=48&lon=55&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 108: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-62&lon=-86&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 109: tsihombe (http://api.openweathermap.org/data/2.5/weather?lat=-53&lon=51&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 110: mahebourg (http://api.openweathermap.org/data/2.5/weather?lat=-48&lon=74&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 111: fukue (http://api.openweathermap.org/data/2.5/weather?lat=34&lon=128&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 112: aykhal (http://api.openweathermap.org/data/2.5/weather?lat=68&lon=108&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 113: port elizabeth (http://api.openweathermap.org/data/2.5/weather?lat=-57&lon=31&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 114: qaanaaq (http://api.openweathermap.org/data/2.5/weather?lat=77&lon=-89&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 115: barbastro (http://api.openweathermap.org/data/2.5/weather?lat=42&lon=0&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 116: aranos (http://api.openweathermap.org/data/2.5/weather?lat=-25&lon=20&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 117: bereda (http://api.openweathermap.org/data/2.5/weather?lat=13&lon=54&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 118: utiroa (http://api.openweathermap.org/data/2.5/weather?lat=-3&lon=172&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 119: navahrudak (http://api.openweathermap.org/data/2.5/weather?lat=54&lon=26&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 120: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-79&lon=-52&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 121: gazojak (http://api.openweathermap.org/data/2.5/weather?lat=41&lon=62&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 122: hermanus (http://api.openweathermap.org/data/2.5/weather?lat=-79&lon=-8&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 123: sukhoy log (http://api.openweathermap.org/data/2.5/weather?lat=57&lon=62&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 124: constitucion (http://api.openweathermap.org/data/2.5/weather?lat=21&lon=-117&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 125: eskisehir (http://api.openweathermap.org/data/2.5/weather?lat=40&lon=31&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 126: puerto colombia (http://api.openweathermap.org/data/2.5/weather?lat=14&lon=-76&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 127: te anau (http://api.openweathermap.org/data/2.5/weather?lat=-41&lon=159&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 128: atuona (http://api.openweathermap.org/data/2.5/weather?lat=-10&lon=-137&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 129: geraldton (http://api.openweathermap.org/data/2.5/weather?lat=-33&lon=98&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 130: caravelas (http://api.openweathermap.org/data/2.5/weather?lat=-21&lon=-32&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 131: paamiut (http://api.openweathermap.org/data/2.5/weather?lat=59&lon=-52&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 132: tiksi (http://api.openweathermap.org/data/2.5/weather?lat=78&lon=132&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 133: tome (http://api.openweathermap.org/data/2.5/weather?lat=-36&lon=-73&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 134: jabiru (http://api.openweathermap.org/data/2.5/weather?lat=-12&lon=133&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 135: atuona (http://api.openweathermap.org/data/2.5/weather?lat=1&lon=-142&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 136: tuktoyaktuk (http://api.openweathermap.org/data/2.5/weather?lat=75&lon=-126&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 137: margate (http://api.openweathermap.org/data/2.5/weather?lat=-42&lon=41&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 138: atuona (http://api.openweathermap.org/data/2.5/weather?lat=5&lon=-132&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 139: bredasdorp (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=17&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 140: flin flon (http://api.openweathermap.org/data/2.5/weather?lat=60&lon=-102&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 141: kapaa (http://api.openweathermap.org/data/2.5/weather?lat=39&lon=-177&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 142: touros (http://api.openweathermap.org/data/2.5/weather?lat=1&lon=-29&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 143: lumphat (http://api.openweathermap.org/data/2.5/weather?lat=13&lon=107&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 144: noumea (http://api.openweathermap.org/data/2.5/weather?lat=-29&lon=163&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 145: torbay (http://api.openweathermap.org/data/2.5/weather?lat=43&lon=-48&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 146: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-42&lon=-121&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 147: mitu (http://api.openweathermap.org/data/2.5/weather?lat=2&lon=-69&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 148: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=23&lon=172&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 149: east london (http://api.openweathermap.org/data/2.5/weather?lat=-76&lon=52&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 150: hovd (http://api.openweathermap.org/data/2.5/weather?lat=46&lon=103&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 151: saskylakh (http://api.openweathermap.org/data/2.5/weather?lat=86&lon=115&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 152: saldanha (http://api.openweathermap.org/data/2.5/weather?lat=-44&lon=-6&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 153: klaksvik (http://api.openweathermap.org/data/2.5/weather?lat=65&lon=-6&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 154: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-74&lon=-79&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 155: tura (http://api.openweathermap.org/data/2.5/weather?lat=63&lon=100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 156: avarua (http://api.openweathermap.org/data/2.5/weather?lat=-74&lon=-166&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 157: hermanus (http://api.openweathermap.org/data/2.5/weather?lat=-54&lon=15&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 158: tanout (http://api.openweathermap.org/data/2.5/weather?lat=15&lon=9&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 159: challans (http://api.openweathermap.org/data/2.5/weather?lat=47&lon=-2&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 160: atuona (http://api.openweathermap.org/data/2.5/weather?lat=9&lon=-133&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 161: dikson (http://api.openweathermap.org/data/2.5/weather?lat=86&lon=70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 162: yellowknife (http://api.openweathermap.org/data/2.5/weather?lat=68&lon=-119&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 163: zyryanka (http://api.openweathermap.org/data/2.5/weather?lat=67&lon=149&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 164: yelizovo (http://api.openweathermap.org/data/2.5/weather?lat=53&lon=158&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 165: mayor pablo lagerenza (http://api.openweathermap.org/data/2.5/weather?lat=-20&lon=-60&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 166: rio gallegos (http://api.openweathermap.org/data/2.5/weather?lat=-50&lon=-71&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 167: jamestown (http://api.openweathermap.org/data/2.5/weather?lat=-26&lon=-11&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 168: pevek (http://api.openweathermap.org/data/2.5/weather?lat=88&lon=174&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 169: saint-philippe (http://api.openweathermap.org/data/2.5/weather?lat=-74&lon=75&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 170: quatre cocos (http://api.openweathermap.org/data/2.5/weather?lat=-17&lon=65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 171: hobyo (http://api.openweathermap.org/data/2.5/weather?lat=4&lon=48&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 172: fastiv (http://api.openweathermap.org/data/2.5/weather?lat=50&lon=30&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 173: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=-100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 174: saskylakh (http://api.openweathermap.org/data/2.5/weather?lat=71&lon=113&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 175: hasaki (http://api.openweathermap.org/data/2.5/weather?lat=35&lon=147&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 176: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-72&lon=-47&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 177: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-83&lon=-18&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 178: tumbotino (http://api.openweathermap.org/data/2.5/weather?lat=56&lon=43&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 179: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=17&lon=162&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 180: provideniya (http://api.openweathermap.org/data/2.5/weather?lat=63&lon=-176&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 181: port alfred (http://api.openweathermap.org/data/2.5/weather?lat=-86&lon=46&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 182: hilo (http://api.openweathermap.org/data/2.5/weather?lat=4&lon=-155&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 183: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-54&lon=-65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 184: copiapo (http://api.openweathermap.org/data/2.5/weather?lat=-27&lon=-71&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 185: mahebourg (http://api.openweathermap.org/data/2.5/weather?lat=-34&lon=71&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 186: yumen (http://api.openweathermap.org/data/2.5/weather?lat=40&lon=97&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 187: ancud (http://api.openweathermap.org/data/2.5/weather?lat=-41&lon=-106&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 188: yellowknife (http://api.openweathermap.org/data/2.5/weather?lat=72&lon=-108&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 189: dikson (http://api.openweathermap.org/data/2.5/weather?lat=76&lon=82&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 190: charters towers (http://api.openweathermap.org/data/2.5/weather?lat=-22&lon=145&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 191: sentyabrskiy (http://api.openweathermap.org/data/2.5/weather?lat=41&lon=151&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 192: barentsburg (http://api.openweathermap.org/data/2.5/weather?lat=89&lon=3&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 193: abu kamal (http://api.openweathermap.org/data/2.5/weather?lat=33&lon=40&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 194: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=-151&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 195: tiksi (http://api.openweathermap.org/data/2.5/weather?lat=74&lon=123&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 196: magadan (http://api.openweathermap.org/data/2.5/weather?lat=58&lon=151&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 197: jamestown (http://api.openweathermap.org/data/2.5/weather?lat=-27&lon=1&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 198: lorengau (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=150&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 199: atuona (http://api.openweathermap.org/data/2.5/weather?lat=12&lon=-132&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 200: guerrero negro (http://api.openweathermap.org/data/2.5/weather?lat=13&lon=-130&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 201: tsihombe (http://api.openweathermap.org/data/2.5/weather?lat=-36&lon=42&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 202: cape town (http://api.openweathermap.org/data/2.5/weather?lat=-40&lon=10&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 203: ixtapa (http://api.openweathermap.org/data/2.5/weather?lat=10&lon=-105&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 204: zlatarica (http://api.openweathermap.org/data/2.5/weather?lat=43&lon=26&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 205: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-77&lon=-132&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 206: castro (http://api.openweathermap.org/data/2.5/weather?lat=-44&lon=-97&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 207: albany (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=106&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 208: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-58&lon=133&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 209: vestmannaeyjar (http://api.openweathermap.org/data/2.5/weather?lat=59&lon=-21&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 210: pismo beach (http://api.openweathermap.org/data/2.5/weather?lat=35&lon=-121&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 211: hobart (http://api.openweathermap.org/data/2.5/weather?lat=-76&lon=144&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 212: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-36&lon=-178&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 213: saint-pierre (http://api.openweathermap.org/data/2.5/weather?lat=40&lon=-53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 214: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-67&lon=-33&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 215: taldan (http://api.openweathermap.org/data/2.5/weather?lat=53&lon=125&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 216: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=134&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 217: tsihombe (http://api.openweathermap.org/data/2.5/weather?lat=-47&lon=47&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 218: attawapiskat (http://api.openweathermap.org/data/2.5/weather?lat=55&lon=-81&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 219: linhares (http://api.openweathermap.org/data/2.5/weather?lat=-20&lon=-38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 220: yar-sale (http://api.openweathermap.org/data/2.5/weather?lat=76&lon=70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 221: sambava (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=52&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 222: port lincoln (http://api.openweathermap.org/data/2.5/weather?lat=-41&lon=133&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 223: azanka (http://api.openweathermap.org/data/2.5/weather?lat=58&lon=65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 224: svit (http://api.openweathermap.org/data/2.5/weather?lat=49&lon=20&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 225: souillac (http://api.openweathermap.org/data/2.5/weather?lat=-57&lon=76&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 226: castro (http://api.openweathermap.org/data/2.5/weather?lat=-43&lon=-77&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 227: naze (http://api.openweathermap.org/data/2.5/weather?lat=26&lon=135&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 228: bur gabo (http://api.openweathermap.org/data/2.5/weather?lat=-1&lon=41&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 229: provideniya (http://api.openweathermap.org/data/2.5/weather?lat=51&lon=-177&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 230: port elizabeth (http://api.openweathermap.org/data/2.5/weather?lat=-68&lon=34&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 231: grand river south east (http://api.openweathermap.org/data/2.5/weather?lat=-21&lon=67&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 232: roma (http://api.openweathermap.org/data/2.5/weather?lat=-26&lon=149&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 233: mandalgovi (http://api.openweathermap.org/data/2.5/weather?lat=45&lon=108&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 234: valleyview (http://api.openweathermap.org/data/2.5/weather?lat=55&lon=-118&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 235: ribeira grande (http://api.openweathermap.org/data/2.5/weather?lat=38&lon=-29&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 236: heihe (http://api.openweathermap.org/data/2.5/weather?lat=51&lon=126&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 237: port-gentil (http://api.openweathermap.org/data/2.5/weather?lat=-4&lon=3&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 238: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=11&lon=178&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 239: rocha (http://api.openweathermap.org/data/2.5/weather?lat=-44&lon=-47&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 240: mindelo (http://api.openweathermap.org/data/2.5/weather?lat=16&lon=-25&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 241: albany (http://api.openweathermap.org/data/2.5/weather?lat=-85&lon=100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 242: yellowknife (http://api.openweathermap.org/data/2.5/weather?lat=83&lon=-104&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 243: port lincoln (http://api.openweathermap.org/data/2.5/weather?lat=-39&lon=132&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 244: erenhot (http://api.openweathermap.org/data/2.5/weather?lat=43&lon=114&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 245: bluff (http://api.openweathermap.org/data/2.5/weather?lat=-67&lon=170&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 246: thompson (http://api.openweathermap.org/data/2.5/weather?lat=59&lon=-94&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 247: port alfred (http://api.openweathermap.org/data/2.5/weather?lat=-63&lon=38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 248: bowen (http://api.openweathermap.org/data/2.5/weather?lat=-19&lon=149&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 249: marau (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=-38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 250: hasaki (http://api.openweathermap.org/data/2.5/weather?lat=34&lon=148&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 251: bethel (http://api.openweathermap.org/data/2.5/weather?lat=42&lon=-171&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 252: saldanha (http://api.openweathermap.org/data/2.5/weather?lat=-47&lon=-10&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 253: srednekolymsk (http://api.openweathermap.org/data/2.5/weather?lat=72&lon=155&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 254: buraydah (http://api.openweathermap.org/data/2.5/weather?lat=26&lon=45&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 255: barrow (http://api.openweathermap.org/data/2.5/weather?lat=71&lon=-162&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 256: tazovskiy (http://api.openweathermap.org/data/2.5/weather?lat=67&lon=79&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 257: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-18&lon=-104&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 258: provideniya (http://api.openweathermap.org/data/2.5/weather?lat=54&lon=-176&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 259: alofi (http://api.openweathermap.org/data/2.5/weather?lat=-16&lon=-168&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 260: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-36&lon=100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 261: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-56&lon=-118&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 262: kodiak (http://api.openweathermap.org/data/2.5/weather?lat=51&lon=-147&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 263: azimur (http://api.openweathermap.org/data/2.5/weather?lat=35&lon=-10&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 264: grand gaube (http://api.openweathermap.org/data/2.5/weather?lat=-13&lon=62&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 265: amderma (http://api.openweathermap.org/data/2.5/weather?lat=80&lon=66&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 266: esperance (http://api.openweathermap.org/data/2.5/weather?lat=-37&lon=126&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 267: port-gentil (http://api.openweathermap.org/data/2.5/weather?lat=-1&lon=8&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 268: luanda (http://api.openweathermap.org/data/2.5/weather?lat=-10&lon=11&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 269: dikson (http://api.openweathermap.org/data/2.5/weather?lat=86&lon=78&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 270: saint-philippe (http://api.openweathermap.org/data/2.5/weather?lat=-54&lon=67&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 271: albany (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=96&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 272: zemio (http://api.openweathermap.org/data/2.5/weather?lat=4&lon=26&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 273: neiafu (http://api.openweathermap.org/data/2.5/weather?lat=-18&lon=-174&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 274: taolanaro (http://api.openweathermap.org/data/2.5/weather?lat=-79&lon=60&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 275: barrow (http://api.openweathermap.org/data/2.5/weather?lat=79&lon=-150&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 276: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-43&lon=100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 277: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-72&lon=-67&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 278: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-40&lon=-176&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 279: tumannyy (http://api.openweathermap.org/data/2.5/weather?lat=82&lon=40&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 280: samarai (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=150&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 281: cidreira (http://api.openweathermap.org/data/2.5/weather?lat=-34&lon=-46&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 282: bredasdorp (http://api.openweathermap.org/data/2.5/weather?lat=-66&lon=23&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 283: tuktoyaktuk (http://api.openweathermap.org/data/2.5/weather?lat=73&lon=-131&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 284: upernavik (http://api.openweathermap.org/data/2.5/weather?lat=87&lon=-54&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 285: yumen (http://api.openweathermap.org/data/2.5/weather?lat=40&lon=96&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 286: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-83&lon=-54&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 287: jamestown (http://api.openweathermap.org/data/2.5/weather?lat=-35&lon=-5&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 288: bethel (http://api.openweathermap.org/data/2.5/weather?lat=65&lon=-161&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 289: kahului (http://api.openweathermap.org/data/2.5/weather?lat=37&lon=-148&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 290: turtas (http://api.openweathermap.org/data/2.5/weather?lat=59&lon=70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 291: namibe (http://api.openweathermap.org/data/2.5/weather?lat=-16&lon=11&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 292: barinas (http://api.openweathermap.org/data/2.5/weather?lat=8&lon=-70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 293: pindi gheb (http://api.openweathermap.org/data/2.5/weather?lat=33&lon=72&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 294: ambo (http://api.openweathermap.org/data/2.5/weather?lat=-10&lon=-76&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 295: carnarvon (http://api.openweathermap.org/data/2.5/weather?lat=-29&lon=100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 296: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-27&lon=-125&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 297: hami (http://api.openweathermap.org/data/2.5/weather?lat=45&lon=95&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 298: upernavik (http://api.openweathermap.org/data/2.5/weather?lat=72&lon=-59&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 299: vila franca do campo (http://api.openweathermap.org/data/2.5/weather?lat=42&lon=-22&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 300: hithadhoo (http://api.openweathermap.org/data/2.5/weather?lat=-13&lon=71&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 301: half moon bay (http://api.openweathermap.org/data/2.5/weather?lat=33&lon=-131&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 302: lebu (http://api.openweathermap.org/data/2.5/weather?lat=-32&lon=-101&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 303: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-79&lon=-51&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 304: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=14&lon=172&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 305: coquimbo (http://api.openweathermap.org/data/2.5/weather?lat=-29&lon=-73&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 306: belushya guba (http://api.openweathermap.org/data/2.5/weather?lat=75&lon=48&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 307: tuktoyaktuk (http://api.openweathermap.org/data/2.5/weather?lat=88&lon=-138&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 308: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=14&lon=179&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 309: gizo (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=156&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 310: vila velha (http://api.openweathermap.org/data/2.5/weather?lat=-24&lon=-30&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 311: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-53&lon=-172&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 312: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-52&lon=95&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 313: pevek (http://api.openweathermap.org/data/2.5/weather?lat=83&lon=165&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 314: tumannyy (http://api.openweathermap.org/data/2.5/weather?lat=82&lon=38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 315: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-56&lon=91&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 316: belushya guba (http://api.openweathermap.org/data/2.5/weather?lat=82&lon=53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 317: hermanus (http://api.openweathermap.org/data/2.5/weather?lat=-75&lon=13&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 318: zhigansk (http://api.openweathermap.org/data/2.5/weather?lat=68&lon=121&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 319: kandi (http://api.openweathermap.org/data/2.5/weather?lat=11&lon=3&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 320: arraial do cabo (http://api.openweathermap.org/data/2.5/weather?lat=-45&lon=-27&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 321: chokurdakh (http://api.openweathermap.org/data/2.5/weather?lat=88&lon=153&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 322: cape town (http://api.openweathermap.org/data/2.5/weather?lat=-68&lon=-13&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 323: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=30&lon=178&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 324: caravelas (http://api.openweathermap.org/data/2.5/weather?lat=-19&lon=-34&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 325: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-18&lon=-107&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 326: aflu (http://api.openweathermap.org/data/2.5/weather?lat=33&lon=0&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 327: ostrovnoy (http://api.openweathermap.org/data/2.5/weather?lat=73&lon=42&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 328: cape town (http://api.openweathermap.org/data/2.5/weather?lat=-58&lon=-11&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 329: mys shmidta (http://api.openweathermap.org/data/2.5/weather?lat=72&lon=-174&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 330: yulara (http://api.openweathermap.org/data/2.5/weather?lat=-22&lon=129&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 331: dakar (http://api.openweathermap.org/data/2.5/weather?lat=16&lon=-19&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 332: namibe (http://api.openweathermap.org/data/2.5/weather?lat=-13&lon=4&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 333: chippewa falls (http://api.openweathermap.org/data/2.5/weather?lat=46&lon=-91&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 334: tasiilaq (http://api.openweathermap.org/data/2.5/weather?lat=61&lon=-34&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 335: pochutla (http://api.openweathermap.org/data/2.5/weather?lat=14&lon=-96&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 336: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-11&lon=-113&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 337: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=-49&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 338: atuona (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=-142&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 339: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-88&lon=-50&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 340: port elizabeth (http://api.openweathermap.org/data/2.5/weather?lat=-80&lon=33&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 341: eureka (http://api.openweathermap.org/data/2.5/weather?lat=41&lon=-130&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 342: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-44&lon=94&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 343: mahebourg (http://api.openweathermap.org/data/2.5/weather?lat=-49&lon=75&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 344: carnarvon (http://api.openweathermap.org/data/2.5/weather?lat=-21&lon=106&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 345: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-78&lon=-119&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 346: ponta do sol (http://api.openweathermap.org/data/2.5/weather?lat=19&lon=-33&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 347: cape town (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=-13&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 348: fort wellington (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=-57&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 349: cherskiy (http://api.openweathermap.org/data/2.5/weather?lat=78&lon=164&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 350: waingapu (http://api.openweathermap.org/data/2.5/weather?lat=-10&lon=120&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 351: jamestown (http://api.openweathermap.org/data/2.5/weather?lat=-14&lon=-6&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 352: kodinar (http://api.openweathermap.org/data/2.5/weather?lat=19&lon=70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 353: sabla (http://api.openweathermap.org/data/2.5/weather?lat=43&lon=29&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 354: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-56&lon=-179&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 355: gamba (http://api.openweathermap.org/data/2.5/weather?lat=-8&lon=2&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 356: taolanaro (http://api.openweathermap.org/data/2.5/weather?lat=-75&lon=64&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 357: saint-francois (http://api.openweathermap.org/data/2.5/weather?lat=20&lon=-53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 358: hobart (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=151&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 359: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-77&lon=129&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 360: belushya guba (http://api.openweathermap.org/data/2.5/weather?lat=75&lon=45&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 361: ancud (http://api.openweathermap.org/data/2.5/weather?lat=-42&lon=-75&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 362: samalaeulu (http://api.openweathermap.org/data/2.5/weather?lat=-7&lon=-169&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 363: puerto escondido (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=-100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 364: rudsar (http://api.openweathermap.org/data/2.5/weather?lat=38&lon=51&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 365: avarua (http://api.openweathermap.org/data/2.5/weather?lat=-24&lon=-159&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 366: port hedland (http://api.openweathermap.org/data/2.5/weather?lat=-18&lon=118&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 367: nanortalik (http://api.openweathermap.org/data/2.5/weather?lat=62&lon=-44&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 368: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-86&lon=78&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 369: belushya guba (http://api.openweathermap.org/data/2.5/weather?lat=87&lon=51&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 370: hobart (http://api.openweathermap.org/data/2.5/weather?lat=-66&lon=147&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 371: gornopravdinsk (http://api.openweathermap.org/data/2.5/weather?lat=60&lon=70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 372: tateyama (http://api.openweathermap.org/data/2.5/weather?lat=22&lon=144&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 373: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-24&lon=-142&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 374: taltal (http://api.openweathermap.org/data/2.5/weather?lat=-25&lon=-70&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 375: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=-89&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 376: ca mau (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=105&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 377: kaitangata (http://api.openweathermap.org/data/2.5/weather?lat=-65&lon=178&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 378: isangel (http://api.openweathermap.org/data/2.5/weather?lat=-22&lon=171&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 379: samusu (http://api.openweathermap.org/data/2.5/weather?lat=-9&lon=-168&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 380: east london (http://api.openweathermap.org/data/2.5/weather?lat=-42&lon=35&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 381: tasbuget (http://api.openweathermap.org/data/2.5/weather?lat=43&lon=65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 382: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-27&lon=-103&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 383: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-65&lon=-136&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 384: charters towers (http://api.openweathermap.org/data/2.5/weather?lat=-22&lon=145&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 385: wajima (http://api.openweathermap.org/data/2.5/weather?lat=40&lon=136&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 386: fortuna (http://api.openweathermap.org/data/2.5/weather?lat=38&lon=-135&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 387: upernavik (http://api.openweathermap.org/data/2.5/weather?lat=82&lon=-55&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 388: barentsburg (http://api.openweathermap.org/data/2.5/weather?lat=77&lon=15&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 389: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-27&lon=-139&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 390: krasnyy oktyabr (http://api.openweathermap.org/data/2.5/weather?lat=56&lon=65&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 391: clyde river (http://api.openweathermap.org/data/2.5/weather?lat=70&lon=-81&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 392: camacha (http://api.openweathermap.org/data/2.5/weather?lat=36&lon=-14&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 393: ruatoria (http://api.openweathermap.org/data/2.5/weather?lat=-36&lon=178&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 394: chaihe (http://api.openweathermap.org/data/2.5/weather?lat=45&lon=130&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 395: pisco (http://api.openweathermap.org/data/2.5/weather?lat=-19&lon=-84&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 396: ribeira grande (http://api.openweathermap.org/data/2.5/weather?lat=41&lon=-33&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 397: thompson (http://api.openweathermap.org/data/2.5/weather?lat=69&lon=-90&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 398: lebu (http://api.openweathermap.org/data/2.5/weather?lat=-29&lon=-103&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 399: avarua (http://api.openweathermap.org/data/2.5/weather?lat=-50&lon=-164&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 400: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=5&lon=163&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 401: longyearbyen (http://api.openweathermap.org/data/2.5/weather?lat=82&lon=22&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 402: kaitangata (http://api.openweathermap.org/data/2.5/weather?lat=-67&lon=178&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 403: cherskiy (http://api.openweathermap.org/data/2.5/weather?lat=73&lon=162&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 404: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-47&lon=-153&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 405: korhogo (http://api.openweathermap.org/data/2.5/weather?lat=9&lon=-6&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 406: illoqqortoormiut (http://api.openweathermap.org/data/2.5/weather?lat=73&lon=-17&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 407: khatanga (http://api.openweathermap.org/data/2.5/weather?lat=78&lon=108&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 408: xichang (http://api.openweathermap.org/data/2.5/weather?lat=28&lon=101&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 409: westport (http://api.openweathermap.org/data/2.5/weather?lat=56&lon=-15&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 410: taolanaro (http://api.openweathermap.org/data/2.5/weather?lat=-63&lon=63&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 411: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-78&lon=-165&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 412: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-49&lon=140&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 413: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-27&lon=-150&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 414: seymchan (http://api.openweathermap.org/data/2.5/weather?lat=64&lon=152&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 415: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-64&lon=-114&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 416: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-76&lon=-145&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 417: fuxin (http://api.openweathermap.org/data/2.5/weather?lat=43&lon=121&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 418: bluff (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=164&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 419: sumbawa (http://api.openweathermap.org/data/2.5/weather?lat=-7&lon=118&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 420: kaitangata (http://api.openweathermap.org/data/2.5/weather?lat=-71&lon=179&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 421: chuy (http://api.openweathermap.org/data/2.5/weather?lat=-46&lon=-43&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 422: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-61&lon=-173&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 423: hilo (http://api.openweathermap.org/data/2.5/weather?lat=14&lon=-152&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 424: bredasdorp (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=24&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 425: bathsheba (http://api.openweathermap.org/data/2.5/weather?lat=16&lon=-55&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 426: san patricio (http://api.openweathermap.org/data/2.5/weather?lat=5&lon=-112&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 427: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-55&lon=-141&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 428: porto novo (http://api.openweathermap.org/data/2.5/weather?lat=16&lon=-30&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 429: yulara (http://api.openweathermap.org/data/2.5/weather?lat=-29&lon=132&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 430: chuy (http://api.openweathermap.org/data/2.5/weather?lat=-64&lon=-23&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 431: chokurdakh (http://api.openweathermap.org/data/2.5/weather?lat=89&lon=146&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 432: samarai (http://api.openweathermap.org/data/2.5/weather?lat=-11&lon=152&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 433: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-16&lon=-118&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 434: narsaq (http://api.openweathermap.org/data/2.5/weather?lat=78&lon=-62&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 435: thompson (http://api.openweathermap.org/data/2.5/weather?lat=71&lon=-94&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 436: kapaa (http://api.openweathermap.org/data/2.5/weather?lat=33&lon=-166&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 437: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-85&lon=-87&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 438: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-65&lon=-90&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 439: hofn (http://api.openweathermap.org/data/2.5/weather?lat=58&lon=-16&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 440: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-30&lon=-174&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 441: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=5&lon=168&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 442: tingo maria (http://api.openweathermap.org/data/2.5/weather?lat=-9&lon=-76&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 443: krasnoselkup (http://api.openweathermap.org/data/2.5/weather?lat=63&lon=84&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 444: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-73&lon=-22&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 445: rikitea (http://api.openweathermap.org/data/2.5/weather?lat=-70&lon=-130&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 446: butaritari (http://api.openweathermap.org/data/2.5/weather?lat=13&lon=159&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 447: mar del plata (http://api.openweathermap.org/data/2.5/weather?lat=-56&lon=-43&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 448: lebu (http://api.openweathermap.org/data/2.5/weather?lat=-34&lon=-100&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 449: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=-85&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 450: warqla (http://api.openweathermap.org/data/2.5/weather?lat=30&lon=8&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 451: duz (http://api.openweathermap.org/data/2.5/weather?lat=32&lon=9&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 452: east london (http://api.openweathermap.org/data/2.5/weather?lat=-72&lon=54&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 453: port alfred (http://api.openweathermap.org/data/2.5/weather?lat=-66&lon=38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 454: camapua (http://api.openweathermap.org/data/2.5/weather?lat=-19&lon=-53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 455: thompson (http://api.openweathermap.org/data/2.5/weather?lat=77&lon=-97&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 456: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-88&lon=-156&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 457: albany (http://api.openweathermap.org/data/2.5/weather?lat=-65&lon=106&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 458: goderich (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=-17&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 459: itaituba (http://api.openweathermap.org/data/2.5/weather?lat=-6&lon=-55&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 460: ambilobe (http://api.openweathermap.org/data/2.5/weather?lat=-11&lon=52&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 461: tawkar (http://api.openweathermap.org/data/2.5/weather?lat=17&lon=38&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 462: arraial do cabo (http://api.openweathermap.org/data/2.5/weather?lat=-31&lon=-31&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 463: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=-83&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 464: taolanaro (http://api.openweathermap.org/data/2.5/weather?lat=-90&lon=69&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 465: klaksvik (http://api.openweathermap.org/data/2.5/weather?lat=71&lon=-6&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 466: puerto ayora (http://api.openweathermap.org/data/2.5/weather?lat=-16&lon=-109&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 467: praia (http://api.openweathermap.org/data/2.5/weather?lat=9&lon=-23&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 468: khatanga (http://api.openweathermap.org/data/2.5/weather?lat=79&lon=108&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 469: new norfolk (http://api.openweathermap.org/data/2.5/weather?lat=-88&lon=123&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 470: imuris (http://api.openweathermap.org/data/2.5/weather?lat=31&lon=-111&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 471: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-84&lon=-69&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 472: bluff (http://api.openweathermap.org/data/2.5/weather?lat=-66&lon=172&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 473: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-48&lon=86&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 474: vaini (http://api.openweathermap.org/data/2.5/weather?lat=-89&lon=-174&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 475: kavaratti (http://api.openweathermap.org/data/2.5/weather?lat=10&lon=63&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 476: nenjiang (http://api.openweathermap.org/data/2.5/weather?lat=49&lon=126&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 477: mataura (http://api.openweathermap.org/data/2.5/weather?lat=-43&lon=-144&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 478: bethel (http://api.openweathermap.org/data/2.5/weather?lat=45&lon=-166&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 479: saskylakh (http://api.openweathermap.org/data/2.5/weather?lat=76&lon=109&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 480: lompoc (http://api.openweathermap.org/data/2.5/weather?lat=29&lon=-126&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 481: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-65&lon=-78&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 482: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-79&lon=-40&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 483: kapaa (http://api.openweathermap.org/data/2.5/weather?lat=39&lon=-167&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 484: aasiaat (http://api.openweathermap.org/data/2.5/weather?lat=70&lon=-53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 485: upernavik (http://api.openweathermap.org/data/2.5/weather?lat=86&lon=-53&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 486: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-52&lon=101&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 487: dingle (http://api.openweathermap.org/data/2.5/weather?lat=49&lon=-18&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 488: punta arenas (http://api.openweathermap.org/data/2.5/weather?lat=-75&lon=-94&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 489: busselton (http://api.openweathermap.org/data/2.5/weather?lat=-57&lon=81&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 490: bluff (http://api.openweathermap.org/data/2.5/weather?lat=-75&lon=172&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 491: yellowknife (http://api.openweathermap.org/data/2.5/weather?lat=66&lon=-113&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 492: ribeira grande (http://api.openweathermap.org/data/2.5/weather?lat=38&lon=-42&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 493: kapaa (http://api.openweathermap.org/data/2.5/weather?lat=21&lon=-160&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 494: ushuaia (http://api.openweathermap.org/data/2.5/weather?lat=-68&lon=-52&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 495: tiebissou (http://api.openweathermap.org/data/2.5/weather?lat=7&lon=-5&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 496: albany (http://api.openweathermap.org/data/2.5/weather?lat=-64&lon=115&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 497: midland (http://api.openweathermap.org/data/2.5/weather?lat=45&lon=-80&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 498: grindavik (http://api.openweathermap.org/data/2.5/weather?lat=59&lon=-24&APPID=3d2e93d0c041ca648839058021e3afe2)
    Call 499: papayal (http://api.openweathermap.org/data/2.5/weather?lat=-4&lon=-81&APPID=3d2e93d0c041ca648839058021e3afe2)
    
    
