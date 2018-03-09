
Observation Trend # 1 - Temperature increases as it approaches the equator.

Observation Trend # 2 - Humidity and windspeed(falls under 10mph) around Equator seems to be similar to the parts of it.

Observation Trend # 3 - Cloudiness seems well spread out for this day.



```python
#Import Dependencies
import numpy as np
import pandas as pd
import requests
import json
import matplotlib.pyplot as plt
import seaborn as sns
import openweathermapy.core as owm
from citipy import citipy
from conf import api_key 
from random import uniform 
```


```python
# Used citipy to associate them with nearest city
location = pd.DataFrame()
location['lat'] = [uniform(-90,90) for x in range(1400)]
location['lng'] = [uniform(-180, 180) for x in range(1400)]
location['Closest City'] = " "
location['Country code'] = " " 
for index,row in location.iterrows():
    city = citipy.nearest_city(row["lat"],row["lng"])
    location.set_value(index,"Closest City",city.city_name)
    location.set_value(index,"Country code",city.country_code.upper()) 
location.head()
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
      <th>lng</th>
      <th>Closest City</th>
      <th>Country code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-34.145215</td>
      <td>-47.603838</td>
      <td>cidreira</td>
      <td>BR</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-59.135368</td>
      <td>52.842476</td>
      <td>taolanaro</td>
      <td>MG</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-81.153149</td>
      <td>140.581318</td>
      <td>hobart</td>
      <td>AU</td>
    </tr>
    <tr>
      <th>3</th>
      <td>73.853839</td>
      <td>166.766659</td>
      <td>pevek</td>
      <td>RU</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19.282787</td>
      <td>70.329105</td>
      <td>kodinar</td>
      <td>IN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Duplicates are dropped from closest city and stored in a variable "locat"
locat = location.drop_duplicates(['Closest City'])
```


```python
# Duplicates are dropped from Country code : for looping to get country count
loct = location.drop_duplicates(['Country code'])
```


```python
# base url 
base_url = "http://api.openweathermap.org/data/2.5/weather"
```


```python
# Looping through cities and country code to get counts of Set ID and records
counter_city = 0
for indx,rw in loct.iterrows():
    counter_city = counter_city + 1
    counter_country = 0
    for index,row in locat.iterrows():
        if rw['Country code'] == row['Country code']:
            counter_country = counter_country + 1
            print(f"Processing Records {counter_city} of Set {counter_country} | {row['Closest City']}")
            
            # set up a parameters dictionary
            params = {"units":"Imperial", "APPID" :api_key, "q" :row["Closest City"]}
            
            # run a request using our params dictionary 
            city_weather_resp = requests.get(base_url,params)
            print(city_weather_resp.url)
            
            # convert response to json
            city_weather_res  = city_weather_resp.json() 
            locat.set_value(index,"Lat",city_weather_res.get("coord",{}).get("lat"))
            locat.set_value(index,"Lng",city_weather_res.get("coord",{}).get("lon"))
            locat.set_value(index,"Temperature",city_weather_res.get("main",{}).get("temp_max"))
            locat.set_value(index,"Wind speed",city_weather_res.get("wind",{}).get("speed"))
            locat.set_value(index,"Humidity",city_weather_res.get("main",{}).get("humidity"))
            locat.set_value(index,"Cloudiness",city_weather_res.get("clouds",{}).get("all")) 
print("-----------------------------")
print("Data Retrieval Complete")
print("-----------------------------")
```

    Processing Records 1 of Set 1 | cidreira
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cidreira
    

    C:\Users\Saranya\AppData\Local\conda\conda\envs\PythonData\lib\site-packages\pandas\core\indexing.py:337: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      self.obj[key] = _infer_fill_value(value)
    C:\Users\Saranya\AppData\Local\conda\conda\envs\PythonData\lib\site-packages\pandas\core\indexing.py:517: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      self.obj[item] = s
    

    Processing Records 1 of Set 2 | touros
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=touros
    Processing Records 1 of Set 3 | morros
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=morros
    Processing Records 1 of Set 4 | cabedelo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cabedelo
    Processing Records 1 of Set 5 | arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=arraial+do+cabo
    Processing Records 1 of Set 6 | ariquemes
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ariquemes
    Processing Records 1 of Set 7 | cacoal
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cacoal
    Processing Records 1 of Set 8 | sao miguel do oeste
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sao+miguel+do+oeste
    Processing Records 1 of Set 9 | caravelas
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=caravelas
    Processing Records 1 of Set 10 | orlandia
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=orlandia
    Processing Records 1 of Set 11 | conde
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=conde
    Processing Records 1 of Set 12 | vila velha
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vila+velha
    Processing Records 1 of Set 13 | sao joao da barra
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sao+joao+da+barra
    Processing Records 1 of Set 14 | moji-mirim
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=moji-mirim
    Processing Records 1 of Set 15 | pauini
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pauini
    Processing Records 1 of Set 16 | palmital
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=palmital
    Processing Records 1 of Set 17 | salvador
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=salvador
    Processing Records 1 of Set 18 | caxias
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=caxias
    Processing Records 1 of Set 19 | caceres
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=caceres
    Processing Records 1 of Set 20 | obidos
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=obidos
    Processing Records 1 of Set 21 | belmonte
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=belmonte
    Processing Records 1 of Set 22 | benjamin constant
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=benjamin+constant
    Processing Records 1 of Set 23 | altamira
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=altamira
    Processing Records 1 of Set 24 | alta floresta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=alta+floresta
    Processing Records 1 of Set 25 | maragogi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=maragogi
    Processing Records 1 of Set 26 | guanambi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=guanambi
    Processing Records 1 of Set 27 | america dourada
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=america+dourada
    Processing Records 1 of Set 28 | uarini
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=uarini
    Processing Records 1 of Set 29 | manoel urbano
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=manoel+urbano
    Processing Records 2 of Set 1 | taolanaro
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=taolanaro
    Processing Records 2 of Set 2 | antsohihy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=antsohihy
    Processing Records 2 of Set 3 | manakara
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=manakara
    Processing Records 2 of Set 4 | tsihombe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tsihombe
    Processing Records 2 of Set 5 | ampanihy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ampanihy
    Processing Records 3 of Set 1 | hobart
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hobart
    Processing Records 3 of Set 2 | kununurra
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kununurra
    Processing Records 3 of Set 3 | karratha
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=karratha
    Processing Records 3 of Set 4 | busselton
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=busselton
    Processing Records 3 of Set 5 | new norfolk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=new+norfolk
    Processing Records 3 of Set 6 | nelson bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nelson+bay
    Processing Records 3 of Set 7 | mareeba
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mareeba
    Processing Records 3 of Set 8 | albany
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=albany
    Processing Records 3 of Set 9 | port lincoln
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+lincoln
    Processing Records 3 of Set 10 | carnarvon
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=carnarvon
    Processing Records 3 of Set 11 | geraldton
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=geraldton
    Processing Records 3 of Set 12 | nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nhulunbuy
    Processing Records 3 of Set 13 | roebourne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=roebourne
    Processing Records 3 of Set 14 | burnie
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=burnie
    Processing Records 3 of Set 15 | batemans bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=batemans+bay
    Processing Records 3 of Set 16 | byron bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=byron+bay
    Processing Records 3 of Set 17 | ballina
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ballina
    Processing Records 3 of Set 18 | townsville
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=townsville
    Processing Records 3 of Set 19 | portland
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=portland
    Processing Records 3 of Set 20 | katherine
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=katherine
    Processing Records 3 of Set 21 | yulara
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yulara
    Processing Records 3 of Set 22 | port hedland
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+hedland
    Processing Records 3 of Set 23 | yeppoon
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yeppoon
    Processing Records 3 of Set 24 | roma
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=roma
    Processing Records 3 of Set 25 | mount gambier
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mount+gambier
    Processing Records 3 of Set 26 | mildura
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mildura
    Processing Records 3 of Set 27 | esperance
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=esperance
    Processing Records 4 of Set 1 | pevek
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pevek
    Processing Records 4 of Set 2 | aykhal
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=aykhal
    Processing Records 4 of Set 3 | safakulevo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=safakulevo
    Processing Records 4 of Set 4 | provideniya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=provideniya
    Processing Records 4 of Set 5 | ishnya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ishnya
    Processing Records 4 of Set 6 | tura
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tura
    Processing Records 4 of Set 7 | tiksi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tiksi
    Processing Records 4 of Set 8 | arman
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=arman
    Processing Records 4 of Set 9 | khatanga
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=khatanga
    Processing Records 4 of Set 10 | saskylakh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saskylakh
    Processing Records 4 of Set 11 | aksarka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=aksarka
    Processing Records 4 of Set 12 | ilinskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ilinskiy
    Processing Records 4 of Set 13 | mys shmidta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mys+shmidta
    Processing Records 4 of Set 14 | amderma
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=amderma
    Processing Records 4 of Set 15 | talnakh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=talnakh
    Processing Records 4 of Set 16 | cherskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cherskiy
    Processing Records 4 of Set 17 | dombarovskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dombarovskiy
    Processing Records 4 of Set 18 | aleksandrov gay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=aleksandrov+gay
    Processing Records 4 of Set 19 | sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sentyabrskiy
    Processing Records 4 of Set 20 | berdigestyakh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=berdigestyakh
    Processing Records 4 of Set 21 | nikolskoye
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nikolskoye
    Processing Records 4 of Set 22 | vostok
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vostok
    Processing Records 4 of Set 23 | urusha
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=urusha
    Processing Records 4 of Set 24 | yefremov
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yefremov
    Processing Records 4 of Set 25 | vrangel
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vrangel
    Processing Records 4 of Set 26 | pestovo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pestovo
    Processing Records 4 of Set 27 | abrau-dyurso
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=abrau-dyurso
    Processing Records 4 of Set 28 | zhigansk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=zhigansk
    Processing Records 4 of Set 29 | dikson
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dikson
    Processing Records 4 of Set 30 | teya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=teya
    Processing Records 4 of Set 31 | belushya guba
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=belushya+guba
    Processing Records 4 of Set 32 | yar-sale
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yar-sale
    Processing Records 4 of Set 33 | sobolevo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sobolevo
    Processing Records 4 of Set 34 | sharkan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sharkan
    Processing Records 4 of Set 35 | nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nizhneyansk
    Processing Records 4 of Set 36 | irbeyskoye
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=irbeyskoye
    Processing Records 4 of Set 37 | leningradskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=leningradskiy
    Processing Records 4 of Set 38 | deputatskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=deputatskiy
    Processing Records 4 of Set 39 | tymovskoye
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tymovskoye
    Processing Records 4 of Set 40 | boguchany
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=boguchany
    Processing Records 4 of Set 41 | muzhi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=muzhi
    Processing Records 4 of Set 42 | severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=severo-kurilsk
    Processing Records 4 of Set 43 | verkhnevilyuysk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=verkhnevilyuysk
    Processing Records 4 of Set 44 | turukhansk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=turukhansk
    Processing Records 4 of Set 45 | lavrentiya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lavrentiya
    Processing Records 4 of Set 46 | komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=komsomolskiy
    Processing Records 4 of Set 47 | ordynskoye
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ordynskoye
    Processing Records 4 of Set 48 | verkhnyaya inta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=verkhnyaya+inta
    Processing Records 4 of Set 49 | yerbogachen
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yerbogachen
    Processing Records 4 of Set 50 | gryazovets
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gryazovets
    Processing Records 4 of Set 51 | timiryazevskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=timiryazevskiy
    Processing Records 4 of Set 52 | iskateley
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=iskateley
    Processing Records 4 of Set 53 | bolshaya martynovka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bolshaya+martynovka
    Processing Records 4 of Set 54 | nizhniy kuranakh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nizhniy+kuranakh
    Processing Records 4 of Set 55 | chokurdakh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=chokurdakh
    Processing Records 4 of Set 56 | ostrovnoy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ostrovnoy
    Processing Records 4 of Set 57 | alekseyevskaya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=alekseyevskaya
    Processing Records 4 of Set 58 | yelizovo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yelizovo
    Processing Records 4 of Set 59 | pavlogradka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pavlogradka
    Processing Records 4 of Set 60 | beringovskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=beringovskiy
    Processing Records 4 of Set 61 | tumannyy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tumannyy
    Processing Records 4 of Set 62 | snezhnogorsk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=snezhnogorsk
    Processing Records 4 of Set 63 | kirillov
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kirillov
    Processing Records 4 of Set 64 | zyryanka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=zyryanka
    Processing Records 4 of Set 65 | turka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=turka
    Processing Records 4 of Set 66 | gornopravdinsk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gornopravdinsk
    Processing Records 4 of Set 67 | zhireken
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=zhireken
    Processing Records 4 of Set 68 | skalistyy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=skalistyy
    Processing Records 4 of Set 69 | erzin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=erzin
    Processing Records 4 of Set 70 | gorbatov
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gorbatov
    Processing Records 4 of Set 71 | pestravka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pestravka
    Processing Records 4 of Set 72 | talaya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=talaya
    Processing Records 4 of Set 73 | chagda
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=chagda
    Processing Records 4 of Set 74 | tyazhinskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tyazhinskiy
    Processing Records 4 of Set 75 | okhotsk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=okhotsk
    Processing Records 4 of Set 76 | anadyr
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=anadyr
    Processing Records 4 of Set 77 | belyy yar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=belyy+yar
    Processing Records 4 of Set 78 | yuzhno-yeniseyskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yuzhno-yeniseyskiy
    Processing Records 4 of Set 79 | yeniseysk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yeniseysk
    Processing Records 4 of Set 80 | buzdyak
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=buzdyak
    Processing Records 4 of Set 81 | karaul
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=karaul
    Processing Records 4 of Set 82 | solnechnyy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=solnechnyy
    Processing Records 4 of Set 83 | kamenka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kamenka
    Processing Records 4 of Set 84 | nyurba
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nyurba
    Processing Records 4 of Set 85 | ozernovskiy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ozernovskiy
    Processing Records 4 of Set 86 | berezovyy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=berezovyy
    Processing Records 4 of Set 87 | ust-nera
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ust-nera
    Processing Records 5 of Set 1 | kodinar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kodinar
    Processing Records 5 of Set 2 | bargi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bargi
    Processing Records 5 of Set 3 | malwan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=malwan
    Processing Records 5 of Set 4 | ratanpur
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ratanpur
    Processing Records 5 of Set 5 | razole
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=razole
    Processing Records 5 of Set 6 | dwarka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dwarka
    Processing Records 5 of Set 7 | port blair
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+blair
    Processing Records 5 of Set 8 | leh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=leh
    Processing Records 5 of Set 9 | manavalakurichi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=manavalakurichi
    Processing Records 5 of Set 10 | eluru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=eluru
    Processing Records 6 of Set 1 | port alfred
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+alfred
    Processing Records 6 of Set 2 | bredasdorp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bredasdorp
    Processing Records 6 of Set 3 | east london
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=east+london
    Processing Records 6 of Set 4 | port elizabeth
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+elizabeth
    Processing Records 6 of Set 5 | saldanha
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saldanha
    Processing Records 6 of Set 6 | cape town
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cape+town
    Processing Records 6 of Set 7 | kruisfontein
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kruisfontein
    Processing Records 6 of Set 8 | margate
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=margate
    Processing Records 6 of Set 9 | hermanus
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hermanus
    Processing Records 6 of Set 10 | burgersdorp
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=burgersdorp
    Processing Records 6 of Set 11 | umtata
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=umtata
    Processing Records 6 of Set 12 | plettenberg bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=plettenberg+bay
    Processing Records 6 of Set 13 | knysna
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=knysna
    Processing Records 6 of Set 14 | de aar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=de+aar
    Processing Records 6 of Set 15 | richards bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=richards+bay
    Processing Records 7 of Set 1 | ushuaia
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ushuaia
    Processing Records 7 of Set 2 | viedma
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=viedma
    Processing Records 7 of Set 3 | rawson
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=rawson
    Processing Records 7 of Set 4 | mar del plata
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mar+del+plata
    Processing Records 7 of Set 5 | santiago del estero
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=santiago+del+estero
    Processing Records 7 of Set 6 | comodoro rivadavia
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=comodoro+rivadavia
    Processing Records 7 of Set 7 | mercedes
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mercedes
    Processing Records 8 of Set 1 | mataura
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mataura
    Processing Records 8 of Set 2 | rikitea
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=rikitea
    Processing Records 8 of Set 3 | atuona
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=atuona
    Processing Records 8 of Set 4 | tiarei
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tiarei
    Processing Records 8 of Set 5 | avera
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=avera
    Processing Records 9 of Set 1 | tocopilla
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tocopilla
    Processing Records 9 of Set 2 | punta arenas
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=punta+arenas
    Processing Records 9 of Set 3 | coquimbo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=coquimbo
    Processing Records 9 of Set 4 | ancud
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ancud
    Processing Records 9 of Set 5 | valdivia
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=valdivia
    Processing Records 9 of Set 6 | castro
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=castro
    Processing Records 9 of Set 7 | lebu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lebu
    Processing Records 9 of Set 8 | calama
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=calama
    Processing Records 10 of Set 1 | bethel
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bethel
    Processing Records 10 of Set 2 | fortuna
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=fortuna
    Processing Records 10 of Set 3 | college
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=college
    Processing Records 10 of Set 4 | kodiak
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kodiak
    Processing Records 10 of Set 5 | half moon bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=half+moon+bay
    Processing Records 10 of Set 6 | ketchikan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ketchikan
    Processing Records 10 of Set 7 | hilo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hilo
    Processing Records 10 of Set 8 | barrow
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=barrow
    Processing Records 10 of Set 9 | coos bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=coos+bay
    Processing Records 10 of Set 10 | berlin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=berlin
    Processing Records 10 of Set 11 | kapaa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kapaa
    Processing Records 10 of Set 12 | nome
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nome
    Processing Records 10 of Set 13 | milford
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=milford
    Processing Records 10 of Set 14 | hailey
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hailey
    Processing Records 10 of Set 15 | winnemucca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=winnemucca
    Processing Records 10 of Set 16 | loiza
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=loiza
    Processing Records 10 of Set 17 | durango
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=durango
    Processing Records 10 of Set 18 | elizabeth city
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=elizabeth+city
    Processing Records 10 of Set 19 | makakilo city
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=makakilo+city
    Processing Records 10 of Set 20 | bastrop
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bastrop
    Processing Records 10 of Set 21 | sitka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sitka
    Processing Records 10 of Set 22 | valdosta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=valdosta
    Processing Records 10 of Set 23 | tucumcari
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tucumcari
    Processing Records 10 of Set 24 | klamath falls
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=klamath+falls
    Processing Records 10 of Set 25 | lompoc
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lompoc
    Processing Records 10 of Set 26 | dillon
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dillon
    Processing Records 10 of Set 27 | gonzales
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gonzales
    Processing Records 10 of Set 28 | mount vernon
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mount+vernon
    Processing Records 10 of Set 29 | brandon
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=brandon
    Processing Records 10 of Set 30 | kahului
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kahului
    Processing Records 10 of Set 31 | torrington
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=torrington
    Processing Records 10 of Set 32 | gatesville
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gatesville
    Processing Records 10 of Set 33 | uniontown
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=uniontown
    Processing Records 10 of Set 34 | lancaster
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lancaster
    Processing Records 11 of Set 1 | hithadhoo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hithadhoo
    Processing Records 11 of Set 2 | kudahuvadhoo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kudahuvadhoo
    Processing Records 11 of Set 3 | eydhafushi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=eydhafushi
    Processing Records 11 of Set 4 | ugoofaaru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ugoofaaru
    Processing Records 12 of Set 1 | cap malheureux
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cap+malheureux
    Processing Records 12 of Set 2 | mahebourg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mahebourg
    Processing Records 12 of Set 3 | quatre cocos
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=quatre+cocos
    Processing Records 12 of Set 4 | bambous virieux
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bambous+virieux
    Processing Records 12 of Set 5 | grand gaube
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=grand+gaube
    Processing Records 12 of Set 6 | souillac
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=souillac
    Processing Records 13 of Set 1 | saint-philippe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saint-philippe
    Processing Records 14 of Set 1 | karkaralinsk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=karkaralinsk
    Processing Records 14 of Set 2 | zhezkazgan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=zhezkazgan
    Processing Records 14 of Set 3 | turkistan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=turkistan
    Processing Records 14 of Set 4 | zachagansk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=zachagansk
    Processing Records 14 of Set 5 | dzhusaly
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dzhusaly
    Processing Records 14 of Set 6 | atasu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=atasu
    Processing Records 15 of Set 1 | adigeni
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=adigeni
    Processing Records 15 of Set 2 | sioni
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sioni
    Processing Records 16 of Set 1 | norman wells
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=norman+wells
    Processing Records 16 of Set 2 | aklavik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=aklavik
    Processing Records 16 of Set 3 | bonnyville
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bonnyville
    Processing Records 16 of Set 4 | yellowknife
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=yellowknife
    Processing Records 16 of Set 5 | tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tuktoyaktuk
    Processing Records 16 of Set 6 | clyde river
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=clyde+river
    Processing Records 16 of Set 7 | iqaluit
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=iqaluit
    Processing Records 16 of Set 8 | thompson
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=thompson
    Processing Records 16 of Set 9 | pangnirtung
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pangnirtung
    Processing Records 16 of Set 10 | torbay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=torbay
    Processing Records 16 of Set 11 | saint-augustin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saint-augustin
    Processing Records 16 of Set 12 | windsor
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=windsor
    Processing Records 16 of Set 13 | shelburne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=shelburne
    Processing Records 16 of Set 14 | attawapiskat
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=attawapiskat
    Processing Records 16 of Set 15 | saint anthony
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saint+anthony
    Processing Records 16 of Set 16 | merritt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=merritt
    Processing Records 16 of Set 17 | temiscaming
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=temiscaming
    Processing Records 16 of Set 18 | fort nelson
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=fort+nelson
    Processing Records 16 of Set 19 | sioux lookout
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sioux+lookout
    Processing Records 16 of Set 20 | chapais
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=chapais
    Processing Records 16 of Set 21 | port hardy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+hardy
    Processing Records 16 of Set 22 | nipawin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nipawin
    Processing Records 16 of Set 23 | smithers
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=smithers
    Processing Records 16 of Set 24 | sept-iles
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sept-iles
    Processing Records 16 of Set 25 | port hawkesbury
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port+hawkesbury
    Processing Records 17 of Set 1 | qaanaaq
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=qaanaaq
    Processing Records 17 of Set 2 | illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=illoqqortoormiut
    Processing Records 17 of Set 3 | upernavik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=upernavik
    Processing Records 17 of Set 4 | narsaq
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=narsaq
    Processing Records 17 of Set 5 | ilulissat
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ilulissat
    Processing Records 17 of Set 6 | qasigiannguit
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=qasigiannguit
    Processing Records 17 of Set 7 | tasiilaq
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tasiilaq
    Processing Records 18 of Set 1 | gat
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gat
    Processing Records 18 of Set 2 | marzuq
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=marzuq
    Processing Records 18 of Set 3 | waddan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=waddan
    Processing Records 18 of Set 4 | tarhunah
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tarhunah
    Processing Records 18 of Set 5 | darnah
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=darnah
    Processing Records 18 of Set 6 | jalu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=jalu
    Processing Records 18 of Set 7 | nalut
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nalut
    Processing Records 19 of Set 1 | chuy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=chuy
    Processing Records 20 of Set 1 | vaini
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vaini
    Processing Records 21 of Set 1 | bayir
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bayir
    Processing Records 22 of Set 1 | panalingaan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=panalingaan
    Processing Records 22 of Set 2 | san policarpo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+policarpo
    Processing Records 22 of Set 3 | anito
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=anito
    Processing Records 22 of Set 4 | sulangan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sulangan
    Processing Records 22 of Set 5 | san agustin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+agustin
    Processing Records 22 of Set 6 | tabo-o
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tabo-o
    Processing Records 22 of Set 7 | daliao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=daliao
    Processing Records 23 of Set 1 | auki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=auki
    Processing Records 23 of Set 2 | buala
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=buala
    Processing Records 23 of Set 3 | gizo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gizo
    Processing Records 23 of Set 4 | tulagi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tulagi
    Processing Records 24 of Set 1 | umm lajj
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=umm+lajj
    Processing Records 24 of Set 2 | abha
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=abha
    Processing Records 24 of Set 3 | riyadh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=riyadh
    Processing Records 24 of Set 4 | sakakah
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sakakah
    Processing Records 24 of Set 5 | najran
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=najran
    Processing Records 24 of Set 6 | turayf
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=turayf
    Processing Records 24 of Set 7 | mecca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mecca
    Processing Records 24 of Set 8 | buqayq
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=buqayq
    Processing Records 25 of Set 1 | wahran
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=wahran
    Processing Records 26 of Set 1 | shingu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=shingu
    Processing Records 26 of Set 2 | muroto
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=muroto
    Processing Records 26 of Set 3 | hasaki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hasaki
    Processing Records 26 of Set 4 | katsuura
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=katsuura
    Processing Records 26 of Set 5 | noshiro
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=noshiro
    Processing Records 26 of Set 6 | nishihara
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nishihara
    Processing Records 26 of Set 7 | naze
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=naze
    Processing Records 26 of Set 8 | hirara
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hirara
    Processing Records 26 of Set 9 | tateyama
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tateyama
    Processing Records 26 of Set 10 | shimoda
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=shimoda
    Processing Records 27 of Set 1 | ponta do sol
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ponta+do+sol
    Processing Records 27 of Set 2 | sao filipe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sao+filipe
    Processing Records 27 of Set 3 | porto novo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=porto+novo
    Processing Records 28 of Set 1 | san patricio
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+patricio
    Processing Records 28 of Set 2 | guerrero negro
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=guerrero+negro
    Processing Records 28 of Set 3 | sayula
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sayula
    Processing Records 28 of Set 4 | imuris
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=imuris
    Processing Records 28 of Set 5 | san blas
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+blas
    Processing Records 28 of Set 6 | tayoltita
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tayoltita
    Processing Records 28 of Set 7 | ixtapa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ixtapa
    Processing Records 28 of Set 8 | coahuayana
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=coahuayana
    Processing Records 28 of Set 9 | pochutla
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pochutla
    Processing Records 28 of Set 10 | san pedro de los naranjos
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+pedro+de+los+naranjos
    Processing Records 28 of Set 11 | constitucion
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=constitucion
    Processing Records 28 of Set 12 | acapulco
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=acapulco
    Processing Records 28 of Set 13 | sabinas hidalgo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sabinas+hidalgo
    Processing Records 28 of Set 14 | valentin gomez farias
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=valentin+gomez+farias
    Processing Records 28 of Set 15 | cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cabo+san+lucas
    Processing Records 28 of Set 16 | dzilam gonzalez
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dzilam+gonzalez
    Processing Records 29 of Set 1 | port-gentil
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=port-gentil
    Processing Records 29 of Set 2 | booue
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=booue
    Processing Records 29 of Set 3 | mayumba
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mayumba
    Processing Records 30 of Set 1 | bengkulu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bengkulu
    Processing Records 30 of Set 2 | labuhan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=labuhan
    Processing Records 30 of Set 3 | meulaboh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=meulaboh
    Processing Records 30 of Set 4 | ruteng
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ruteng
    Processing Records 30 of Set 5 | sibolga
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sibolga
    Processing Records 30 of Set 6 | waingapu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=waingapu
    Processing Records 30 of Set 7 | merauke
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=merauke
    Processing Records 30 of Set 8 | pontianak
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pontianak
    Processing Records 30 of Set 9 | denpasar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=denpasar
    Processing Records 30 of Set 10 | padang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=padang
    Processing Records 30 of Set 11 | kijang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kijang
    Processing Records 30 of Set 12 | baturaja
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=baturaja
    Processing Records 30 of Set 13 | sabang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sabang
    Processing Records 30 of Set 14 | manado
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=manado
    Processing Records 30 of Set 15 | bambanglipuro
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bambanglipuro
    Processing Records 30 of Set 16 | kupang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kupang
    Processing Records 30 of Set 17 | palabuhanratu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=palabuhanratu
    Processing Records 31 of Set 1 | suez
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=suez
    Processing Records 31 of Set 2 | alexandria
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=alexandria
    Processing Records 32 of Set 1 | puerto ayora
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=puerto+ayora
    Processing Records 32 of Set 2 | san cristobal
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+cristobal
    Processing Records 33 of Set 1 | jamestown
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=jamestown
    Processing Records 33 of Set 2 | georgetown
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=georgetown
    Processing Records 34 of Set 1 | mandalgovi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mandalgovi
    Processing Records 34 of Set 2 | ulaanbaatar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ulaanbaatar
    Processing Records 35 of Set 1 | klaksvik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=klaksvik
    Processing Records 35 of Set 2 | vestmanna
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vestmanna
    Processing Records 36 of Set 1 | cockburn town
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=cockburn+town
    Processing Records 37 of Set 1 | kaitangata
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kaitangata
    Processing Records 37 of Set 2 | dunedin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dunedin
    Processing Records 37 of Set 3 | ahipara
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ahipara
    Processing Records 37 of Set 4 | kaeo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kaeo
    Processing Records 37 of Set 5 | bluff
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bluff
    Processing Records 37 of Set 6 | christchurch
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=christchurch
    Processing Records 37 of Set 7 | takaka
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=takaka
    Processing Records 37 of Set 8 | hastings
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hastings
    Processing Records 37 of Set 9 | tuatapere
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tuatapere
    Processing Records 37 of Set 10 | russell
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=russell
    Processing Records 37 of Set 11 | southbridge
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=southbridge
    Processing Records 37 of Set 12 | westport
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=westport
    Processing Records 37 of Set 13 | hokitika
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hokitika
    Processing Records 37 of Set 14 | ruatoria
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ruatoria
    Processing Records 38 of Set 1 | olafsvik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=olafsvik
    Processing Records 38 of Set 2 | bolungarvik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bolungarvik
    Processing Records 38 of Set 3 | vestmannaeyjar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vestmannaeyjar
    Processing Records 38 of Set 4 | grindavik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=grindavik
    Processing Records 38 of Set 5 | husavik
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=husavik
    Processing Records 38 of Set 6 | hofn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hofn
    Processing Records 39 of Set 1 | butaritari
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=butaritari
    Processing Records 39 of Set 2 | bairiki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bairiki
    Processing Records 40 of Set 1 | vila franca do campo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vila+franca+do+campo
    Processing Records 40 of Set 2 | ribeira grande
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ribeira+grande
    Processing Records 40 of Set 3 | praia da vitoria
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=praia+da+vitoria
    Processing Records 40 of Set 4 | lagoa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lagoa
    Processing Records 41 of Set 1 | longyearbyen
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=longyearbyen
    Processing Records 41 of Set 2 | barentsburg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=barentsburg
    Processing Records 42 of Set 1 | mwanza
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mwanza
    Processing Records 43 of Set 1 | longyan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=longyan
    Processing Records 43 of Set 2 | xinan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=xinan
    Processing Records 43 of Set 3 | korla
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=korla
    Processing Records 43 of Set 4 | weihe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=weihe
    Processing Records 43 of Set 5 | leiyang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=leiyang
    Processing Records 43 of Set 6 | xining
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=xining
    Processing Records 43 of Set 7 | kashi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kashi
    Processing Records 43 of Set 8 | hailar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hailar
    Processing Records 43 of Set 9 | altay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=altay
    Processing Records 43 of Set 10 | xuanzhou
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=xuanzhou
    Processing Records 43 of Set 11 | lasa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lasa
    Processing Records 43 of Set 12 | kuche
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kuche
    Processing Records 43 of Set 13 | hami
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hami
    Processing Records 43 of Set 14 | zhongshu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=zhongshu
    Processing Records 43 of Set 15 | linqiong
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=linqiong
    Processing Records 43 of Set 16 | jiuquan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=jiuquan
    Processing Records 44 of Set 1 | myitkyina
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=myitkyina
    Processing Records 44 of Set 2 | lashio
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lashio
    Processing Records 44 of Set 3 | ye
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ye
    Processing Records 45 of Set 1 | nouadhibou
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nouadhibou
    Processing Records 45 of Set 2 | atar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=atar
    Processing Records 46 of Set 1 | qandala
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=qandala
    Processing Records 46 of Set 2 | kismayo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kismayo
    Processing Records 46 of Set 3 | xuddur
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=xuddur
    Processing Records 46 of Set 4 | bereda
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bereda
    Processing Records 46 of Set 5 | odweyne
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=odweyne
    Processing Records 47 of Set 1 | alofi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=alofi
    Processing Records 48 of Set 1 | samusu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=samusu
    Processing Records 48 of Set 2 | satitoa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=satitoa
    Processing Records 48 of Set 3 | saleaula
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saleaula
    Processing Records 49 of Set 1 | gazanjyk
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gazanjyk
    Processing Records 50 of Set 1 | pachino
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pachino
    Processing Records 51 of Set 1 | mrirt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mrirt
    Processing Records 51 of Set 2 | nador
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nador
    Processing Records 52 of Set 1 | tatawin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tatawin
    Processing Records 53 of Set 1 | morehead
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=morehead
    Processing Records 53 of Set 2 | madang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=madang
    Processing Records 53 of Set 3 | kavieng
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kavieng
    Processing Records 53 of Set 4 | lorengau
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lorengau
    Processing Records 53 of Set 5 | kieta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kieta
    Processing Records 53 of Set 6 | vanimo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vanimo
    Processing Records 53 of Set 7 | namatanai
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=namatanai
    Processing Records 53 of Set 8 | daru
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=daru
    Processing Records 53 of Set 9 | kokopo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kokopo
    Processing Records 54 of Set 1 | bud
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bud
    Processing Records 54 of Set 2 | storforshei
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=storforshei
    Processing Records 54 of Set 3 | sorland
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sorland
    Processing Records 54 of Set 4 | vardo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vardo
    Processing Records 54 of Set 5 | havoysund
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=havoysund
    Processing Records 54 of Set 6 | skjervoy
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=skjervoy
    Processing Records 54 of Set 7 | mehamn
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mehamn
    Processing Records 54 of Set 8 | al
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=al
    Processing Records 54 of Set 9 | raudeberg
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=raudeberg
    Processing Records 54 of Set 10 | vigrestad
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vigrestad
    Processing Records 54 of Set 11 | roald
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=roald
    Processing Records 54 of Set 12 | magnor
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=magnor
    Processing Records 55 of Set 1 | codrington
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=codrington
    Processing Records 56 of Set 1 | brae
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=brae
    Processing Records 56 of Set 2 | lerwick
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lerwick
    Processing Records 56 of Set 3 | bridge of weir
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bridge+of+weir
    Processing Records 56 of Set 4 | scarborough
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=scarborough
    Processing Records 56 of Set 5 | ashington
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ashington
    Processing Records 57 of Set 1 | araouane
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=araouane
    Processing Records 58 of Set 1 | savalou
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=savalou
    Processing Records 59 of Set 1 | bondo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bondo
    Processing Records 59 of Set 2 | nioki
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nioki
    Processing Records 59 of Set 3 | lubumbashi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lubumbashi
    Processing Records 59 of Set 4 | mangai
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mangai
    Processing Records 60 of Set 1 | mporokoso
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mporokoso
    Processing Records 60 of Set 2 | mumbwa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mumbwa
    Processing Records 61 of Set 1 | salalah
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=salalah
    Processing Records 61 of Set 2 | sur
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sur
    Processing Records 62 of Set 1 | ramhormoz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ramhormoz
    Processing Records 62 of Set 2 | kashan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kashan
    Processing Records 62 of Set 3 | gerash
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gerash
    Processing Records 62 of Set 4 | khormuj
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=khormuj
    Processing Records 63 of Set 1 | saint-pierre
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saint-pierre
    Processing Records 64 of Set 1 | avarua
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=avarua
    Processing Records 65 of Set 1 | ranong
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ranong
    Processing Records 66 of Set 1 | marcona
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=marcona
    Processing Records 66 of Set 2 | chicama
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=chicama
    Processing Records 66 of Set 3 | hualmay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hualmay
    Processing Records 66 of Set 4 | chazuta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=chazuta
    Processing Records 66 of Set 5 | pisco
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pisco
    Processing Records 66 of Set 6 | san vicente de canete
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=san+vicente+de+canete
    Processing Records 66 of Set 7 | abancay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=abancay
    Processing Records 67 of Set 1 | henties bay
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=henties+bay
    Processing Records 67 of Set 2 | arandis
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=arandis
    Processing Records 67 of Set 3 | luderitz
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=luderitz
    Processing Records 68 of Set 1 | airai
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=airai
    Processing Records 69 of Set 1 | sebeta
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sebeta
    Processing Records 69 of Set 2 | mizan teferi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mizan+teferi
    Processing Records 70 of Set 1 | inhambane
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=inhambane
    Processing Records 70 of Set 2 | quelimane
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=quelimane
    Processing Records 70 of Set 3 | manjacaze
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=manjacaze
    Processing Records 71 of Set 1 | kalmunai
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kalmunai
    Processing Records 72 of Set 1 | karpathos
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=karpathos
    Processing Records 73 of Set 1 | soyo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=soyo
    Processing Records 73 of Set 2 | luau
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=luau
    Processing Records 74 of Set 1 | sinkat
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sinkat
    Processing Records 74 of Set 2 | tandalti
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tandalti
    Processing Records 74 of Set 3 | kaduqli
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kaduqli
    Processing Records 75 of Set 1 | bilma
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bilma
    Processing Records 76 of Set 1 | aksaray
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=aksaray
    Processing Records 76 of Set 2 | kutahya
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kutahya
    Processing Records 77 of Set 1 | mach
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mach
    Processing Records 78 of Set 1 | lolua
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lolua
    Processing Records 79 of Set 1 | jumla
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=jumla
    Processing Records 80 of Set 1 | bani
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bani
    Processing Records 82 of Set 1 | kuantan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kuantan
    Processing Records 82 of Set 2 | sri aman
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sri+aman
    Processing Records 83 of Set 1 | bathsheba
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bathsheba
    Processing Records 84 of Set 1 | santa rosa
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=santa+rosa
    Processing Records 85 of Set 1 | keuruu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=keuruu
    Processing Records 86 of Set 1 | skibbereen
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=skibbereen
    Processing Records 86 of Set 2 | dingle
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=dingle
    Processing Records 87 of Set 1 | isangel
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=isangel
    Processing Records 87 of Set 2 | luganville
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=luganville
    Processing Records 88 of Set 1 | muros
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=muros
    Processing Records 88 of Set 2 | los llanos de aridane
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=los+llanos+de+aridane
    Processing Records 89 of Set 1 | victoria
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=victoria
    Processing Records 90 of Set 1 | rumonge
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=rumonge
    Processing Records 91 of Set 1 | parrita
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=parrita
    Processing Records 92 of Set 1 | saint-francois
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saint-francois
    Processing Records 93 of Set 1 | krumbach
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=krumbach
    Processing Records 93 of Set 2 | neustadt
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=neustadt
    Processing Records 93 of Set 3 | stadthagen
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=stadthagen
    Processing Records 94 of Set 1 | puerto leguizamo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=puerto+leguizamo
    Processing Records 94 of Set 2 | manaure
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=manaure
    Processing Records 95 of Set 1 | sokoto
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sokoto
    Processing Records 95 of Set 2 | epe
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=epe
    Processing Records 96 of Set 1 | bangassou
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bangassou
    Processing Records 96 of Set 2 | carnot
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=carnot
    Processing Records 97 of Set 1 | bac lieu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=bac+lieu
    Processing Records 97 of Set 2 | tra vinh
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tra+vinh
    Processing Records 97 of Set 3 | phan rang
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=phan+rang
    Processing Records 98 of Set 1 | the valley
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=the+valley
    Processing Records 99 of Set 1 | gurgan
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=gurgan
    Processing Records 100 of Set 1 | pouembout
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=pouembout
    Processing Records 100 of Set 2 | vao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vao
    Processing Records 101 of Set 1 | nagyszenas
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=nagyszenas
    Processing Records 102 of Set 1 | mpigi
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mpigi
    Processing Records 103 of Set 1 | toktogul
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=toktogul
    Processing Records 103 of Set 2 | ozgon
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ozgon
    Processing Records 104 of Set 1 | tucupita
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tucupita
    Processing Records 104 of Set 2 | maturin
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=maturin
    Processing Records 105 of Set 1 | lodwar
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=lodwar
    Processing Records 106 of Set 1 | tarime
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tarime
    Processing Records 107 of Set 1 | barra patuca
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=barra+patuca
    Processing Records 108 of Set 1 | moissala
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=moissala
    Processing Records 108 of Set 2 | ngama
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ngama
    Processing Records 108 of Set 3 | mao
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=mao
    Processing Records 109 of Set 1 | farah
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=farah
    Processing Records 110 of Set 1 | kaohsiung
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=kaohsiung
    Processing Records 111 of Set 1 | charlestown
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=charlestown
    Processing Records 112 of Set 1 | skoghall
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=skoghall
    Processing Records 112 of Set 2 | ostersund
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=ostersund
    Processing Records 113 of Set 1 | baghdad
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=baghdad
    Processing Records 114 of Set 1 | saint george
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=saint+george
    Processing Records 114 of Set 2 | hamilton
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=hamilton
    Processing Records 115 of Set 1 | sassandra
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=sassandra
    Processing Records 115 of Set 2 | korhogo
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=korhogo
    Processing Records 116 of Set 1 | vaitupu
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=vaitupu
    Processing Records 117 of Set 1 | tonota
    http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=11cb939beaa584f261fe95765cf52336&q=tonota
    -----------------------------
    Data Retrieval Complete
    -----------------------------
    


```python
# Drop cities with missing information
locat = locat[pd.notnull(locat['Temperature'])]
counter_city = 0
locat['Date'] = ""
for indx,rw in loct.iterrows():
    
    counter_city = counter_city + 1
    counter_country = 0
    for index,row in locat.iterrows():

        if rw['Country code'] == row['Country code']:
            params = {"units":"Imperial", "APPID" :api_key, "q" :row["Closest City"]}
            
            # run a request using our params dictionary 
            city_weather_resp = requests.get(base_url,params)
            
            # convert response to json
            city_weather_res  = city_weather_resp.json() 
            
            # Loop for calculating the date after excluding the NAN valuea
            locat.set_value(index, 'Date', int(city_weather_res['dt'])) 
            
```


```python
locat.count()
```




    Closest City    505
    Country code    505
    Lat             505
    Lng             505
    Temperature     505
    Wind speed      505
    Humidity        505
    Cloudiness      505
    Date            505
    dtype: int64




```python
locat.head()
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
      <th>Closest City</th>
      <th>Country code</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Temperature</th>
      <th>Wind speed</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>cidreira</td>
      <td>BR</td>
      <td>-30.17</td>
      <td>-50.22</td>
      <td>62.32</td>
      <td>6.29</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>1520589019</td>
    </tr>
    <tr>
      <th>2</th>
      <td>hobart</td>
      <td>AU</td>
      <td>-42.88</td>
      <td>147.33</td>
      <td>62.60</td>
      <td>9.17</td>
      <td>77.0</td>
      <td>75.0</td>
      <td>1520587800</td>
    </tr>
    <tr>
      <th>3</th>
      <td>pevek</td>
      <td>RU</td>
      <td>69.70</td>
      <td>170.27</td>
      <td>-8.47</td>
      <td>29.10</td>
      <td>98.0</td>
      <td>44.0</td>
      <td>1520589043</td>
    </tr>
    <tr>
      <th>4</th>
      <td>kodinar</td>
      <td>IN</td>
      <td>20.79</td>
      <td>70.70</td>
      <td>95.39</td>
      <td>5.95</td>
      <td>26.0</td>
      <td>0.0</td>
      <td>1520589075</td>
    </tr>
    <tr>
      <th>5</th>
      <td>port alfred</td>
      <td>ZA</td>
      <td>-33.59</td>
      <td>26.89</td>
      <td>67.76</td>
      <td>8.75</td>
      <td>87.0</td>
      <td>0.0</td>
      <td>1520589079</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Converting the date using datetime function
current_date = pd.to_datetime(locat['Date'][0], unit='s')

#Exporting the data to csv
locat.to_csv("City_Weather_data.csv")
current_date
```




    Timestamp('2018-03-09 09:50:19')




```python
# Plot settings
def set_plot_prop(x_title,x_lim,y_title):
    plt.title(f"{y_title} vs {x_title}")
    plt.ylabel(y_title)
    plt.xlabel(x_title)
    plt.grid(True)
    plt.xlim(x_lim) 
```


```python
# Latitude vs Temperature 
locat.plot(kind="scatter",x="Lat",y="Temperature",grid=True,color="blue",edgecolors="black",linewidth='1',s=40) 
set_plot_prop("Latitude",[-80,100],"Max Temperature (F)")
plt.title("City Latitude vs Max Temperature (09/03/18)")
plt.ylim(-100,150)
plt.savefig("Temperature vs Latitude")
sns.set(rc={'figure.figsize':(9,7)})
plt.show()
```


![png](output_12_0.png)



```python
# Latitude vs Humidity
locat.plot(kind="scatter",x="Lat",y="Humidity",grid=True,color="blue",edgecolor="black",linewidth='1',s=40)
set_plot_prop("Latitude",[-80,100],"Humidity(%)")
plt.title("City Latitude vs Humidity (09/03/18)")
plt.ylim(-20,120)
sns.set(rc={'figure.figsize':(9,7)})
plt.savefig("Humidity vs Latitude")
plt.show()
```


![png](output_13_0.png)



```python
# Latitude vs Cloudiness
locat["Cloudiness"] = pd.to_numeric(locat["Cloudiness"])
locat.plot(kind="scatter",x="Lat",y="Cloudiness",grid=True,color="blue",edgecolor="black",linewidth='1',s=40)
set_plot_prop("Latitude",[-80,100],"Cloudiness(%)")
plt.title("City Latitude vs Cloudiness (09/03/18)")
plt.ylim(-20,120)
sns.set(rc={'figure.figsize':(9,7)})
plt.savefig("Cloudiness vs Latitude")
plt.show() 
```


![png](output_14_0.png)



```python
# Latitude vs Wind Speed
locat["Wind speed"] = pd.to_numeric(locat["Wind speed"])
locat.plot(kind="scatter",x="Lat",y="Wind speed",grid=True,color="blue",edgecolor="black",linewidth='1',s=40)
set_plot_prop("Latitude",[-80,100],"Wind speed (mph)")
plt.title("City Latitude vs Wind Speed (09/03/18)")
plt.ylim(-5,40)
sns.set(rc={'figure.figsize':(9,7)})
plt.savefig("Wind speed vs Latitude")
plt.show() 
```


![png](output_15_0.png)

