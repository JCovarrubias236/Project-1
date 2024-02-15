# Project-1
Music Trends Through the Generations
Music has been around in various forms for many many years, with some forms even dating back to over 35,000 years ago. Throughout time, music has been used within religions, to celebrate, to share messages, to inspire, to express oneself, and to generally entertain. Across the decades, how music is created, shared, consumed, and utilized has evolved rapidly and innovatively. 
Since the start of the new millenia, music consumption has been digitized and consumers are now more likely to consume music via streaming than in previous generations’ where consumption was via physical or analog mediums. 

Depending on what (recent) generation you may have been born in, the consumption, listening habits, and popularity of genres or music types may differ. Our team was interested in analyzing music trends of the past to determine if its possible to predict the trends of the next generation to come. 

Objectives
1. How has the number of tracks (songs) changed over time, specifically across each generation.
2. Understand how have features of tracks evolved over time? What features seem to be the most common in each listening generation.
3. Determine how genres have evolved and if there are consistent genres that stand the test of time.


### The Kaggle Dataset Set Up and Cleaning
Dataset was created in October of 2023 and it has 114,000 rows and 21 columns of data. To ready the dataset for further processing, ensuring compatibility or refining the data for use in API queries, we did next:
1. Removing Duplicates: The code first removes duplicate rows based on the combination of the "album_name" and "track_name" columns, then resets the index.
2. Deleting Unnecessary Columns: Several columns are deleted from the DataFrame. These columns include "Unnamed: 0", "key", "time_signature", "mode", "popularity", and "index".
3. Adding Empty Columns: Three new columns named "release_date", "#_of_tracks_in_album", and "track_popularity" are added to the DataFrame, initially filled with empty strings.
4. Filtering Rows: Rows containing album names or track names with foreign letters are removed from the DataFrame. The removal of rows with non-English characters suggests an attempt to standardize the data or avoid potential issues with encoding or language discrepancies during subsequent operations.

#Reducing any duplicate album and track names
spotify_df = spotify_df.drop_duplicates(subset=["album_name", "track_name"]).reset_index()
#Deleting column that was from the kaggle dataset
del spotify_df["Unnamed: 0"]
del spotify_df["key"]
del spotify_df["time_signature"]
del spotify_df["mode"]
del spotify_df["popularity"]
del spotify_df["index"]
# Add empty column called release date and # of tracks in album
spotify_df["release_date"] = ''
spotify_df["#_of_tracks_in_album"] = ''
spotify_df["track_popularity"] = ''
# Deleting rows with album names or track names with foreign letters to get as much data within the API key time limit.
mask = spotify_df[["album_name","track_name"]].apply(lambda x: x.str.contains(r"[^\x00-\xFF]+").any(), axis = 1)
spotify_df = spotify_df[~mask]

After cleaning the dataset we check below information and take an action if needed
# Check if any column has null values
spotify_df.isnull().any()
# Delete the rows with null values
spotify_df = spotify_df.dropna(how='any')
# Checking for any blank values
spotify_df.eq("").any()
# No action taken since we will be filling most values in the last three columns with the API call.


### Running Spotify API to pull the data for `release_date`, `track_popularity`, and `#_of_tracks_in_album`
To fully utilize the functionalities of this project, we need to obtain API keys from Spotify.  
Spotify’s API has limitations on the amount of data that you use. 
- 1 hour time limits on the API key
- 30 second window call limit intervals
Because there is some limitations, you need to creat access token each time as you want to call Spotify API's. Here's a step-by-step guide to help you through the process:
1. Log into the dashboard using your Spotify account.
2. Create an app and select "Web API" for the question asking which APIs are you planning to use. Once you have created your app, you will have access to the app credentials (client_id & client_secret). These will be required for API authorization to obtain an access token.
    curl -X POST "https://accounts.spotify.com/api/token" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "grant_type=client_credentials&client_id="your_client_id"&client_secret="your_client_secret""
3. Use the access token in your API requests.

# Import requests library
import requests
import json
import pandas as pd
import time

# Define base URL and headers
base_url = "https://api.spotify.com/v1/tracks"
headers = {"Authorization": "Bearer  BQBXcGB2Gv6NfOzKWFr0z9POVS-Dxptsg9eISYU1pYOOJVnNjEsj6zrzpXO_73_Kq9nNzS9DfgHLDK-1fuUwcW5NMvNlFuO3YOoRwjAnY1cy0VNDHfA"}

# Define the batch size (how many IDs per request)
batch_size = 50

# Initialize an empty list to store the results
results = []
counter = 0
# Loop over the rows of the spotify_df dataframe starting from the last index
for index, row in spotify_df.iterrows():
    if counter >= 1000:
        counter = 0
        time.sleep(28)
    else:
        counter+=1
    # Append the track ID to the results list
        results.append(row["track_id"])
        # Check if the results list has reached the batch size or the end of the dataframe
        if len(results) == batch_size or index == len(spotify_df) - 1:
            # Join the IDs with a comma as a query parameter
            query = ",".join(results)
            # Make a request with the query and headers
            response = requests.get(base_url, params={"ids": query}, headers=headers)
            # Get the JSON data from the response
            data = response.json()
            #print(json.dumps(data,indent=4))
            # Get the tracks from the data
            tracks = data["tracks"]
            # Loop over the tracks and update the dataframe with the information
            try:
                for track in tracks:
                    # Find the row index that matches the track ID
                    row_index = spotify_df[spotify_df["track_id"] == track["id"]].index[0]
                    # Update the release date, track popularity, and number of tracks in album columns
                    spotify_df.loc[row_index, "release_date"] = track["album"]["release_date"]
                    spotify_df.loc[row_index, "track_popularity"] = track["popularity"]
                    spotify_df.loc[row_index, "#_of_tracks_in_album"] = track["album"]["total_tracks"]
                    print(f"The track {spotify_df.loc[row_index, 'track_name']} release date is {spotify_df.loc[row_index, 'release_date']}. Popularity: {spotify_df.loc[row_index, 'track_popularity']}. # of tracks: {spotify_df.loc[row_index,'#_of_tracks_in_album']}.")
            except: 
                print("Theres no data or an error")
            # Clear the results list for the next batch
            results.clear()   

After calling the API, we removed duplicate albums and song names, includes any cover songs or live/extended/remastered versions removed album or song names with any foreign letters and languages
# How many rows are blank? We reached the time limit for the API call key and will have to drop the rows with blank values. 
print(spotify_df.eq("").any())
print(f"There are {spotify_df['release_date'].eq('').sum()} blank rows.")
# Replace blank values with np.nan
spotify_df = spotify_df.replace("", np.nan)
# Drop the rows with null values
spotify_df = spotify_df.dropna(how="any")
print(spotify_df.eq("").any())

# Save the spotify_df dataframe to a csv file so we can access data later without running the API again
spotify_df.to_csv('../Resources/spotify_clean_data.csv', encoding='utf-8', index=False)

# Creating another column called release year in order to analyze by year
spotify_clean_df["release_year"] = spotify_clean_df["release_date"].str[:4]
spotify_clean_df["release_year"] = pd.to_numeric(spotify_clean_df["release_year"])

# Splitting the artists column to name the main artist and the featured artists in different columns
split_artists = spotify_clean_df["artists"].str.split(";")
spotify_clean_df["artist"] = split_artists.str.get(0)
spotify_clean_df
