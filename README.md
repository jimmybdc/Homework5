#Dependencies
import matplotlib.pyplot as plt
import numpy as np
import os
import pandas as pd

#Import files
citydata_csv = os.path.join('raw_data', 'city_data.csv')
ridedata_csv = os.path.join('raw_data', 'ride_data.csv')

#Create dataframes
citydata_df = pd.read_csv(citydata_csv)
ridedata_df = pd.read_csv(ridedata_csv)

#View citydata dataframe
citydata_df.head()

#View ridedata dataframe
ridedata_df.head()

# Count number of rides per city
rides = ridedata_df["city"].value_counts()
rides.head()

# Creating a summary DataFrame using above values
rides_per_city_df = rides.rename_axis('city').reset_index(name='rides per city')

rides_per_city_df.head()

# Create the GroupBy object based on the "City" column
city_group = ridedata_df.groupby(["city"], as_index=False)

# Calculate average fare 
average_fare = city_group.mean()
#average_fare.df = average_fare.rename_axis('average fare').reset_index(name='rides per city')
average_fare_df = pd.DataFrame(average_fare)
del average_fare_df['ride_id']
average_fare_df.rename(columns={'fare': 'average fare'}, inplace=True)
average_fare_df.head()

# Merge the two DataFrames together based on the city
mergecityandride_df = pd.merge(citydata_df, ridedata_df, on="city")
mergecityandride_df.head()

# Merge the two DataFrames together based on the city
mergeaveragefare_df = pd.merge(mergecityandride_df, average_fare_df, on="city", how="outer")
mergeaveragefare_df.head()

# Merge the two DataFrames together based on the Dates they share
cityandride_df = pd.merge(mergeaveragefare_df, rides_per_city_df, on="city", how="outer")
cityandride_df = cityandride_df.drop_duplicates("city")
cityandride_df.head()

#Bubble Plot "Pyber Ride Sharing Data (2016)"
driver_count = cityandride_df["driver_count"]
plt.scatter(cityandride_df[cityandride_df['type'] == 'Urban']['rides per city'], cityandride_df[cityandride_df['type'] == 'Urban']['average fare'], c='pink', s=driver_count, alpha=0.9, linewidth=.5, label='Urban')
plt.scatter(cityandride_df[cityandride_df['type'] == 'Suburban']['rides per city'], cityandride_df[cityandride_df['type'] == 'Suburban']['average fare'], c='blue', s=driver_count, alpha=0.9, linewidth=.5, label='Suburban')
plt.scatter(cityandride_df[cityandride_df['type'] == 'Rural']['rides per city'], cityandride_df[cityandride_df['type'] == 'Rural']['average fare'], c='gold', s=driver_count, alpha=0.9, linewidth=.5,label='Rural')
plt.title("Pyber Ride Sharing Data (2016)")
plt.xlabel('Total Number of Rides Per City')
plt.ylabel('Average Fare($) Per City')
plt.legend(title="City Type")
plt.text(45, 30, "Note: Circle size corresponds with driver count per city.", rotation='horizontal')
plt.show()

# Pie Chart % of Total Fares by City Type
# Labels for the sections of our pie chart
labels = ["Suburban", "Urban", "Rural"]

# The values of each section of the pie chart
sizes = [cityandride_df.loc[cityandride_df.type == "Suburban"]["fare"].sum(), cityandride_df.loc[cityandride_df.type == "Urban"]["fare"].sum(), cityandride_df.loc[cityandride_df.type == "Rural"]["fare"].sum()]

# The colors of each section of the pie chart
colors = ["blue", "pink", "gold"]

# Tells matplotlib to seperate the "Python" section from the others
explode = (0.05, 0.05, 0.05)

# Creates the pie chart based upon the values above
# Automatically finds the percentages of each part of the pie chart
plt.title('% of Total Fares by City Type')
plt.pie(sizes, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=140)

# Tells matplotlib that we want a pie chart with equal axes
plt.axis("equal")

# Prints our pie chart to the screen
plt.show()

# Pie Chart % of Total Rides by City Type
# The values of each section of the pie chart
sizes2 = [len(cityandride_df.loc[cityandride_df.type == "Suburban"]["ride_id"]), len(cityandride_df.loc[cityandride_df.type == "Urban"]["ride_id"]), len(cityandride_df.loc[cityandride_df.type == "Rural"]["ride_id"])]

# Creates the pie chart based upon the values above
# Automatically finds the percentages of each part of the pie chart
plt.title('% of Total Rides by City Type')
plt.pie(sizes2, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=140)

# Tells matplotlib that we want a pie chart with equal axes
plt.axis("equal")

# Prints our pie chart to the screen
plt.show()

# Pie Chart % of Total Drivers by City Type
# The values of each section of the pie chart
sizes3 = [cityandride_df.loc[cityandride_df.type == "Suburban"]["driver_count"].sum(), cityandride_df.loc[cityandride_df.type == "Urban"]["driver_count"].sum(), cityandride_df.loc[cityandride_df.type == "Rural"]["driver_count"].sum()]

# Creates the pie chart based upon the values above
# Automatically finds the percentages of each part of the pie chart
plt.title('% of Total Drivers by City Type')
plt.pie(sizes3, explode=explode, labels=labels, colors=colors,
        autopct="%1.1f%%", shadow=True, startangle=140)
# Tells matplotlib that we want a pie chart with equal axes
plt.axis("equal")

# Prints our pie chart to the screen
plt.show()