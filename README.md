
# Scraping Concerts - Lab

## Introduction

Now that you've seen how to scrape a simple website, it's time to again practice those skills on a full-fledged site!
In this lab, you'll practice your scraping skills on a music website: https://www.residentadvisor.net.
## Objectives

You will be able to:
* Create a full scraping pipeline that involves traversing over many pages of a website, dealing with errors and storing data

## View the Website

For this lab, you'll be scraping the https://www.residentadvisor.net website. Start by navigating to the events page [here](https://www.residentadvisor.net/events) in your browser.

<img src="images/ra.png">


```python
# Load the https://www.residentadvisor.net/events page in your browser.
```

## Open the Inspect Element Feature

Next, open the inspect element feature from your web browser in order to preview the underlying HTML associated with the page.


```python
# Open the inspect element feature in your browser
```

## Write a Function to Scrape all of the Events on the Given Page Events Page

The function should return a Pandas DataFrame with columns for the Event_Name, Venue, Event_Date and Number_of_Attendees.


```python
import re
import numpy as np
import pandas as pd
import requests
from bs4 import BeautifulSoup
import time
```


```python
#Exploration; designing/testing function parts
response = requests.get("https://www.residentadvisor.net/events/us/newyork")
soup = BeautifulSoup(response.content, 'html.parser')
```


```python
event_listings = soup.find('div', id="event-listing")
```


```python
entries = event_listings.findAll('li')
print(len(entries), entries[0])
```

    53 <li><p class="eventDate date"><a href="/events.aspx?ai=8&amp;v=day&amp;mn=4&amp;yr=2019&amp;dy=18"><span>Thu, 18 Apr 2019 /</span></a></p></li>



```python
#Successive exploration in function development
rows = []
for entry in entries:
    #Is it a date? If so, set current date.
    date = entry.find('p', class_="eventDate date")
    event = entry.find('h1', class_="event-title")
    if event:
        details = event.text.split(' at ')
        event_name = details[0].strip()
        venue = details[1].strip()
        try:
            n_attendees = int(re.match("(\d*)", entry.find('p', class_="attending").text)[0])
        except:
            n_attendees = np.nan
        rows.append([event_name, venue, cur_date, n_attendees])
    elif date:
        cur_date = date.text
    else:
        continue
df = pd.DataFrame(rows)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>New York Trax 3rd Anniversary with Luke Slater...</td>
      <td>Good Room</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Secret Solstice Pre-Party with Metro Area, Ale...</td>
      <td>Kings Hall - Avant Gardner</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Planetarium: Hypnotic Spa, Earthen Sea (Live) ...</td>
      <td>Nowadays</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AceMo, Moma Ready and X-Coast</td>
      <td>Elsewhere</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bonobo (DJ Set)</td>
      <td>Elsewhere</td>
      <td>Fri, 19 Apr 2019 /</td>
      <td>94.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Final function
def scrape_events(events_page_url):
    #Your code here
    response = requests.get(events_page_url)
    soup = BeautifulSoup(response.content, 'html.parser')
    entries = event_listings.findAll('li')
    rows = []
    for entry in entries:
        #Is it a date? If so, set current date.
        date = entry.find('p', class_="eventDate date")
        event = entry.find('h1', class_="event-title")
        if event:
            details = event.text.split(' at ')
            event_name = details[0].strip()
            venue = details[1].strip()
            try:
                n_attendees = int(re.match("(\d*)", entry.find('p', class_="attending").text)[0])
            except:
                n_attendees = np.nan
            rows.append([event_name, venue, cur_date, n_attendees])
        elif date:
            cur_date = date.text
        else:
            continue
    df = pd.DataFrame(rows)
    df.head()
    df.columns = ["Event_Name", "Venue", "Event_Date", "Number_of_Attendees"]
    return df
```

## Write a Function to Retrieve the URL for the Next Page


```python
#Function development cell
soup.find('a', attrs={'ga-event-action':"Next "}).attrs['href']
```




    '/events/us/newyork/week/2019-04-25'




```python
def next_page(url):
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')
    url_ext = soup.find('a', attrs={'ga-event-action':"Next "}).attrs['href']
    next_page_url = "https://www.residentadvisor.net" + url_ext
    #Your code here
    return next_page_url
```

## Scrape the Next 1000 Events for Your Area

Display the data sorted by the number of attendees. If there is a tie for the number attending, sort by event date.


```python
#Your code here
dfs = []
total_rows = 0
cur_url = "https://www.residentadvisor.net/events/us/newyork"
while total_rows <= 1000:
    df = scrape_events(cur_url)
    dfs.append(df)
    total_rows += len(df)
    cur_url = next_page(cur_url)
    time.sleep(.2)
df = pd.concat(dfs)
df = df.iloc[:1000]
print(len(df))
df.head()
```

    1000





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Event_Name</th>
      <th>Venue</th>
      <th>Event_Date</th>
      <th>Number_of_Attendees</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>New York Trax 3rd Anniversary with Luke Slater...</td>
      <td>Good Room</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Secret Solstice Pre-Party with Metro Area, Ale...</td>
      <td>Kings Hall - Avant Gardner</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Planetarium: Hypnotic Spa, Earthen Sea (Live) ...</td>
      <td>Nowadays</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>AceMo, Moma Ready and X-Coast</td>
      <td>Elsewhere</td>
      <td>Thu, 18 Apr 2019 /</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Bonobo (DJ Set)</td>
      <td>Elsewhere</td>
      <td>Fri, 19 Apr 2019 /</td>
      <td>94.0</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

## Summary 

Congratulations! In this lab, you successfully developed a pipeline to scrape a website for concert event information!
