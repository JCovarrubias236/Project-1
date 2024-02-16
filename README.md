# Project-1
## Spotify Data Analysis Through the Generations

Music has been around in various forms for many many years, with some forms even dating back to over 35,000 years ago. Throughout time, music has been used within religions, to celebrate, to share messages, to inspire, to express oneself, and to generally entertain. Across the decades, how music is created, shared, consumed, and utilized has evolved rapidly and innovatively. 
Since the start of the new millenia, music consumption has been digitized and consumers are now more likely to consume music via streaming than in previous generations’ where consumption was via physical or analog mediums. 

Depending on what (recent) generation you may have been born in, the consumption, listening habits, and popularity of genres or music types may differ. Our team was interested in analyzing music trends of the past to determine if its possible to predict the trends of the next generation to come. 

Spotify is a streaming platform established in 2006, and the popularity is calculated by Spotify's algorithm based mostly on the total number of plays the track has had and how recent those plays are. Songs that are being played a lot now will have a higher popularity than songs that were played in the past.
This analysis is good for understanding market preferences nowadays and can be used for marketing and content creation strategies rather than historical music industry analysis.

Objectives:
1. How has the number of tracks (songs) changed over time, specifically across each generation.
2. Understand how have features of tracks evolved over time? What features seem to be the most common in each listening generation.
3. Determine how genres have evolved and if there are consistent genres that stand the test of time.

This repository contains code for analyzing Spotify data retrieved from the Spotify API. The code conducts various data cleaning, manipulation, and exploratory analysis tasks on the dataset.

### Cleaning the Kaggle dataset

The code begins by cleaning the Kaggle dataset, which involves:
- Loading the dataset from a CSV file.
- Reducing duplicate album and track names.
- Deleting unnecessary columns from the dataset.
- Adding empty columns for release date, number of tracks in the album, and track popularity.
- Removing rows with album or track names containing foreign letters.
- Handling null and blank values.

### Running Spotify API to pull additional data

The code then utilizes the Spotify API to retrieve additional data for `release_date`, `track_popularity`, and `#_of_tracks_in_album`. This involves making API requests for each track in the dataset, updating the dataset with the retrieved information.
Spotify’s API has limitations on the amount of data that you use. 
- 1 hour time limits on the API key
- 30 second window call limit intervals
Because there is some limitations, you need to creat access token each time as you want to call Spotify API's. Here's a step-by-step guide to help you through the process:
1. Log into the dashboard using your Spotify account.
2. Create an app and select "Web API" for the question asking which APIs are you planning to use. Once you have created your app, you will have access to the app credentials (client_id & client_secret). These will be required for API authorization to obtain an access token.
   
    curl -X POST "https://accounts.spotify.com/api/token" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    -d "grant_type=client_credentials&client_id="your_client_id"&client_secret="your_client_secret""
4. Use the access token in your API requests.

### Data Cleaning and Dataframe Manipulation

Further data cleaning and manipulation are performed, including:
- Handling blank rows.
- Saving the cleaned dataset to a CSV file for future use.
- Analyzing the dataset by creating additional columns for `release_year` and splitting the `artists` column.

### Dataset Exploratory Analysis

The code conducts exploratory analysis on the dataset, including:
- Analyzing track generation trends.
- Analyzing track genrsand artists popularity across generations.
- Analyzing the distribution of music genres across generations.
- Identifying top songs and exploring their attributes.
- Visualizing correlations between track features in each generation and popularity.
- Visualizing correlations between track attributes and popularity.

### Dependencies

The code relies on the following Python libraries:
- `requests`
- `json`
- `pandas`
- `numpy`
- `datetime`
- `matplotlib`
- `scipy`
- `seaborn`

Ensure that these libraries are installed in your Python environment to run the code successfully.

### Usage

1. Clone this repository to your local machine.
2. Ensure the dependencies are installed.
3. Run the Python scripts in a compatible environment to analyze the Spotify dataset.

### Contributions

Contributions to this repository are welcome. If you find any issues or have suggestions for improvements, please feel free to open an issue or submit a pull request.


