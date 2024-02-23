# Weather influence on music

This is a concept study, that works out what music preferences an individual likes to listen to, stimulated to their surrounding weather condition. 

<img src="./analytics/example.png" alt="Example output" style="width:40%;"/>

## Table of contents

- [Weather influence on music](#weather-influence-on-music)
    - [Concept](#concept)
    - [State of implementation](#state-of-implementation)
    - [Application](#application)
        - [Initial setup](#initial-setup)
        - [User Interface](#user-interface)
        - [SurveyHandling](#surveyhandling)
        - [SongAttributes](#songattributes)
        - [WeatherRequests](#weatherrequests)
        - [RelationAnalytics](#relationanalytics)

## Concept

Have a look at the [project description](PROJECT.md) for further details.

## State of implementation

The code is in a stage of *proof of concept*. Meaning:

- essential unit- or integration-testing has not been carried out
- only a limited amount of data has been utilized for evaluation. 
- the application has been excuted under various conditions, though it cannot be guaranteed bugs or crashes won't occur.
- an holistic architecture

It has been successfully executed on Windows11 and Debian12

## Application

There are mainly two parts:
- one for data aquisition, training and general preparation. 
- and the other for with the user interface

In the user interface a user can 

1. type in his geolocation.
2. receive meterological data for that region, even manipulate that data.
3. receive suggestions what kind of music would be appropriate.
4. get some matching examples from the dataset. 

**Note**: with the provided data in this repo one can already start the user interface with all features. No preparations like training are necessary for this. If you would like to run the steps on your own, you need to provide Spotify API 
credentials, as the ones in the code have been deactivated.

Below is an overview of the individual parts and how to interact with them

### Initial setup

After the repo has been cloned locally, the required python packages need to be installed that are summarized in the `packages.txt`. 

**Note**: it is recommended to work with a particular virtual environment on that matter.

```
python3 -m pip install -r packages.txt
```

### User Interface

The user inteface holds the interaction with the song estimators. To execute it, one has to run

```
python3 web_interface.py
```

and follow the instructions in the terminal.

Requires:
- song database (*full_data.p*)
- regressors  (*clfs.p*)

### SurveyHandling

This part processes and extracts relevant data from the *Million Musical Tweets Dataset*: what user listened where and when to which track.

```
python3 pipelines\SurveyHandling.py
```
No input data needed.

Exports:
- listening statistics (*survey_data.p*)
- statistics and visualizations (*analytics/*)

### SongAttributes

Here the songs are looked up in Spotify's database for their individual features. It showed that the API isn't always working stable so running it a multiple times might be necessary, but the scripts are prepared to handle these situations.

```
python3 pipelines\SongAttributes.py
```
Requires:
- listening statistics (*survey_data.p*)

Exports:
- intermediate states to continue on an intended or unintended early exit (*song_ids.csv*)
- song features (*song_attribute_data.p*)

### WeatherRequests

Gathers the meterological data to the songs under investigation. Since it might be that Spotify can't look up a song, both *SurveyHandling* and *SongAttributes* need to be run beforehands.

```
python3 pipelines\WeatherRequests.py
```
Requires:
- song features (*song_attribute_data.p*)
- listening statistics (*survey_data.p*)

Exports:
- weather information (*weather_data.p*)

### RelationAnalytics

As a final step, this is where all the data gets combined. Here for each music attribute of a song an individual regressor is trained, on basis of the related weather conditions.

```
python3 pipelines\RelationAnalytics.py
```
Requires
- weather information (*weather_data.p*)
- listening statistics (*survey_data.p*)
- song features (*song_attribute_data.p*)

Exports:
- a set of regressors (*clfs.p*)