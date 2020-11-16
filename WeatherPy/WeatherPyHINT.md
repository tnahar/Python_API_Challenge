# WeatherPy
----

### Analysis
* As expected, the weather becomes significantly warmer as one approaches the equator (0 Deg. Latitude). More interestingly, however, is the fact that the southern hemisphere tends to be warmer this time of year than the northern hemisphere. This may be due to the tilt of the earth.
* There is no strong relationship between latitude and cloudiness. However, it is interesting to see that a strong band of cities sits at 0, 80, and 100% cloudiness.
* There is no strong relationship between latitude and wind speed. However, in northern hemispheres there is a flurry of cities with over 20 mph of wind.


```python
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import time
from scipy.stats import linregress

# Import API key
from api_keys import weather_api_key

# Incorporated citipy to determine city based on latitude and longitude
from citipy import citipy

# Output File (CSV)
output_data_file = "output_data/cities.csv"

# Range of latitudes and longitudes
lat_range = (-90, 90)
lng_range = (-180, 180)
```

## Generate Cities List


```python
# List for holding lat_lngs and cities
lat_lngs = []
cities = []

# Create a set of random lat and lng combinations
lats = np.random.uniform(lat_range[0], lat_range[1], size=1500)
lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500)
lat_lngs = zip(lats, lngs)

# Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs:
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
    
    # If the city is unique, then add it to a our cities list
    if city not in cities:
        cities.append(city)

# Print the city count to confirm sufficient count
len(cities)
```




    620



## Perform API Calls


```python
# Starting URL for Weather Map API Call
#url = "http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=" + weather_api_key
url = "http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=f2e7be3fbf6a6ca5ee22f83c7998246c"

# List of city data
city_data = []

# Print to logger
print("Beginning Data Retrieval     ")
print("-----------------------------")

# Create counters
record_count = 1
set_count = 1

# Loop through all the cities in our list
for i, city in enumerate(cities):
        
    # Group cities in sets of 50 for logging purposes
    if (i % 50 == 0 and i >= 50):
        set_count += 1
        record_count = 0

    # Create endpoint URL with each city
    city_url = url + "&q=" + city
    
    # Log the url, record, and set numbers
    print("Processing Record %s of Set %s | %s" % (record_count, set_count, city))

    # Add 1 to the record count
    record_count += 1

    # Run an API request for each of the cities
    try:
        # Parse the JSON and retrieve data
        city_weather = requests.get(city_url).json()

        # Parse out the max temp, humidity, and cloudiness
        city_lat = city_weather["coord"]["lat"]
        city_lng = city_weather["coord"]["lon"]
        city_max_temp = city_weather["main"]["temp_max"]
        city_humidity = city_weather["main"]["humidity"]
        city_clouds = city_weather["clouds"]["all"]
        city_wind = city_weather["wind"]["speed"]
        city_country = city_weather["sys"]["country"]
        city_date = city_weather["dt"]

        # Append the City information into city_data list
        city_data.append({"City": city, 
                          "Lat": city_lat, 
                          "Lng": city_lng, 
                          "Max Temp": city_max_temp,
                          "Humidity": city_humidity,
                          "Cloudiness": city_clouds,
                          "Wind Speed": city_wind,
                          "Country": city_country,
                          "Date": city_date})

    # If an error is experienced, skip the city
    except:
        print("City not found. Skipping...")
        pass
              
# Indicate that Data Loading is complete 
print("-----------------------------")
print("Data Retrieval Complete      ")
print("-----------------------------")
        
        
```

    Beginning Data Retrieval     
    -----------------------------
    Processing Record 1 of Set 1 | taolanaro
    City not found. Skipping...
    Processing Record 2 of Set 1 | port elizabeth
    Processing Record 3 of Set 1 | albany
    Processing Record 4 of Set 1 | port alfred
    Processing Record 5 of Set 1 | sitka
    Processing Record 6 of Set 1 | grand river south east
    City not found. Skipping...
    Processing Record 7 of Set 1 | khatanga
    Processing Record 8 of Set 1 | paranhos
    Processing Record 9 of Set 1 | lolua
    City not found. Skipping...
    Processing Record 10 of Set 1 | cabo san lucas
    Processing Record 11 of Set 1 | torit
    Processing Record 12 of Set 1 | lagoa
    Processing Record 13 of Set 1 | hofn
    Processing Record 14 of Set 1 | sorong
    Processing Record 15 of Set 1 | hamilton
    Processing Record 16 of Set 1 | prachin buri
    Processing Record 17 of Set 1 | chambar
    Processing Record 18 of Set 1 | tuktoyaktuk
    Processing Record 19 of Set 1 | geraldton
    Processing Record 20 of Set 1 | kailua
    Processing Record 21 of Set 1 | luba
    Processing Record 22 of Set 1 | hilo
    Processing Record 23 of Set 1 | amderma
    City not found. Skipping...
    Processing Record 24 of Set 1 | korla
    Processing Record 25 of Set 1 | georgetown
    Processing Record 26 of Set 1 | jamestown
    Processing Record 27 of Set 1 | yellowknife
    Processing Record 28 of Set 1 | mataura
    Processing Record 29 of Set 1 | hobart
    Processing Record 30 of Set 1 | talnakh
    Processing Record 31 of Set 1 | pangnirtung
    Processing Record 32 of Set 1 | lorengau
    Processing Record 33 of Set 1 | bluff
    Processing Record 34 of Set 1 | tete
    Processing Record 35 of Set 1 | hithadhoo
    Processing Record 36 of Set 1 | mahebourg
    Processing Record 37 of Set 1 | smithers
    Processing Record 38 of Set 1 | nyurba
    Processing Record 39 of Set 1 | punta arenas
    Processing Record 40 of Set 1 | lai
    Processing Record 41 of Set 1 | san juan nepomuceno
    Processing Record 42 of Set 1 | butaritari
    Processing Record 43 of Set 1 | gulshat
    City not found. Skipping...
    Processing Record 44 of Set 1 | limoeiro do ajuru
    Processing Record 45 of Set 1 | ribeira grande
    Processing Record 46 of Set 1 | batemans bay
    Processing Record 47 of Set 1 | banjarmasin
    Processing Record 48 of Set 1 | cherskiy
    Processing Record 49 of Set 1 | nome
    Processing Record 50 of Set 1 | new norfolk
    Processing Record 0 of Set 2 | luderitz
    Processing Record 1 of Set 2 | saldanha
    Processing Record 2 of Set 2 | kruisfontein
    Processing Record 3 of Set 2 | bo rai
    Processing Record 4 of Set 2 | barrow
    Processing Record 5 of Set 2 | xuddur
    Processing Record 6 of Set 2 | watertown
    Processing Record 7 of Set 2 | thompson
    Processing Record 8 of Set 2 | jambi
    Processing Record 9 of Set 2 | bengkulu
    Processing Record 10 of Set 2 | vostok
    Processing Record 11 of Set 2 | kahului
    Processing Record 12 of Set 2 | kununurra
    Processing Record 13 of Set 2 | eastbourne
    Processing Record 14 of Set 2 | reconquista
    Processing Record 15 of Set 2 | waitara
    Processing Record 16 of Set 2 | lebu
    Processing Record 17 of Set 2 | viet tri
    Processing Record 18 of Set 2 | tasiilaq
    Processing Record 19 of Set 2 | ponta do sol
    Processing Record 20 of Set 2 | faanui
    Processing Record 21 of Set 2 | chengde
    Processing Record 22 of Set 2 | vallenar
    Processing Record 23 of Set 2 | ushuaia
    Processing Record 24 of Set 2 | halifax
    Processing Record 25 of Set 2 | rikitea
    Processing Record 26 of Set 2 | osinki
    Processing Record 27 of Set 2 | mitchell
    Processing Record 28 of Set 2 | labutta
    City not found. Skipping...
    Processing Record 29 of Set 2 | mar del plata
    Processing Record 30 of Set 2 | sao miguel do araguaia
    Processing Record 31 of Set 2 | beacon
    Processing Record 32 of Set 2 | bismarck
    Processing Record 33 of Set 2 | illoqqortoormiut
    City not found. Skipping...
    Processing Record 34 of Set 2 | goderich
    Processing Record 35 of Set 2 | hailar
    Processing Record 36 of Set 2 | chokurdakh
    Processing Record 37 of Set 2 | bethel
    Processing Record 38 of Set 2 | robinson
    Processing Record 39 of Set 2 | steamboat springs
    Processing Record 40 of Set 2 | bur gabo
    City not found. Skipping...
    Processing Record 41 of Set 2 | ahuimanu
    Processing Record 42 of Set 2 | kapaa
    Processing Record 43 of Set 2 | bilibino
    Processing Record 44 of Set 2 | coquimbo
    Processing Record 45 of Set 2 | george
    Processing Record 46 of Set 2 | port macquarie
    Processing Record 47 of Set 2 | natal
    Processing Record 48 of Set 2 | ludvika
    Processing Record 49 of Set 2 | saint-philippe
    Processing Record 0 of Set 3 | issoire
    Processing Record 1 of Set 3 | viedma
    Processing Record 2 of Set 3 | barentsburg
    City not found. Skipping...
    Processing Record 3 of Set 3 | karratha
    Processing Record 4 of Set 3 | carnarvon
    Processing Record 5 of Set 3 | vaini
    Processing Record 6 of Set 3 | kayerkan
    Processing Record 7 of Set 3 | san javier
    Processing Record 8 of Set 3 | iqaluit
    Processing Record 9 of Set 3 | petropavlovsk-kamchatskiy
    Processing Record 10 of Set 3 | panaba
    Processing Record 11 of Set 3 | chuy
    Processing Record 12 of Set 3 | tuatapere
    Processing Record 13 of Set 3 | talara
    Processing Record 14 of Set 3 | margate
    Processing Record 15 of Set 3 | touros
    Processing Record 16 of Set 3 | nizhniy baskunchak
    Processing Record 17 of Set 3 | busselton
    Processing Record 18 of Set 3 | mys shmidta
    City not found. Skipping...
    Processing Record 19 of Set 3 | bathsheba
    Processing Record 20 of Set 3 | piraquara
    Processing Record 21 of Set 3 | skibbereen
    Processing Record 22 of Set 3 | atuona
    Processing Record 23 of Set 3 | benin
    Processing Record 24 of Set 3 | jalu
    Processing Record 25 of Set 3 | gaoua
    Processing Record 26 of Set 3 | arraial do cabo
    Processing Record 27 of Set 3 | sao filipe
    Processing Record 28 of Set 3 | port victoria
    Processing Record 29 of Set 3 | colmenar viejo
    Processing Record 30 of Set 3 | amberley
    Processing Record 31 of Set 3 | atambua
    Processing Record 32 of Set 3 | shimoda
    Processing Record 33 of Set 3 | karaul
    City not found. Skipping...
    Processing Record 34 of Set 3 | lusambo
    Processing Record 35 of Set 3 | coahuayana
    Processing Record 36 of Set 3 | east london
    Processing Record 37 of Set 3 | alice springs
    Processing Record 38 of Set 3 | rincon
    Processing Record 39 of Set 3 | jimenez
    Processing Record 40 of Set 3 | teya
    Processing Record 41 of Set 3 | severo-kurilsk
    Processing Record 42 of Set 3 | katangli
    Processing Record 43 of Set 3 | codrington
    Processing Record 44 of Set 3 | bredasdorp
    Processing Record 45 of Set 3 | biak
    Processing Record 46 of Set 3 | sleaford
    Processing Record 47 of Set 3 | shankargarh
    Processing Record 48 of Set 3 | lisala
    Processing Record 49 of Set 3 | kushiro
    Processing Record 0 of Set 4 | sinnamary
    Processing Record 1 of Set 4 | tiksi
    Processing Record 2 of Set 4 | cidreira
    Processing Record 3 of Set 4 | hermanus
    Processing Record 4 of Set 4 | avarua
    Processing Record 5 of Set 4 | mehran
    Processing Record 6 of Set 4 | necochea
    Processing Record 7 of Set 4 | quba
    Processing Record 8 of Set 4 | anadyr
    Processing Record 9 of Set 4 | cape town
    Processing Record 10 of Set 4 | kodiak
    Processing Record 11 of Set 4 | barbar
    City not found. Skipping...
    Processing Record 12 of Set 4 | tsihombe
    City not found. Skipping...
    Processing Record 13 of Set 4 | newport
    Processing Record 14 of Set 4 | acarau
    Processing Record 15 of Set 4 | tangshan
    Processing Record 16 of Set 4 | kuche
    City not found. Skipping...
    Processing Record 17 of Set 4 | susuman
    Processing Record 18 of Set 4 | burica
    City not found. Skipping...
    Processing Record 19 of Set 4 | grindavik
    Processing Record 20 of Set 4 | adolfo lopez mateos
    Processing Record 21 of Set 4 | cap malheureux
    Processing Record 22 of Set 4 | constitucion
    Processing Record 23 of Set 4 | esperance
    Processing Record 24 of Set 4 | dzhusaly
    City not found. Skipping...
    Processing Record 25 of Set 4 | olafsvik
    Processing Record 26 of Set 4 | tahta
    Processing Record 27 of Set 4 | poum
    Processing Record 28 of Set 4 | saint-joseph
    Processing Record 29 of Set 4 | kalmunai
    Processing Record 30 of Set 4 | guerrero negro
    Processing Record 31 of Set 4 | izhma
    Processing Record 32 of Set 4 | grants
    Processing Record 33 of Set 4 | les cayes
    Processing Record 34 of Set 4 | yumbe
    Processing Record 35 of Set 4 | otyniya
    Processing Record 36 of Set 4 | vasilsursk
    Processing Record 37 of Set 4 | ilulissat
    Processing Record 38 of Set 4 | torbay
    Processing Record 39 of Set 4 | poway
    Processing Record 40 of Set 4 | dikson
    Processing Record 41 of Set 4 | samaipata
    Processing Record 42 of Set 4 | lubango
    Processing Record 43 of Set 4 | meyungs
    City not found. Skipping...
    Processing Record 44 of Set 4 | puerto ayora
    Processing Record 45 of Set 4 | hobyo
    Processing Record 46 of Set 4 | norman wells
    Processing Record 47 of Set 4 | cururupu
    Processing Record 48 of Set 4 | agirish
    Processing Record 49 of Set 4 | kupang
    Processing Record 0 of Set 5 | muros
    Processing Record 1 of Set 5 | nichinan
    Processing Record 2 of Set 5 | cravo norte
    Processing Record 3 of Set 5 | atar
    Processing Record 4 of Set 5 | fukue
    Processing Record 5 of Set 5 | lompoc
    Processing Record 6 of Set 5 | dzhebariki-khaya
    Processing Record 7 of Set 5 | tual
    Processing Record 8 of Set 5 | itaueira
    Processing Record 9 of Set 5 | hambantota
    Processing Record 10 of Set 5 | port lincoln
    Processing Record 11 of Set 5 | castro
    Processing Record 12 of Set 5 | qaanaaq
    Processing Record 13 of Set 5 | mount gambier
    Processing Record 14 of Set 5 | bermeo
    Processing Record 15 of Set 5 | belushya guba
    City not found. Skipping...
    Processing Record 16 of Set 5 | samarai
    Processing Record 17 of Set 5 | lyubech
    Processing Record 18 of Set 5 | palmer
    Processing Record 19 of Set 5 | barcelos
    Processing Record 20 of Set 5 | thaba nchu
    Processing Record 21 of Set 5 | boromo
    Processing Record 22 of Set 5 | pirgos
    Processing Record 23 of Set 5 | wahiawa
    Processing Record 24 of Set 5 | tondano
    Processing Record 25 of Set 5 | folkestone
    Processing Record 26 of Set 5 | brae
    Processing Record 27 of Set 5 | tibati
    Processing Record 28 of Set 5 | yenagoa
    Processing Record 29 of Set 5 | santa cruz de la palma
    Processing Record 30 of Set 5 | willemstad
    Processing Record 31 of Set 5 | sinkat
    City not found. Skipping...
    Processing Record 32 of Set 5 | nanortalik
    Processing Record 33 of Set 5 | flinders
    Processing Record 34 of Set 5 | bocaranga
    City not found. Skipping...
    Processing Record 35 of Set 5 | mananara
    Processing Record 36 of Set 5 | khani
    Processing Record 37 of Set 5 | kjollefjord
    Processing Record 38 of Set 5 | alofi
    Processing Record 39 of Set 5 | baghdad
    Processing Record 40 of Set 5 | vitimskiy
    City not found. Skipping...
    Processing Record 41 of Set 5 | adrar
    Processing Record 42 of Set 5 | lindi
    Processing Record 43 of Set 5 | do gonbadan
    Processing Record 44 of Set 5 | berlevag
    Processing Record 45 of Set 5 | buin
    Processing Record 46 of Set 5 | los lunas
    Processing Record 47 of Set 5 | antofagasta
    Processing Record 48 of Set 5 | nizhneyansk
    City not found. Skipping...
    Processing Record 49 of Set 5 | matamoros
    Processing Record 0 of Set 6 | aleppo
    Processing Record 1 of Set 6 | kuusamo
    Processing Record 2 of Set 6 | vaitupu
    City not found. Skipping...
    Processing Record 3 of Set 6 | bilma
    Processing Record 4 of Set 6 | ahipara
    Processing Record 5 of Set 6 | tidore
    City not found. Skipping...
    Processing Record 6 of Set 6 | yerbogachen
    Processing Record 7 of Set 6 | beringovskiy
    Processing Record 8 of Set 6 | aguimes
    Processing Record 9 of Set 6 | sentyabrskiy
    City not found. Skipping...
    Processing Record 10 of Set 6 | falavarjan
    Processing Record 11 of Set 6 | attawapiskat
    City not found. Skipping...
    Processing Record 12 of Set 6 | neyshabur
    Processing Record 13 of Set 6 | kudahuvadhoo
    Processing Record 14 of Set 6 | fort nelson
    Processing Record 15 of Set 6 | salalah
    Processing Record 16 of Set 6 | pevek
    Processing Record 17 of Set 6 | ishikawa
    Processing Record 18 of Set 6 | san angelo
    Processing Record 19 of Set 6 | praia da vitoria
    Processing Record 20 of Set 6 | fortuna
    Processing Record 21 of Set 6 | samusu
    City not found. Skipping...
    Processing Record 22 of Set 6 | hovd
    Processing Record 23 of Set 6 | temyasovo
    Processing Record 24 of Set 6 | vila velha
    Processing Record 25 of Set 6 | kon tum
    Processing Record 26 of Set 6 | qaqortoq
    Processing Record 27 of Set 6 | kosa
    Processing Record 28 of Set 6 | ketchikan
    Processing Record 29 of Set 6 | souillac
    Processing Record 30 of Set 6 | saskylakh
    Processing Record 31 of Set 6 | micheweni
    Processing Record 32 of Set 6 | bambous virieux
    Processing Record 33 of Set 6 | vila franca do campo
    Processing Record 34 of Set 6 | sola
    Processing Record 35 of Set 6 | kavieng
    Processing Record 36 of Set 6 | dharchula
    Processing Record 37 of Set 6 | jan kempdorp
    Processing Record 38 of Set 6 | santo domingo
    Processing Record 39 of Set 6 | grand-lahou
    Processing Record 40 of Set 6 | longyearbyen
    Processing Record 41 of Set 6 | praia
    Processing Record 42 of Set 6 | maghama
    City not found. Skipping...
    Processing Record 43 of Set 6 | lakatoro
    Processing Record 44 of Set 6 | inuvik
    Processing Record 45 of Set 6 | havoysund
    Processing Record 46 of Set 6 | rawson
    Processing Record 47 of Set 6 | taiyuan
    Processing Record 48 of Set 6 | san luis
    Processing Record 49 of Set 6 | mullaitivu
    City not found. Skipping...
    Processing Record 0 of Set 7 | port moresby
    Processing Record 1 of Set 7 | borlange
    City not found. Skipping...
    Processing Record 2 of Set 7 | haines junction
    Processing Record 3 of Set 7 | lata
    Processing Record 4 of Set 7 | springbok
    Processing Record 5 of Set 7 | paamiut
    Processing Record 6 of Set 7 | khasan
    Processing Record 7 of Set 7 | pushkino
    Processing Record 8 of Set 7 | nouadhibou
    Processing Record 9 of Set 7 | komsomolskiy
    Processing Record 10 of Set 7 | briancon
    Processing Record 11 of Set 7 | najran
    Processing Record 12 of Set 7 | takoradi
    Processing Record 13 of Set 7 | kindu
    Processing Record 14 of Set 7 | wajir
    Processing Record 15 of Set 7 | boa vista
    Processing Record 16 of Set 7 | kiunga
    Processing Record 17 of Set 7 | nelson bay
    Processing Record 18 of Set 7 | siloam springs
    Processing Record 19 of Set 7 | hasaki
    Processing Record 20 of Set 7 | arona
    Processing Record 21 of Set 7 | shingu
    Processing Record 22 of Set 7 | ostrovnoy
    Processing Record 23 of Set 7 | tres picos
    Processing Record 24 of Set 7 | vitoria
    Processing Record 25 of Set 7 | narsaq
    Processing Record 26 of Set 7 | tumen
    Processing Record 27 of Set 7 | mehamn
    Processing Record 28 of Set 7 | nassau
    Processing Record 29 of Set 7 | oistins
    Processing Record 30 of Set 7 | camacupa
    Processing Record 31 of Set 7 | lar gerd
    City not found. Skipping...
    Processing Record 32 of Set 7 | shwebo
    Processing Record 33 of Set 7 | kosino
    Processing Record 34 of Set 7 | dennis
    Processing Record 35 of Set 7 | naze
    Processing Record 36 of Set 7 | nueve de julio
    Processing Record 37 of Set 7 | provideniya
    Processing Record 38 of Set 7 | airai
    Processing Record 39 of Set 7 | marsa matruh
    Processing Record 40 of Set 7 | comodoro rivadavia
    Processing Record 41 of Set 7 | kamloops
    Processing Record 42 of Set 7 | vigrestad
    Processing Record 43 of Set 7 | pisco
    Processing Record 44 of Set 7 | saint-louis
    Processing Record 45 of Set 7 | saint-francois
    Processing Record 46 of Set 7 | asau
    Processing Record 47 of Set 7 | ulaangom
    Processing Record 48 of Set 7 | sorland
    Processing Record 49 of Set 7 | tairua
    Processing Record 0 of Set 8 | pierre
    Processing Record 1 of Set 8 | san cristobal
    Processing Record 2 of Set 8 | rocha
    Processing Record 3 of Set 8 | gadoros
    Processing Record 4 of Set 8 | mao
    Processing Record 5 of Set 8 | gravdal
    Processing Record 6 of Set 8 | noumea
    Processing Record 7 of Set 8 | bambanglipuro
    Processing Record 8 of Set 8 | ponta delgada
    Processing Record 9 of Set 8 | amapa
    Processing Record 10 of Set 8 | fort beaufort
    Processing Record 11 of Set 8 | concarneau
    Processing Record 12 of Set 8 | marrakesh
    Processing Record 13 of Set 8 | coos bay
    Processing Record 14 of Set 8 | imeni zhelyabova
    Processing Record 15 of Set 8 | simao
    Processing Record 16 of Set 8 | saint-augustin
    Processing Record 17 of Set 8 | evanston
    Processing Record 18 of Set 8 | fenoarivo
    Processing Record 19 of Set 8 | juiz de fora
    Processing Record 20 of Set 8 | eyl
    Processing Record 21 of Set 8 | tirumullaivasal
    Processing Record 22 of Set 8 | santa cruz
    Processing Record 23 of Set 8 | namibe
    Processing Record 24 of Set 8 | xiongzhou
    Processing Record 25 of Set 8 | coroico
    Processing Record 26 of Set 8 | the valley
    Processing Record 27 of Set 8 | oriximina
    Processing Record 28 of Set 8 | rajur
    Processing Record 29 of Set 8 | umzimvubu
    City not found. Skipping...
    Processing Record 30 of Set 8 | santa ines
    Processing Record 31 of Set 8 | buala
    Processing Record 32 of Set 8 | teeli
    Processing Record 33 of Set 8 | sri aman
    Processing Record 34 of Set 8 | isangel
    Processing Record 35 of Set 8 | teahupoo
    Processing Record 36 of Set 8 | koungheul
    Processing Record 37 of Set 8 | bitung
    Processing Record 38 of Set 8 | tucuma
    Processing Record 39 of Set 8 | deh rawud
    City not found. Skipping...
    Processing Record 40 of Set 8 | cay
    Processing Record 41 of Set 8 | myitkyina
    Processing Record 42 of Set 8 | panukulan
    Processing Record 43 of Set 8 | santa maria
    Processing Record 44 of Set 8 | jicaro galan
    Processing Record 45 of Set 8 | weligama
    Processing Record 46 of Set 8 | krasnoarmeyskiy
    Processing Record 47 of Set 8 | gualeguay
    Processing Record 48 of Set 8 | sao joao da barra
    Processing Record 49 of Set 8 | yulara
    Processing Record 0 of Set 9 | baruun-urt
    Processing Record 1 of Set 9 | urucara
    Processing Record 2 of Set 9 | dindori
    Processing Record 3 of Set 9 | medea
    Processing Record 4 of Set 9 | begoro
    Processing Record 5 of Set 9 | tumannyy
    City not found. Skipping...
    Processing Record 6 of Set 9 | inongo
    Processing Record 7 of Set 9 | kawalu
    Processing Record 8 of Set 9 | toliary
    City not found. Skipping...
    Processing Record 9 of Set 9 | turukhansk
    Processing Record 10 of Set 9 | saint george
    Processing Record 11 of Set 9 | saleaula
    City not found. Skipping...
    Processing Record 12 of Set 9 | tura
    Processing Record 13 of Set 9 | araceli
    Processing Record 14 of Set 9 | lavrentiya
    Processing Record 15 of Set 9 | wanaka
    Processing Record 16 of Set 9 | bogorodskoye
    Processing Record 17 of Set 9 | henties bay
    Processing Record 18 of Set 9 | la gaulette
    City not found. Skipping...
    Processing Record 19 of Set 9 | marzuq
    Processing Record 20 of Set 9 | bandarbeyla
    Processing Record 21 of Set 9 | roald
    Processing Record 22 of Set 9 | urumqi
    Processing Record 23 of Set 9 | barawe
    City not found. Skipping...
    Processing Record 24 of Set 9 | buraydah
    Processing Record 25 of Set 9 | malakal
    Processing Record 26 of Set 9 | makakilo city
    Processing Record 27 of Set 9 | clyde river
    Processing Record 28 of Set 9 | mezen
    Processing Record 29 of Set 9 | kurilsk
    Processing Record 30 of Set 9 | taoudenni
    Processing Record 31 of Set 9 | brooks
    Processing Record 32 of Set 9 | victoria
    Processing Record 33 of Set 9 | arlit
    Processing Record 34 of Set 9 | magdagachi
    Processing Record 35 of Set 9 | alekseyevka
    Processing Record 36 of Set 9 | wewak
    Processing Record 37 of Set 9 | cayenne
    Processing Record 38 of Set 9 | upernavik
    Processing Record 39 of Set 9 | hualmay
    Processing Record 40 of Set 9 | itarema
    Processing Record 41 of Set 9 | krasnyy luch
    Processing Record 42 of Set 9 | leningradskiy
    Processing Record 43 of Set 9 | namatanai
    Processing Record 44 of Set 9 | resistencia
    Processing Record 45 of Set 9 | sao jose da coroa grande
    Processing Record 46 of Set 9 | verkh-usugli
    Processing Record 47 of Set 9 | wajima
    Processing Record 48 of Set 9 | acapulco
    Processing Record 49 of Set 9 | dingle
    Processing Record 0 of Set 10 | murmashi
    Processing Record 1 of Set 10 | miquelon
    Processing Record 2 of Set 10 | deputatskiy
    Processing Record 3 of Set 10 | khonuu
    City not found. Skipping...
    Processing Record 4 of Set 10 | hami
    Processing Record 5 of Set 10 | nova era
    Processing Record 6 of Set 10 | nipawin
    Processing Record 7 of Set 10 | butte
    Processing Record 8 of Set 10 | tucupita
    Processing Record 9 of Set 10 | bloomfield
    Processing Record 10 of Set 10 | hohhot
    Processing Record 11 of Set 10 | mizdah
    Processing Record 12 of Set 10 | visby
    Processing Record 13 of Set 10 | santa comba
    Processing Record 14 of Set 10 | chapleau
    Processing Record 15 of Set 10 | mitu
    Processing Record 16 of Set 10 | olinda
    Processing Record 17 of Set 10 | ormara
    Processing Record 18 of Set 10 | sur
    Processing Record 19 of Set 10 | esqueda
    Processing Record 20 of Set 10 | marataizes
    Processing Record 21 of Set 10 | bukachacha
    Processing Record 22 of Set 10 | tabou
    Processing Record 23 of Set 10 | nehe
    Processing Record 24 of Set 10 | alytus
    Processing Record 25 of Set 10 | yar-sale
    Processing Record 26 of Set 10 | tambul
    City not found. Skipping...
    Processing Record 27 of Set 10 | sindou
    Processing Record 28 of Set 10 | maniitsoq
    Processing Record 29 of Set 10 | chaman
    Processing Record 30 of Set 10 | west bay
    Processing Record 31 of Set 10 | gogrial
    Processing Record 32 of Set 10 | san quintin
    Processing Record 33 of Set 10 | presidencia roque saenz pena
    Processing Record 34 of Set 10 | defiance
    Processing Record 35 of Set 10 | mut
    Processing Record 36 of Set 10 | soe
    Processing Record 37 of Set 10 | sijunjung
    Processing Record 38 of Set 10 | udachnyy
    Processing Record 39 of Set 10 | egvekinot
    Processing Record 40 of Set 10 | sovetskiy
    Processing Record 41 of Set 10 | richards bay
    Processing Record 42 of Set 10 | bonavista
    Processing Record 43 of Set 10 | honiara
    Processing Record 44 of Set 10 | westport
    Processing Record 45 of Set 10 | garden city
    Processing Record 46 of Set 10 | sinjai
    Processing Record 47 of Set 10 | atocha
    Processing Record 48 of Set 10 | tigil
    Processing Record 49 of Set 10 | kaitangata
    Processing Record 0 of Set 11 | sikonge
    Processing Record 1 of Set 11 | turayf
    Processing Record 2 of Set 11 | la ronge
    Processing Record 3 of Set 11 | ushtobe
    Processing Record 4 of Set 11 | nikolskoye
    Processing Record 5 of Set 11 | manalurpet
    City not found. Skipping...
    Processing Record 6 of Set 11 | vrangel
    Processing Record 7 of Set 11 | kemijarvi
    Processing Record 8 of Set 11 | srednekolymsk
    Processing Record 9 of Set 11 | barabinsk
    Processing Record 10 of Set 11 | orapa
    Processing Record 11 of Set 11 | katsuura
    Processing Record 12 of Set 11 | havre-saint-pierre
    Processing Record 13 of Set 11 | mumbwa
    Processing Record 14 of Set 11 | ulaanbaatar
    Processing Record 15 of Set 11 | homer
    Processing Record 16 of Set 11 | waddan
    Processing Record 17 of Set 11 | east stroudsburg
    Processing Record 18 of Set 11 | zhigansk
    Processing Record 19 of Set 11 | kavaratti
    Processing Record 20 of Set 11 | sakakah
    Processing Record 21 of Set 11 | karatau
    Processing Record 22 of Set 11 | menongue
    Processing Record 23 of Set 11 | bako
    Processing Record 24 of Set 11 | malwan
    City not found. Skipping...
    Processing Record 25 of Set 11 | ilhabela
    Processing Record 26 of Set 11 | klaksvik
    Processing Record 27 of Set 11 | palabuhanratu
    City not found. Skipping...
    Processing Record 28 of Set 11 | narasannapeta
    Processing Record 29 of Set 11 | bronderslev
    Processing Record 30 of Set 11 | oakdale
    Processing Record 31 of Set 11 | gizo
    Processing Record 32 of Set 11 | kologriv
    Processing Record 33 of Set 11 | matara
    Processing Record 34 of Set 11 | puerto escondido
    Processing Record 35 of Set 11 | longhua
    Processing Record 36 of Set 11 | mandalgovi
    Processing Record 37 of Set 11 | saint-pierre
    Processing Record 38 of Set 11 | anito
    Processing Record 39 of Set 11 | grand gaube
    Processing Record 40 of Set 11 | redmond
    Processing Record 41 of Set 11 | concepcion del uruguay
    Processing Record 42 of Set 11 | warqla
    City not found. Skipping...
    Processing Record 43 of Set 11 | pacific grove
    Processing Record 44 of Set 11 | bata
    Processing Record 45 of Set 11 | pompeia
    Processing Record 46 of Set 11 | destin
    Processing Record 47 of Set 11 | kota bahru
    Processing Record 48 of Set 11 | sisimiut
    Processing Record 49 of Set 11 | diu
    Processing Record 0 of Set 12 | centenario do sul
    Processing Record 1 of Set 12 | qeshm
    Processing Record 2 of Set 12 | kodino
    Processing Record 3 of Set 12 | axim
    Processing Record 4 of Set 12 | ende
    Processing Record 5 of Set 12 | mount isa
    Processing Record 6 of Set 12 | te anau
    Processing Record 7 of Set 12 | tibu
    Processing Record 8 of Set 12 | misawa
    Processing Record 9 of Set 12 | pitimbu
    Processing Record 10 of Set 12 | mimongo
    Processing Record 11 of Set 12 | padang
    Processing Record 12 of Set 12 | port hedland
    Processing Record 13 of Set 12 | byron bay
    Processing Record 14 of Set 12 | venado tuerto
    Processing Record 15 of Set 12 | tarko-sale
    Processing Record 16 of Set 12 | itapemirim
    Processing Record 17 of Set 12 | stonehaven
    Processing Record 18 of Set 12 | luorong
    Processing Record 19 of Set 12 | sabang
    Processing Record 20 of Set 12 | otradnoye
    Processing Record 21 of Set 12 | taltal
    Processing Record 22 of Set 12 | barguzin
    Processing Record 23 of Set 12 | alabaster
    Processing Record 24 of Set 12 | tucuman
    Processing Record 25 of Set 12 | ust-nera
    Processing Record 26 of Set 12 | varberg
    Processing Record 27 of Set 12 | nioro
    Processing Record 28 of Set 12 | nosy varika
    Processing Record 29 of Set 12 | yunjinghong
    City not found. Skipping...
    Processing Record 30 of Set 12 | halalo
    City not found. Skipping...
    Processing Record 31 of Set 12 | terrace
    Processing Record 32 of Set 12 | maceio
    Processing Record 33 of Set 12 | haimen
    Processing Record 34 of Set 12 | yurkivka
    Processing Record 35 of Set 12 | muroto
    Processing Record 36 of Set 12 | santa fe
    Processing Record 37 of Set 12 | luanda
    Processing Record 38 of Set 12 | hit
    Processing Record 39 of Set 12 | solnechnyy
    Processing Record 40 of Set 12 | antalaha
    Processing Record 41 of Set 12 | stralsund
    Processing Record 42 of Set 12 | mareeba
    Processing Record 43 of Set 12 | eugene
    Processing Record 44 of Set 12 | berdigestyakh
    Processing Record 45 of Set 12 | dunedin
    Processing Record 46 of Set 12 | saint-georges
    Processing Record 47 of Set 12 | korbach
    Processing Record 48 of Set 12 | amga
    Processing Record 49 of Set 12 | vao
    Processing Record 0 of Set 13 | ewa beach
    Processing Record 1 of Set 13 | safaqis
    City not found. Skipping...
    Processing Record 2 of Set 13 | ye
    City not found. Skipping...
    Processing Record 3 of Set 13 | iskateley
    Processing Record 4 of Set 13 | pingdu
    Processing Record 5 of Set 13 | cabedelo
    Processing Record 6 of Set 13 | pestovo
    Processing Record 7 of Set 13 | chiredzi
    Processing Record 8 of Set 13 | wakkanai
    Processing Record 9 of Set 13 | masterton
    Processing Record 10 of Set 13 | porto nacional
    Processing Record 11 of Set 13 | vardo
    Processing Record 12 of Set 13 | santa rosa
    Processing Record 13 of Set 13 | rovaniemi
    Processing Record 14 of Set 13 | pietarsaari
    Processing Record 15 of Set 13 | barahona
    Processing Record 16 of Set 13 | sogdiondon
    City not found. Skipping...
    Processing Record 17 of Set 13 | quatre cocos
    Processing Record 18 of Set 13 | ust-kamchatsk
    City not found. Skipping...
    Processing Record 19 of Set 13 | piacabucu
    -----------------------------
    Data Retrieval Complete      
    -----------------------------
    


```python
# Convert array of JSONs into Pandas DataFrame
city_data_pd = pd.DataFrame(city_data)

# Show Record Count
city_data_pd.count()
```




    City          570
    Lat           570
    Lng           570
    Max Temp      570
    Humidity      570
    Cloudiness    570
    Wind Speed    570
    Country       570
    Date          570
    dtype: int64




```python
# Display the City Data Frame
city_data_pd.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>Country</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>port elizabeth</td>
      <td>-33.92</td>
      <td>25.57</td>
      <td>62.60</td>
      <td>82</td>
      <td>12</td>
      <td>11.41</td>
      <td>ZA</td>
      <td>1605389503</td>
    </tr>
    <tr>
      <td>1</td>
      <td>albany</td>
      <td>42.60</td>
      <td>-73.97</td>
      <td>44.01</td>
      <td>50</td>
      <td>18</td>
      <td>8.28</td>
      <td>US</td>
      <td>1605389757</td>
    </tr>
    <tr>
      <td>2</td>
      <td>port alfred</td>
      <td>-33.59</td>
      <td>26.89</td>
      <td>63.28</td>
      <td>86</td>
      <td>87</td>
      <td>8.90</td>
      <td>ZA</td>
      <td>1605389759</td>
    </tr>
    <tr>
      <td>3</td>
      <td>sitka</td>
      <td>57.05</td>
      <td>-135.33</td>
      <td>37.40</td>
      <td>86</td>
      <td>90</td>
      <td>4.70</td>
      <td>US</td>
      <td>1605389695</td>
    </tr>
    <tr>
      <td>4</td>
      <td>khatanga</td>
      <td>71.97</td>
      <td>102.50</td>
      <td>3.52</td>
      <td>94</td>
      <td>100</td>
      <td>19.80</td>
      <td>RU</td>
      <td>1605389759</td>
    </tr>
  </tbody>
</table>
</div>



## Inspect the data and remove the cities where the humidity > 100%.
----
Skip this step if there are no cities that have humidity > 100%. 


```python
city_data_pd.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>count</td>
      <td>570.000000</td>
      <td>570.000000</td>
      <td>570.000000</td>
      <td>570.000000</td>
      <td>570.000000</td>
      <td>570.000000</td>
      <td>5.700000e+02</td>
    </tr>
    <tr>
      <td>mean</td>
      <td>19.392158</td>
      <td>17.602333</td>
      <td>56.758895</td>
      <td>74.584211</td>
      <td>49.696491</td>
      <td>8.313088</td>
      <td>1.605390e+09</td>
    </tr>
    <tr>
      <td>std</td>
      <td>33.671165</td>
      <td>89.956414</td>
      <td>24.471179</td>
      <td>19.206878</td>
      <td>38.889879</td>
      <td>6.007553</td>
      <td>6.418650e+01</td>
    </tr>
    <tr>
      <td>min</td>
      <td>-54.800000</td>
      <td>-179.170000</td>
      <td>-23.840000</td>
      <td>10.000000</td>
      <td>0.000000</td>
      <td>0.490000</td>
      <td>1.605389e+09</td>
    </tr>
    <tr>
      <td>25%</td>
      <td>-8.885000</td>
      <td>-58.547500</td>
      <td>41.000000</td>
      <td>66.000000</td>
      <td>9.250000</td>
      <td>4.260000</td>
      <td>1.605390e+09</td>
    </tr>
    <tr>
      <td>50%</td>
      <td>22.430000</td>
      <td>21.060000</td>
      <td>64.565000</td>
      <td>80.000000</td>
      <td>41.000000</td>
      <td>6.930000</td>
      <td>1.605390e+09</td>
    </tr>
    <tr>
      <td>75%</td>
      <td>47.797500</td>
      <td>99.605000</td>
      <td>76.840000</td>
      <td>88.000000</td>
      <td>90.000000</td>
      <td>10.740000</td>
      <td>1.605390e+09</td>
    </tr>
    <tr>
      <td>max</td>
      <td>78.220000</td>
      <td>179.320000</td>
      <td>93.200000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>35.570000</td>
      <td>1.605390e+09</td>
    </tr>
  </tbody>
</table>
</div>




```python
#  Get the indices of cities that have humidity over 100%.
dirty_city_data = city_data_pd[(city_data_pd["Humidity"] > 100)].index
                                  
dirty_city_data
```




    Int64Index([], dtype='int64')




```python
# Make a new DataFrame equal to the city data to drop all humidity outliers by index.
# Passing "inplace=False" will make a copy of the city_data DataFrame, which we call "clean_city_data".
clean_city_data = city_data_pd.drop(dirty_city_data, inplace=False)
clean_city_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>Country</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>port elizabeth</td>
      <td>-33.92</td>
      <td>25.57</td>
      <td>62.60</td>
      <td>82</td>
      <td>12</td>
      <td>11.41</td>
      <td>ZA</td>
      <td>1605389503</td>
    </tr>
    <tr>
      <td>1</td>
      <td>albany</td>
      <td>42.60</td>
      <td>-73.97</td>
      <td>44.01</td>
      <td>50</td>
      <td>18</td>
      <td>8.28</td>
      <td>US</td>
      <td>1605389757</td>
    </tr>
    <tr>
      <td>2</td>
      <td>port alfred</td>
      <td>-33.59</td>
      <td>26.89</td>
      <td>63.28</td>
      <td>86</td>
      <td>87</td>
      <td>8.90</td>
      <td>ZA</td>
      <td>1605389759</td>
    </tr>
    <tr>
      <td>3</td>
      <td>sitka</td>
      <td>57.05</td>
      <td>-135.33</td>
      <td>37.40</td>
      <td>86</td>
      <td>90</td>
      <td>4.70</td>
      <td>US</td>
      <td>1605389695</td>
    </tr>
    <tr>
      <td>4</td>
      <td>khatanga</td>
      <td>71.97</td>
      <td>102.50</td>
      <td>3.52</td>
      <td>94</td>
      <td>100</td>
      <td>19.80</td>
      <td>RU</td>
      <td>1605389759</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Extract relevant fields from the data frame
lats = clean_city_data["Lat"]
max_temps = clean_city_data["Max Temp"]
humidity = clean_city_data["Humidity"]
cloudiness = clean_city_data["Cloudiness"]
wind_speed = clean_city_data["Wind Speed"]

# Export the City_Data into a csv
clean_city_data.to_csv(output_data_file, index_label="City_ID")
```

## Latitude vs. Temperature Plot


```python
# Build scatter plot for latitude vs. temperature
plt.scatter(lats, 
            max_temps,
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Cities")

# Incorporate the other graph properties
plt.title("City Latitude vs. Max Temperature (%s)" % time.strftime("%x"))
plt.ylabel("Max Temperature (F)")
plt.xlabel("Latitude")
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig1.png")

# Show plot
plt.show()
```


![png](output_14_0.png)


## Latitude vs. Humidity Plot


```python
# Build the scatter plots for latitude vs. humidity

# Incorporate the other graph properties

# Save the figure

# Show plot

```


![png](output_16_0.png)


## Latitude vs. Cloudiness Plot


```python
# Build the scatter plots for latitude vs. cloudiness

# Incorporate the other graph properties

# Save the figure

# Show plot

```


![png](output_18_0.png)


## Latitude vs. Wind Speed Plot


```python
# Build the scatter plots for latitude vs. wind speed

# Incorporate the other graph properties

# Save the figure

# Show plot

```


![png](output_20_0.png)


## Linear Regression


```python
# Create a function to create Linear Regression plots
def plot_linear_regression(x_values, y_values, title, text_coordinates):
    
    # Run regresson on southern hemisphere
    (slope, intercept, rvalue, pvalue, stderr) = linregress(x_values, y_values)
    regress_values = x_values * slope + intercept
    line_eq = "y = " + str(round(slope,2)) + "x + " + str(round(intercept,2))

    # Plot
    plt.scatter(x_values,y_values)
    plt.plot(x_values,regress_values,"r-")
    plt.annotate(line_eq,text_coordinates,fontsize=15,color="red")
    plt.xlabel('Latitude')
    plt.ylabel(title)
    print(f"The r-value is: {rvalue**2}")
    plt.show()
```


```python
# Create Northern and Southern Hemisphere DataFrames
northern_hemi_df = city_data_pd.loc[(city_data_pd["Lat"] >= 0)]
southern_hemi_df = city_data_pd.loc[(city_data_pd["Lat"] < 0)]
```

###  Max Temp vs. Latitude Linear Regression


```python
# Linear regression on Northern Hemisphere
x_values = northern_hemi_df["Lat"]
y_values = northern_hemi_df["Max Temp"]
plot_linear_regression(x_values, y_values, 'Max Temp',(6,30))
```

    The r-value is: 0.7439269848110541
    


![png](output_25_1.png)



```python
# Linear regression on Southern Hemisphere

```

    The r-value is: 0.44378855125023653
    


![png](output_26_1.png)


The high r value indicates a strong positive correlation between latitude and max temperature. R values ~ 0.5 indicate that there is a moderate positive correlation. 

### Humidity (%) vs. Latitude Linear Regression


```python
# Northern Hemisphere

```

    The r-value is: 0.10791483720343435
    


![png](output_29_1.png)



```python
# Southern Hemisphere
x_values = southern_hemi_df["Lat"]
y_values = southern_hemi_df["Humidity"]
plot_linear_regression(x_values, y_values, 'Humidity', (-50, 20))
```

    The r-value is: 0.07333023089550374
    


![png](output_30_1.png)


The low r values indicate a weak to no relationship between humidity and latitude.

### Cloudiness (%) vs. Latitude Linear Regression


```python
# Northern Hemisphere

```

    The r-value is: 0.06168108017754348
    


![png](output_33_1.png)



```python
# Southern Hemisphere

```

    The r-value is: 4.472648335161714e-05
    


![png](output_34_1.png)


The low r values indicate a weak positive relationship between latitude and cloudiness.

### Wind Speed (mph) vs. Latitude Linear Regression


```python
# Northern Hemisphere

```

    The r-value is: 0.05368478377790392
    


![png](output_37_1.png)



```python
# Southern Hemisphere

```

    The r-value is: 0.03406158572750718
    


![png](output_38_1.png)


The low r values indicate that there is no real relationship between wind speed and latitude." The difference between the hemispheres doesn't seem to be significant enough to comment upon.
