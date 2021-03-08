---
title: 'Exporting data from Enphase APIs'
date: '2018-01-02T18:00:00+10:00'
permalink: /exporting-data-from-enphase-apis
author: 'James Elsey'
category:
    - 'Cloud'
tag:
    - api
    - data
    - enphase
    - iot
    - lambda
    - python

---
I bought an Enphase solar powered system in early 2017, one of the major appeals of the Enphase brand was that is has [developer APIs](https://developer.enphase.com/), so I could track my systems power generation and even household usage.

My aim is to get this data out of the Enphase APIs then try to make sense of it, possibly bringing in weather data and water usage from IoT systems on my ever increasing to-do list.

There doesn’t seem to be a way of bulk exporting data, and with [rate limiting on the free tier](https://developer.enphase.com/plans) I figured I could write a script that hits the stats API for each day, grabs a whole days data then persist it, wait a period of time, then hit the next day. By delaying, I don’t break the rate limit, I can just kick the script off and have a coffee!

```python
import pendulum
import time
import requests
import json
import os

userId = os.environ\['ENHPASE\_USER\_ID'\]  
key = os.environ\['ENPHASE\_KEY'\]  
systemId = os.environ\['ENPHASE\_SYSTEM\_ID'\]  
\# set tz code from http://www.localeplanet.com/java/en-AU/index.html  
tzCode = os.environ\['TIME\_ZONE'\]  
\# free tier only allows so many requests per minute, space them out with delays  
sleepBetweenRequests = int(os.environ\['SLEEP\_BETWEEN\_REQUESTS'\])  
\# Start/end dates to export  
startDate = pendulum.parse(os.environ\["START\_DATE"\], tzinfo=tzCode)  
endDate = pendulum.parse(os.environ\["END\_DATE"\], tzinfo=tzCode)  
\# Shouldn't need to modify this  
url = 'https://api.enphaseenergy.com/api/v2/systems/%s/stats' % systemId

print('Starting report between %s and %s' % (startDate.to\_date\_string(), endDate.to\_date\_string()))

period = pendulum.period(startDate, endDate)

for dt in period.range('days'):  
 time.sleep(sleepBetweenRequests)

 print('date \[%s\] START \[%s\] END \[%s\]' % (dt, dt.start\_of('day'), dt.end\_of('day')))  
 # HTTP Params  
 params = {'user\_id': userId,  
 'key': key,  
 'datetime\_format': 'iso8601',  
 'start\_at': dt.start\_of('day').int\_timestamp,  
 'end\_at': dt.end\_of('day').int\_timestamp}

 r = requests.get(url=url, params=params)

 if r.status\_code == 200:  
 filename = "out/%s.json" % dt.to\_date\_string()  
 os.makedirs(os.path.dirname(filename), exist\_ok=True)  
 with open(filename, 'w') as outfile:  
 json.dump(r.json(), outfile, indent=2)  
 print('Success %s' % dt.to\_date\_string())  
 else:  
 print('Failed to get data for %s' % dt.to\_date\_string())

```
Run that using `python stats.py` and it’ll dump out a json file in /out/ per day. I’ve only got the stats API setup so far, but feel free to pull request in the others!

Also keen an eye on my [github](https://github.com/jameselsey/python-enphase-export) project, I’ll be adding more to it, the plan is to get it running in AWS with scheduled lambdas to pull in data on an hourly basis, mash in data from weather APIs, and any IoT systems I build.