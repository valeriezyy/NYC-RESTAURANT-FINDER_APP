# NYC-RESTAURANT-FINDER_APP
Business case: This app allows people in New York City to look up the closest dining establishments to them based on their health ratings and cuisine choice. 

Data source: Department of Health and Mental Hygiene (DOHMH) New York City Restaurant Inspection Results (https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/43nn-pn8j)


Grade definitions: 
A-C = Range of health grades provided to restaurants
N = Not yet graded
Z = Grade pending 
P = Grade pending issued on re-opening following an initial inspection that resulted in a closure

How to use the app:
Choose the cuisine type
Choose the grade of the restaurant that you want
Based on a function that pulls in your longitude and latitude based on your IP address, results will automatically update in the map and be listed in order of proximity

Required Libraries or Imports:
Pandas for data analytics and manipulation
Matplotlib.pyplot for mapping in Python
Ipywidgets and ipyleaflet for python widgets
Requests for making HTTP requests
Json for decoding JSON files 

What’s happening in the app:


1. Data Cleanup：
 
From DOHMH’s raw data, there are some rows with NULL values and some columns are less essential to the whole project. Before digging into the data, we first need to make sure that there are no duplicates and remove all useless punctuations.
First, we chose to limit useful information on restaurant grades A, B, C, and remove rows with unclear grades, such as grades Z, P, N. Second, after doing some simple early exploratory data analysis, we concluded that over ⅔ of the restaurants are in the top 20 popular cuisines type list. Thus, we decided to choose to put cuisines in the type list as our targets.

The top 20 popular cuisines type list has: American, Chinese, Café/Coffee/Tea, Pizza, Mexican, Italian, Latin (Cuban, Dominican, Puerto Rican, South & Central American), Japanese, Bakery, Caribbean, Spanish, Pizza/Italian, Chicken, Donuts, Indian, Asian, Delicatessen, Jewish/Kosher, Hamburgers, Thai

2. Location UDF:

In order to obtain the user’s location, we have used Geocoding. Geocoding is the process of converting geographic coordinates like latitude and longitude. We have written a user-defined function, which first acquires the IP address of the user and then processes to convert the IP address to geodata, which consists of all information of the user’s location such as area code, continent code, country, country code, IP address, latitude, longitude, and timezone. Since we need only latitude and longitude for the algorithm to calculate the distances from the restaurants, the UDF returns only latitude and longitude. The package requests are used to get the IP address of the user and the package json is used to obtain the geodata from the IP address.

3. User Interface & Output Mapping:

To let users input their selections, we chose to build interactive widgets in Jupyter Notebook, using package ipywidgets and ipyleaflet to create a user-friendly input interface. A user can firstly select a cuisine type with a preferred restaurant grade. Then the map will mark the top five restaurants and a data frame will pop up to display more detailed information about the restaurants.
 
Based on a grid layout, we added two selection boxes for users to select cuisine type and grade for restaurants. Then we added an OpenStreetMap to map the top five qualified restaurants. Besides, we added the filtered data frame at the bottom. 
 
To display the restaurants as user-selected, we created a common_filtering() function. It firstly removes any additional markers on the map, that is created from the last user selection. Then it takes the geolocation returned from UDF, and it runs the coordinates through the algorithm. After getting the filtered data frame, we added markers on the map for each record according to their longitude and latitude. For each widget on the grid layout, we wrote an eventhandler() function and an observe() function, which observes any changes from the selection boxes and handles any event by calling the common_filtering() function with the newly selected value. By running new selections through common_filtering(), both the map and the filtered data frame will update immediately.

4. Matching Algorithm:

Matching Algorithm essentially filters the results. First, there are multiple entries for restaurants, as restaurants can be tested multiple times, so the algorithm will retain only last grade and lose all history. Next, filter the restaurant cuisine and grade chosen by the user. Then, calculate the proximity of the resulting restaurants from the user and store in. Do so through this formula:
Distance in latitude between user to restaurant (absolute value of difference)
Distance in longitude between user and restaurant (absolute value of difference)
      =   Total distance in degrees
      x    68.703 miles per degree to convert to miles    
      =   Total Distance in miles

Lastly, add that distance as a datapoint for each restaurant and report back the restaurant, street address and boro, grade and grade date, the location and distance, which is what it is sorted by. This facilitates the user’s need to have the closest restaurants with the criteria entered, displaying the 5 closest restaurants based on cuisine and grade selection made.

