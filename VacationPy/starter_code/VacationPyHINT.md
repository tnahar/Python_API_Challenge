```python
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import gmaps
import os

# Import API key
from api_keys import g_key
```

### Store Part I results into DataFrame
* Load the csv exported in Part I to a DataFrame


```python
# Store csv created in part one into a DataFrame
city_data_df = pd.read_csv("output_data/cities.csv")
city_data_df.head()
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
      <th>City_ID</th>
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



### Humidity Heatmap
* Configure gmaps.
* Use the Lat and Lng as locations and Humidity as the weight.
* Add Heatmap layer to map.


```python
# Configure gmaps
gmaps.configure(api_key=g_key)
```


```python
# Heatmap of humidity
locations = city_data_df[["Lat", "Lng"]]
humidity = city_data_df["Humidity"]
fig = gmaps.figure()
heat_layer = gmaps.heatmap_layer(locations, weights=humidity, dissipating=False, max_intensity=300, point_radius=5)

fig.add_layer(heat_layer)

fig
```


    Figure(layout=FigureLayout(height='420px'))


### Create new DataFrame fitting weather criteria
* Narrow down the cities to fit weather conditions.
* Drop any rows will null values.


```python
# Narrow down cities that fit criteria and drop any results with null values
narrowed_city_df = city_data_df.loc[(city_data_df["Max Temp"] < 80) & (city_data_df["Max Temp"] > 70) \
                                    & (city_data_df["Wind Speed"] < 10) \
                                    & (city_data_df["Cloudiness"] == 0)].dropna()
narrowed_city_df
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
      <th>City_ID</th>
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
      <td>13</td>
      <td>13</td>
      <td>chambar</td>
      <td>25.29</td>
      <td>68.81</td>
      <td>73.17</td>
      <td>43</td>
      <td>0</td>
      <td>9.01</td>
      <td>PK</td>
      <td>1605389760</td>
    </tr>
    <tr>
      <td>50</td>
      <td>50</td>
      <td>xuddur</td>
      <td>4.12</td>
      <td>43.89</td>
      <td>74.17</td>
      <td>67</td>
      <td>0</td>
      <td>8.08</td>
      <td>SO</td>
      <td>1605389763</td>
    </tr>
    <tr>
      <td>67</td>
      <td>67</td>
      <td>vallenar</td>
      <td>-28.57</td>
      <td>-70.76</td>
      <td>73.00</td>
      <td>30</td>
      <td>0</td>
      <td>7.81</td>
      <td>CL</td>
      <td>1605389765</td>
    </tr>
    <tr>
      <td>88</td>
      <td>88</td>
      <td>port macquarie</td>
      <td>-31.43</td>
      <td>152.92</td>
      <td>73.99</td>
      <td>88</td>
      <td>0</td>
      <td>5.82</td>
      <td>AU</td>
      <td>1605389766</td>
    </tr>
    <tr>
      <td>115</td>
      <td>115</td>
      <td>gaoua</td>
      <td>10.30</td>
      <td>-3.25</td>
      <td>78.35</td>
      <td>28</td>
      <td>0</td>
      <td>5.19</td>
      <td>BF</td>
      <td>1605389768</td>
    </tr>
    <tr>
      <td>136</td>
      <td>136</td>
      <td>shankargarh</td>
      <td>25.18</td>
      <td>81.62</td>
      <td>70.25</td>
      <td>52</td>
      <td>0</td>
      <td>4.12</td>
      <td>IN</td>
      <td>1605389770</td>
    </tr>
    <tr>
      <td>155</td>
      <td>155</td>
      <td>adolfo lopez mateos</td>
      <td>28.47</td>
      <td>-107.30</td>
      <td>73.00</td>
      <td>10</td>
      <td>0</td>
      <td>8.99</td>
      <td>MX</td>
      <td>1605389772</td>
    </tr>
    <tr>
      <td>161</td>
      <td>161</td>
      <td>poum</td>
      <td>-20.23</td>
      <td>164.02</td>
      <td>78.28</td>
      <td>80</td>
      <td>0</td>
      <td>7.52</td>
      <td>NC</td>
      <td>1605389773</td>
    </tr>
    <tr>
      <td>192</td>
      <td>192</td>
      <td>hambantota</td>
      <td>6.12</td>
      <td>81.12</td>
      <td>77.00</td>
      <td>100</td>
      <td>0</td>
      <td>3.36</td>
      <td>LK</td>
      <td>1605389775</td>
    </tr>
    <tr>
      <td>214</td>
      <td>214</td>
      <td>flinders</td>
      <td>-34.58</td>
      <td>150.86</td>
      <td>72.00</td>
      <td>86</td>
      <td>0</td>
      <td>1.01</td>
      <td>AU</td>
      <td>1605389777</td>
    </tr>
    <tr>
      <td>234</td>
      <td>234</td>
      <td>aguimes</td>
      <td>27.91</td>
      <td>-15.45</td>
      <td>73.40</td>
      <td>69</td>
      <td>0</td>
      <td>4.70</td>
      <td>ES</td>
      <td>1605389779</td>
    </tr>
    <tr>
      <td>239</td>
      <td>239</td>
      <td>salalah</td>
      <td>17.02</td>
      <td>54.09</td>
      <td>73.40</td>
      <td>56</td>
      <td>0</td>
      <td>3.36</td>
      <td>OM</td>
      <td>1605389660</td>
    </tr>
    <tr>
      <td>262</td>
      <td>262</td>
      <td>grand-lahou</td>
      <td>5.14</td>
      <td>-5.02</td>
      <td>79.03</td>
      <td>85</td>
      <td>0</td>
      <td>6.15</td>
      <td>CI</td>
      <td>1605389781</td>
    </tr>
    <tr>
      <td>313</td>
      <td>313</td>
      <td>saint-louis</td>
      <td>16.33</td>
      <td>-15.00</td>
      <td>78.80</td>
      <td>57</td>
      <td>0</td>
      <td>8.08</td>
      <td>SN</td>
      <td>1605389786</td>
    </tr>
    <tr>
      <td>387</td>
      <td>387</td>
      <td>malakal</td>
      <td>9.53</td>
      <td>31.66</td>
      <td>79.03</td>
      <td>31</td>
      <td>0</td>
      <td>4.76</td>
      <td>SS</td>
      <td>1605389792</td>
    </tr>
    <tr>
      <td>446</td>
      <td>446</td>
      <td>soe</td>
      <td>-9.86</td>
      <td>124.28</td>
      <td>71.53</td>
      <td>56</td>
      <td>0</td>
      <td>2.26</td>
      <td>ID</td>
      <td>1605389797</td>
    </tr>
    <tr>
      <td>485</td>
      <td>485</td>
      <td>narasannapeta</td>
      <td>18.42</td>
      <td>84.05</td>
      <td>72.68</td>
      <td>71</td>
      <td>0</td>
      <td>6.73</td>
      <td>IN</td>
      <td>1605389801</td>
    </tr>
    <tr>
      <td>518</td>
      <td>518</td>
      <td>port hedland</td>
      <td>-20.32</td>
      <td>118.57</td>
      <td>75.20</td>
      <td>69</td>
      <td>0</td>
      <td>8.05</td>
      <td>AU</td>
      <td>1605389803</td>
    </tr>
    <tr>
      <td>533</td>
      <td>533</td>
      <td>nioro</td>
      <td>13.35</td>
      <td>-15.75</td>
      <td>77.70</td>
      <td>21</td>
      <td>0</td>
      <td>0.69</td>
      <td>GM</td>
      <td>1605389804</td>
    </tr>
    <tr>
      <td>559</td>
      <td>559</td>
      <td>chiredzi</td>
      <td>-21.05</td>
      <td>31.67</td>
      <td>70.21</td>
      <td>51</td>
      <td>0</td>
      <td>6.06</td>
      <td>ZW</td>
      <td>1605389806</td>
    </tr>
  </tbody>
</table>
</div>



### Hotel Map
* Store into variable named `hotel_df`.
* Add a "Hotel Name" column to the DataFrame.
* Set parameters to search for hotels with 5000 meters.
* Hit the Google Places API for each city's coordinates.
* Store the first Hotel result into the DataFrame.
* Plot markers on top of the heatmap.


```python
# Create DataFrame called hotel_df to store hotel names along with city, country and coordinates

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
      <th>Country</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Hotel Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>13</td>
      <td>chambar</td>
      <td>PK</td>
      <td>25.29</td>
      <td>68.81</td>
      <td></td>
    </tr>
    <tr>
      <td>50</td>
      <td>xuddur</td>
      <td>SO</td>
      <td>4.12</td>
      <td>43.89</td>
      <td></td>
    </tr>
    <tr>
      <td>67</td>
      <td>vallenar</td>
      <td>CL</td>
      <td>-28.57</td>
      <td>-70.76</td>
      <td></td>
    </tr>
    <tr>
      <td>88</td>
      <td>port macquarie</td>
      <td>AU</td>
      <td>-31.43</td>
      <td>152.92</td>
      <td></td>
    </tr>
    <tr>
      <td>115</td>
      <td>gaoua</td>
      <td>BF</td>
      <td>10.30</td>
      <td>-3.25</td>
      <td></td>
    </tr>
    <tr>
      <td>136</td>
      <td>shankargarh</td>
      <td>IN</td>
      <td>25.18</td>
      <td>81.62</td>
      <td></td>
    </tr>
    <tr>
      <td>155</td>
      <td>adolfo lopez mateos</td>
      <td>MX</td>
      <td>28.47</td>
      <td>-107.30</td>
      <td></td>
    </tr>
    <tr>
      <td>161</td>
      <td>poum</td>
      <td>NC</td>
      <td>-20.23</td>
      <td>164.02</td>
      <td></td>
    </tr>
    <tr>
      <td>192</td>
      <td>hambantota</td>
      <td>LK</td>
      <td>6.12</td>
      <td>81.12</td>
      <td></td>
    </tr>
    <tr>
      <td>214</td>
      <td>flinders</td>
      <td>AU</td>
      <td>-34.58</td>
      <td>150.86</td>
      <td></td>
    </tr>
    <tr>
      <td>234</td>
      <td>aguimes</td>
      <td>ES</td>
      <td>27.91</td>
      <td>-15.45</td>
      <td></td>
    </tr>
    <tr>
      <td>239</td>
      <td>salalah</td>
      <td>OM</td>
      <td>17.02</td>
      <td>54.09</td>
      <td></td>
    </tr>
    <tr>
      <td>262</td>
      <td>grand-lahou</td>
      <td>CI</td>
      <td>5.14</td>
      <td>-5.02</td>
      <td></td>
    </tr>
    <tr>
      <td>313</td>
      <td>saint-louis</td>
      <td>SN</td>
      <td>16.33</td>
      <td>-15.00</td>
      <td></td>
    </tr>
    <tr>
      <td>387</td>
      <td>malakal</td>
      <td>SS</td>
      <td>9.53</td>
      <td>31.66</td>
      <td></td>
    </tr>
    <tr>
      <td>446</td>
      <td>soe</td>
      <td>ID</td>
      <td>-9.86</td>
      <td>124.28</td>
      <td></td>
    </tr>
    <tr>
      <td>485</td>
      <td>narasannapeta</td>
      <td>IN</td>
      <td>18.42</td>
      <td>84.05</td>
      <td></td>
    </tr>
    <tr>
      <td>518</td>
      <td>port hedland</td>
      <td>AU</td>
      <td>-20.32</td>
      <td>118.57</td>
      <td></td>
    </tr>
    <tr>
      <td>533</td>
      <td>nioro</td>
      <td>GM</td>
      <td>13.35</td>
      <td>-15.75</td>
      <td></td>
    </tr>
    <tr>
      <td>559</td>
      <td>chiredzi</td>
      <td>ZW</td>
      <td>-21.05</td>
      <td>31.67</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
# Set parameters to search for a hotel
params = {
    "radius": 5000,
    "types": "lodging",
    "key": g_key
}

# Iterate through 
for index, row in hotel_df.iterrows():
    # get lat, lng from df
    lat = row["Lat"]
    lng = row["Lng"]
    
    params["location"] = f"{lat},{lng}"
    
    # Use the search term: "Hotel" and our lat/lng
    # your-code-here

    # make request and print url
    # your-code-here
        
    # convert to json
    # your-code-here    
    
    # Grab the first hotel from the results and store the name
    try:
        hotel_df.loc[index, "Hotel Name"] = name_address["results"][0]["name"]
    except (KeyError, IndexError):
        print("Missing field/result... skipping.")

hotel_df
```

    Missing field/result... skipping.
    Missing field/result... skipping.
    Missing field/result... skipping.
    




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
      <th>Country</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Hotel Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>13</td>
      <td>chambar</td>
      <td>PK</td>
      <td>25.29</td>
      <td>68.81</td>
      <td>Village Haji Abdullah Laghari</td>
    </tr>
    <tr>
      <td>50</td>
      <td>xuddur</td>
      <td>SO</td>
      <td>4.12</td>
      <td>43.89</td>
      <td>Banjanay Hotel</td>
    </tr>
    <tr>
      <td>67</td>
      <td>vallenar</td>
      <td>CL</td>
      <td>-28.57</td>
      <td>-70.76</td>
      <td>Humacao Bed &amp; Breakfast</td>
    </tr>
    <tr>
      <td>88</td>
      <td>port macquarie</td>
      <td>AU</td>
      <td>-31.43</td>
      <td>152.92</td>
      <td>Rydges Port Macquarie</td>
    </tr>
    <tr>
      <td>115</td>
      <td>gaoua</td>
      <td>BF</td>
      <td>10.30</td>
      <td>-3.25</td>
      <td></td>
    </tr>
    <tr>
      <td>136</td>
      <td>shankargarh</td>
      <td>IN</td>
      <td>25.18</td>
      <td>81.62</td>
      <td>Sagar Guest House</td>
    </tr>
    <tr>
      <td>155</td>
      <td>adolfo lopez mateos</td>
      <td>MX</td>
      <td>28.47</td>
      <td>-107.30</td>
      <td>Hotel Victor Inn</td>
    </tr>
    <tr>
      <td>161</td>
      <td>poum</td>
      <td>NC</td>
      <td>-20.23</td>
      <td>164.02</td>
      <td>Gîte kajeon</td>
    </tr>
    <tr>
      <td>192</td>
      <td>hambantota</td>
      <td>LK</td>
      <td>6.12</td>
      <td>81.12</td>
      <td>Bungalow 63</td>
    </tr>
    <tr>
      <td>214</td>
      <td>flinders</td>
      <td>AU</td>
      <td>-34.58</td>
      <td>150.86</td>
      <td>Shellharbour Resort &amp; Conference Centre</td>
    </tr>
    <tr>
      <td>234</td>
      <td>aguimes</td>
      <td>ES</td>
      <td>27.91</td>
      <td>-15.45</td>
      <td>Hotel Villa de Aguimes</td>
    </tr>
    <tr>
      <td>239</td>
      <td>salalah</td>
      <td>OM</td>
      <td>17.02</td>
      <td>54.09</td>
      <td>HAMDAN PLAZA HOTEL</td>
    </tr>
    <tr>
      <td>262</td>
      <td>grand-lahou</td>
      <td>CI</td>
      <td>5.14</td>
      <td>-5.02</td>
      <td>Hotel Beatitude de Grand Lahou</td>
    </tr>
    <tr>
      <td>313</td>
      <td>saint-louis</td>
      <td>SN</td>
      <td>16.33</td>
      <td>-15.00</td>
      <td></td>
    </tr>
    <tr>
      <td>387</td>
      <td>malakal</td>
      <td>SS</td>
      <td>9.53</td>
      <td>31.66</td>
      <td>Nile Palace Hotel</td>
    </tr>
    <tr>
      <td>446</td>
      <td>soe</td>
      <td>ID</td>
      <td>-9.86</td>
      <td>124.28</td>
      <td>Dena Hotel</td>
    </tr>
    <tr>
      <td>485</td>
      <td>narasannapeta</td>
      <td>IN</td>
      <td>18.42</td>
      <td>84.05</td>
      <td>G.lingaraju home</td>
    </tr>
    <tr>
      <td>518</td>
      <td>port hedland</td>
      <td>AU</td>
      <td>-20.32</td>
      <td>118.57</td>
      <td>The Esplanade Hotel</td>
    </tr>
    <tr>
      <td>533</td>
      <td>nioro</td>
      <td>GM</td>
      <td>13.35</td>
      <td>-15.75</td>
      <td></td>
    </tr>
    <tr>
      <td>559</td>
      <td>chiredzi</td>
      <td>ZW</td>
      <td>-21.05</td>
      <td>31.67</td>
      <td>The Nesbitt Arms</td>
    </tr>
  </tbody>
</table>
</div>




```python
# NOTE: Do not change any of the code in this cell

# Using the template add the hotel marks to the heatmap
info_box_template = """
<dl>
<dt>Name</dt><dd>{Hotel Name}</dd>
<dt>City</dt><dd>{City}</dd>
<dt>Country</dt><dd>{Country}</dd>
</dl>
"""
# Store the DataFrame Row
hotel_info = [info_box_template.format(**row) for index, row in hotel_df.iterrows()]
locations = hotel_df[["Lat", "Lng"]]
```


```python
# Add marker layer ontop of heat map
marker_layer = gmaps.marker_layer(locations, info_box_content=hotel_info)
fig.add_layer(marker_layer)

# Display figure
fig
```


    Figure(layout=FigureLayout(height='420px'))

