Geobed
============

[![Build Status](https://drone.io/github.com/SocialHarvest/geobed/status.png)](https://drone.io/github.com/SocialHarvest/geobed/latest) [![Coverage Status](https://coveralls.io/repos/SocialHarvest/geobed/badge.png)](https://coveralls.io/r/SocialHarvest/geobed)

This Golang package contains an embedded geocoder. There are no major external dependendies other than some downloaded data files. Once downloaded, those data files 
are stored in memory. So after the initial load there truly are no outside dependencies. It geocodes and reverse geocodes to a city level detail. It approximates and takes 
educated guesses when not enough detail is provided. See test cases for examples.

### Why?

Honestly, have you seen what people want for geocoding? The pricing for API usage is insane. Yes, the data sets are hundreds of gigabytes. Yes, it takes a good bit of RAM 
to quickly query and return results. I think it's just too expensive to be frank. I also think that street level detail is sometimes going overboard. For starters, it's only 
a guess anyway. Second, street level detail is so rarely needed anyway. Fraud detection is one of the biggest use cases, but what if you just want to simply analyze a piece of 
text and grab any major cities from it to then plot (or whatever)? You might go to use an API, but you're getting details you'll never use. So it's wasteful.

APIs are slow too. Think about geocoding hundreds of thousands of location strings. Making those HTTP requests take a long time and batching doesn't even help that much. 

Wouldn't it be awesome to not need to make HTTP requests to an API? Wouldn't you be willing to give up the street level detail for that performance increase? For the cost savings? 

Then the "why" becomes quite clear and I'm glad you stumbled upon this package. It might be for you.

### How?

Simple. There are free data sets out there with cities (states and countries) and their lat/lng coordinates. Maxmind offers one as does Geonames. This data gets loaded into 
memory to be searched upon.

What about reverse geocoding? Well, that's much more difficult. The pro software is going to require even more data in the database, PostGIS is common, boundary data, etc. 
All this just to narrow down and make the query faster.

Our data set has significantly less points, so it should be faster...But also less accurate. Even still, we have an issue with querying two different columns with float values. 
The precision can lead to some long decimals too. How can we query that?! Enter geohash.

Have you heard of geohashing? http://en.wikipedia.org/wiki/Geohash

It takes those two fields (lat/lng) and converts them into one string. This makes it far easier to query. Now, we can simply do a substring comparison. On one field. 
Heck, even a regular expression could be used.

Then it's just a matter of finding the best matching hash and voila. You've reversed geocoded in a pretty quick fashion.

Yes. Yes a million times over this is not super accurate. It is, however, super fast and inexpensive. When you need fuzzy geocoding, this is a great solution.

### What Kinda Detail?

Well, you could geocode "New York, NY" for example or even just "New York". You could geocode some small city as well. 

You could reverse geocode for New York as well. However, it won't give you detail by the street level. It won't even give you detail by the district. It might simply return the center 
of the city (or close to).

Chances are that's good enough for you (if you're still reading this). Chances are you want to cluster your locations anyway. Chances are you're looking at a map with plotted points 
that's rather zoomed out. Or you're filling in a vector image state by state without a zoom. In these cases, you don't need anything other than the city.