# Weather API

In order to support free weather apis (Such as th one provided by the Norweigan Meterological Institute), this project acts as a central hub for software to make weather api requests. Instead of the tousands of devices which use your software to get data from the NMI using up their bandwidth, they can query your own infrastructure and reliive some pressure of off freely provided services

In order to reduce the amount of requests going to the upstream service, this tool caches results for a location and only fetches new data after a reasonable amount of time has past (dictated by the provider)

## APIs Supported

I plan to support the National Weather Service, the Norweigan Meterological Institute

## Technical Map

The server keeps a database of current weather data, and updates it every minute

Locations are seperated by latitudes and longitudes

In the future, if the server can't keep up with requests and loading from the database, maybe we could split it up by continent or lat-long grid, so then it doesn't have to search a table of all of the locations in the world

### Defeating Spam

In order to mitigate spam or DOS attacks, there has to be a way to check a request to verify that it's from a legitimate source

It might be possible to take an approach similar to Tor (https://blog.torproject.org/introducing-proof-of-work-defense-for-onion-services/), in which the client solves a small puzzle (https://en.wikipedia.org/wiki/Proof_of_work) before getting a response. It would happen in the background, and not be so hard that the user detects a delay in the response. In theory, it would be hardly noticible to real users, but would make it less efficient to use a botnet to launch an attack. It would also be possible and more effective to make the puzzles harder as the server is put under more strain/the server detects more requests than usual (as if from an attack)

### The database
The locations table has a list of the world's latitude and longitude points

database.db/
├─ server_metadata
│
├─ cities_lat_long (maps cities to lat-long)
│
├─ location1/
│  ├─ last_updated
│  ├─ data
│
├─ location2/
│  ├─ last_updated
│  ├─ data


### Process
0. The client sends the api call
1. The server recieves it. It checks if the location being requested has been updated within one minute, if it has, it returns the cached data. If not, it fetches new data.
2. If there is new data, it writes it in the database and updates the last checked metadata

## Provider Customization

The provider must choose how long before location data is refreshed and choose an email to submit to the NMI in the user agent