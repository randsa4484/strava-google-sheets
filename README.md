# strava-google-sheets
This project allows you creating nice statistics graphs for your [Strava club](https://www.strava.com) with [Google Sheets](https://www.google.com/sheets/about/).

Strava is an amazing platform for tracking sport activities. Runners, bikers are using it to measure own progress, save routes and compete. The athletes can join clubs. The clubs are organized by interest or location. Some clubs are tied to a company where club members work. The club is a great way of building up a community. However, clubs have several problems.

- There is no way to see the results of the club members. There are weekly dashboards. It is not possible to view the weekly history. Also, there is a list of all activities in the club.
- It is not possible to create a challenge in the club. For example, a monthly challenge for achieving kilometers, gaining elevation meters or frequency of the training.

These issues have been raised several times by the community, so there might be changes in the future.

## Easy solution

An easy and obvious solution is to build nice dashboards using [Strava API](https://developers.strava.com). Before going into any visualization, I looked into API to [get club activities](https://developers.strava.com/docs/reference/#api-Clubs-getClubActivitiesById). The API returns an array of activities (the example is taken from the official Strava documentation).

```json
[ {
  "resource_state" : 2,
  "athlete" : {
    "resource_state" : 2,
    "firstname" : "Peter",
    "lastname" : "S."
  },
  "name" : "World Championship",
  "distance" : 2641.7,
  "moving_time" : 577,
  "elapsed_time" : 635,
  "total_elevation_gain" : 8.8,
  "type" : "Ride",
  "workout_type" : null
} ]
```

The data is not very helpful. It doesn't contain the date/time of the activity. The athlete complete names are also hidden which might be an obstacle for some purposes.

## Working solution

All right, the solution is to build a web-scrapper that would get all the necessary data from the web-page. The [web-scrapper](lambda_function.py) is implemented in python. And it is executed as an AWS Lambda function.

The data visualization can be done using Google Sheets. The data is written to the Google Sheet by a Google Apps Script. The [script](strava-fetch.gs) calls HTTPS endpoint on AWS and adds the data to the Google Sheet. The script is triggered by a button placed on a sheet.

The Google sheet has several sheets to organize the data:

- ``raw data``: this sheet contains data obtained from Strava

- ``stats for month``: this sheet is created using a pivot table for athletes vs kilometers and elevation gain meter

- ``stats using calculcations``: these sheets are created using calculations

- ``graphs``: graphs showing the results of athlete progress

### AWS Lambda setup

For the AWS Lambda setup, it is required to create a layer containing [lxml](https://lxml.de) library using the ideas from the following resources: [first](https://stackoverflow.com/questions/56818579/unable-to-import-lxml-etree-on-aws-lambda) and [second](https://gist.github.com/allen-munsch/ad8faf9c04b72aa8d0808fa8953bc639).
