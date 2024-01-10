# New York City Taxi Trip Duration
The project aims to predict the total ride duration of taxi trips in New York city. In cities like New york where the traffic is high and the distance between the destinations is short, everyone wants to reach their respective destinations as soon as possible.
The dependent variable in this project is “trip_duration” which is the duration of the trip in seconds. There are 10 independent variables or features which we will use in our hypothesis to generate our predictions. We have performed feature engineering, data exploration, time series analysis, model building and validation to build a low latency system to predict "Trip Duration" given pick up and drop off geographical co-ordinates.

![image](https://github.com/shanunDS/Group5-project/assets/66896800/016534b4-12b4-4670-932f-8716049e68fa)

## Team Members (Team 5):
-  Shanun Randev 
-  Anand Raj
- Mowzli Sre 
-  Bala krishna Reddy

## DataSet Details:

- Id: Unique identifier for each trip
- Vendor_id: Code indicating the provider associated with the trip record
- Pickup_datetime: Date and time when the meter was engaged
- Dropoff_datetime: Date and time when the meter was disengaged
- Passenger_count: Number of passengers in the vehicle
- Pickup_longitude: Longitude where the meter was engaged
- Pickup_latitude: Latitude where the meter was engaged
- Dropoff_latitude: Latitude where the meter was disengaged
- Dropoff_longitude: Longitude where the meter was disengaged
- Store_and_fwd_flag: Flag indicating whether the trip record was held in vehicle memory before sending to the vendor because the vehicle did not have a connection to the server
  - Y = store and forward
  - N = not a store and forward trip
- Target Variable:
  - Trip Duration (seconds)

## Data Analysis:
- How can we predict the total distance using the pickup and dropoff latitude and longitude, then examine the relationship between the two variables: trip duration and distance ?
- On what days and at what time during the day the trip duration is maximum ?
- What are the top 4 locations with high demand for taxis in NYC ?
- How do we compute the speed of vehicles and how is it related to the trip duration ?
### Observation on the Dataset

![image](https://github.com/shanunDS/Group5-project/assets/66896800/888d0266-a8ed-4757-a454-0ad729d0af82)

### Cleaning and Feature Engineering

![image](https://github.com/shanunDS/Group5-project/assets/66896800/276c96de-1209-4c9e-87bb-79d889886a9c)

### Data Cleaning and Preprocessing
#### Outlier Correction
- We are employing 1.5 IQR method which is used for identifying the outliers in a dataset
- The IQR is a measure of statistical dispersion, representing the range between the first quartile(Q1) and third quartile (Q3)
- Outliers are observations that fall below Q1 - 1.5*IQR or above Q3 + 1.5*IQR
  
![image](https://github.com/shanunDS/Group5-project/assets/66896800/8c3634e4-f54d-4c0c-bbe3-cdc6a09380be)

#### Cleaning of Passenger Counts
- Practical considerations were taken out as it is unlikely for a car to accommodate more than 6 passengers or no passenger
- All trips with 0 and above 6 passengers were removed from the dataset
- Implementation of a domain based outlier detection is a crucial data cleaning part to provide accurate modelling

#### Cleaning of trip durations
- A threshold of 100 hours was taken, for practicality
- Trip data exceeding this limit, were considered outlier and were removed from the dataset
- Trip data with more than 100 hours of trip durations are extreme outliers which may easily affect the accuracy of the modelling

### Haversine Formula
a = sin²(Δφ/2) + cos(φ₁) * cos(φ₂) * sin²(Δλ/2)
c = 2 * atan2(√a, √(1-a))
d = R * c
Where:
- φ₁ and φ₂ are the latitudes of the two points in radians.
- Δφ is the difference between the latitudes of the two points.
- Δλ is the difference between the longitudes of the two points.
- R is the radius of the Earth (mean radius = 6,371 kilometers).
The Haversine formula calculates the great-circle distance, which is the shortest distance over the Earth's surface between two points.
The result is the distance between the two points along the surface of the sphere.
This formula takes into account the curvature of the Earth and provides accurate distance calculations for geographic coordinates.

### Cleaning of Trip Distance
- A minimum meaningful trip distance of 1 meter was chosen
- Any trip distance less than 1 meter is considered as an outlier
- Other less trip distance like 2, 3, 4, etc were not clipped, because there are high chances of user cancelling the trip
- So outliers with short distance and very long timings were clipped

### Computing Directional Angle
- Calculating the compass bearing (direction) between two geographic points specified by their latitude and longitude coordinates.
- The Bearing is angle measured in degrees from the north direction in a clockwise direction.The haversine formula is often used for navigation and geographical applications.
- The resulting bearing is typically expressed as value between 0 and 360 degrees where 0 is north, 90 degrees is east, 180 degree is south and 270 degrees is west

### Feature Engineering
- From the dataset, many new columns were featured from the existing columns
- Hour of the day, day of the trip, week, month, direction of the trip, avg. traffic speed and more were calculated
- Presence of these variables tend to give more accuracy than the default variables from the dataset

### Density Plots after preprocessing of Data

![image](https://github.com/shanunDS/Group5-project/assets/66896800/efce6fdf-3329-43e9-a4d7-81545a406624)

### Correlation of Trip Duration with other features
Trip duration  has a strong positive correlation of 0.8 with trip_distance(km), which suggests that as the trip distance increases, the trip duration tends to increase as well

![image](https://github.com/shanunDS/Group5-project/assets/66896800/1349599b-892b-4c4e-be91-b073ef918260)

### Relationship of Trip Distance with Trip Duration
The scatter plot clearly shows a strong positive correlation with Trip Distance

The higher the Trip Distance, the chances of the trip Duration to extend is also high 

![image](https://github.com/shanunDS/Group5-project/assets/66896800/1e8f7bb9-d559-4aaa-b178-08375163c1d6)

## Time Series Analysis
- Few peaks and drops were seen in the Trend graph, where weather accounts for the trend
- By the end of Jan, 2016 New York saw it’s highest snowfall of 27 inches which accounted for the drop in taxi demands in early february
- The trend shows another bump in the series during the mid february, where Valentine’s day could be more accountable
- A gradual raise in the demands post summer may be accounted for peoples who travelled to different places in the early fall ( to longer distance )

![image](https://github.com/shanunDS/Group5-project/assets/66896800/1aae8867-232f-4424-acf4-61e84d691a75)

A regular pattern is observed in the Seasonal graph, 4 times for every month, which clearly shows the weekend demands
So attempted to analyse the weekly and daily trends throughout the dataset 

### Daily Taxi Ride Trends
We could observe, gradual increase in number of rides from Monday to Saturday
Friday and Saturday are busiest days in terms of ride demand, and there is drop on sunday compared to saturday.

![image](https://github.com/shanunDS/Group5-project/assets/66896800/f85859af-0dbe-4e56-bb16-767b93e0bb3a)

### Taxi Ride Trends by Hour
We could see that there are  less number of rides in early morning hours and a steady increase from the late morning
During the evening hours starting at 6 pm to 10 pm there is high demand for taxis

![image](https://github.com/shanunDS/Group5-project/assets/66896800/5b0eb8bd-9be2-4bd4-a99b-4ecd7b029745)

### Weekly Taxi Ride patterns
Each day we observe a similar pattern with 2 prominent peaks one occurring at 8 to 9 am and another at 6 to 7 pm
The evening peak tends to last longer than the morning peak
There is a sharper decline in rides after the evening peak on weekdays compared to weekends

![image](https://github.com/shanunDS/Group5-project/assets/66896800/fc6d633e-cd58-425e-ae85-554893a10b55)

## Demand Prediction using K-means clustering
### Demand Prediction

![image](https://github.com/shanunDS/Group5-project/assets/66896800/80083246-25dc-4e9f-9111-695cef96bb73)

### Determining ‘k’ for Clusters
- The elbow method is used to determine the number of clusters to be considered for fitting the K-means clustering.
- There is no significant improvement in the clustering after 10. 
- So, considering 10 clusters for fitting the K-means clustering.

![image](https://github.com/shanunDS/Group5-project/assets/66896800/1217656c-e60a-42a7-937a-44755854bd51)

### Fitting and Identifying the Clusters

![image](https://github.com/shanunDS/Group5-project/assets/66896800/fffc41e6-c4e7-4641-949f-00767ccd9f1c)

### Top Locations (Displayed using Folium)

![image](https://github.com/shanunDS/Group5-project/assets/66896800/58ffaf58-24a0-42fa-b14d-0b0c4ea2938f)

## Avg. Speed and Trip Duration Analysis
### Computing Avg. Traffic Speeds
- Avg. Traffic Speed = distance traveled / time taken
- The Avg. Speed of Traffic is relatively high in the morning
- Again, the traffic speed increases in the evening but less compared to morning
- The Avg. Traffic speed was found to be more during the weekends

![image](https://github.com/shanunDS/Group5-project/assets/66896800/088e4bf1-5cab-470d-9cec-6342f9b0c4cc)

### Linear Regression using CV
Performance Metric Used: “RMSE” (Root Mean Square Error)

- Root Mean Squared Error on Training Set: 317.35 
- RMSE for Each Fold (cross-validation): [318.51, 317.32, 317.31, 316.88, 317.55]
- Mean Root Mean Squared Error (cross-validation): 317.52
- Root Mean Squared Error on Test Set: 317.75

The RMSE value is around 317.75 seconds  (~5 Minutes), which is descent but to further improve the model performance we performed XGBoost.

### XGBoost
XGBoost without  Hyper Parameter Tuning
- Root Mean Squared Error on Training Set: 225.24
- Root Mean Squared Error on Test Set: 227.98

XGBoost with Hyper Parameter Tuning
- Root Mean Squared Error on Training Set: 221.09
- Root Mean Squared Error on Test Set: 224.68

Best Hyperparameters:   subsample: 0.9, n_estimators: 200, max_depth: 6, learning_rate: 0.2

### Compare LR vs XGBoost

![image](https://github.com/shanunDS/Group5-project/assets/66896800/fe12c505-f4da-4b39-986b-638ce45ed8ab)

## Conclusion
- Haversine Formula was used to compute the trip distance for a given coordinate
- We found that cluster around Manhattan had the more the pickup points, including Harlem, Wall Street, Columbus Circle and Manhattan Community Board
- We  analysed the time series and found the peak hours were from 6pm to 10pm and most taxi demands were on Friday and Saturday
- The maximum Avg. Speed was recorded to be 24 Km/hr with higher activity in the weekends
- XGBoost with Hyper Parameter Tuning turned out to be the best fit model to our dataset with ~3.7 minutes  error for a given coordinate
