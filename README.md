# Scraping Concerts - Lab

## Introduction

Now that you've seen how to scrape a simple website, it's time to again practice those skills on a full-fledged site!

In this lab, you'll practice your scraping skills on an online music magazine and events website called Resident Advisor.

## Objectives

You will be able to:

* Create a full scraping pipeline that involves traversing over many pages of a website, dealing with errors and storing data

## View the Website

For this lab, you'll be scraping the https://ra.co website. For reproducibility we will use the [Internet Archive](https://archive.org/) Wayback Machine to retrieve a version of this page from March 2019.

Start by navigating to the events page [here](https://web.archive.org/web/20210325230938/https://ra.co/events/us/newyork?week=2019-03-30) in your browser. It should look something like this:

<img src="images/ra_top.png">

## Open the Inspect Element Feature

Next, open the inspect element feature from your web browser in order to preview the underlying HTML associated with the page.

## Write a Function to Scrape all of the Events on the Given Page

The function should return a Pandas DataFrame with columns for the `Event_Name`, `Venue`, and `Number_of_Attendees`.

Start by importing the relevant libraries, making a request to the relevant URL, and exploring the contents of the response with `BeautifulSoup`. Then fill in the `scrape_events` function with the relevant code.


```python
# Relevant imports
```


```python
EVENTS_PAGE_URL = "https://web.archive.org/web/20210326225933/https://ra.co/events/us/newyork?week=2019-03-30"

# Exploration: making the request and parsing the response

```


```python
# Find the container with event listings in it
# Some hints are giving along the way

# This page is organized somewhat unusually, and many of
# the CSS attributes seem auto-generated. We notice that
# there is a div with "events-all" in its attributes that
# looks promising if we use soup.find(), call this events_all_div

events_all_div = None

# The actual content is nested in a ul containing a single
# li within that div. Unclear why they are using a "list"
# concept for one element, but let's go ahead and select it
# Call this event_listings and use it to find ul and li in 
# events_all_div

event_listings = None


# Print out some chunks of the text inside to make sure we
# have everything we need in here
# For example print the events for March 30th and 31st

```


```python
# Find a list of events by date within that container

# Now we look at what is inside of that event_listings li tag.
# Based on looking at the HTML with developer tools, we see
# that there are 13 children of that tag, all divs. Each div
# is either a container of events on a given date, or empty

# Let's create a collection of those divs. recursive=False
# means we stop at 1 level below the event_listings li
dates = event_listings.findChildren(recursive=False)

# Now let's print out the start of the March 30th and March
# 31st sections again. This time each is in its own "date"
# container

# March 30th is at the 0 index
print("0 index:", dates[0].text[:200])
print()
# The 1 index is empty. We'll need to skip this later
print("1 index: ", dates[1].text)
print()
# March 31st is at the 2 index
print("2 index:", dates[2].text[:200])

# Now we know we can loop over all of the items in the dates
# list of divs to find the dates, although some will be blank
# so we'll need to skip them
```


```python
# Extract the date (e.g. Sat, 30 Mar) from one of those containers
# Call this first_date

# Grabbing just one to practice on
first_date = None

# This div contains a div with the date, followed by several uls
# containing actual event information

# The div with the date happens to have another human-readable
# CSS class, so let's use that to select it then grab its text
# Call this date, and use class_=sticky header as an argument for
# first_date.find
date = None

# There is a / thing used for aesthetic reasons; let's remove it
date = None
```


```python
# Extract the name, venue, and number of attendees from one of the
# events within that container

# As noted previously, the div with information about events on
# this date contains several ul tags, each with information about
# a specific event. Get a list of them.
# (Again this is an odd use of HTML, to have an unordered list
# containing a single list item. But we scrape what we find!)
first_date_events = None

# Grabbing the first event ul to practice on
first_event = None

# Each event ul contains a single h3 with the event name, easy enough
name = None

# First, get all 1-3 divs that match this description,
# where first_event.findAll has attrs={"height": 30}
# as one of its arguments
venue_and_attendees = None
# The venue is the 0th (left-most) div, get its text
venue = None
# The number of attendees is the last div (although it's sometimes
# missing), get its text
num_attendees = None
```


```python
# Run the code below
# Make sure you understand it since it will
# for the basis of the definition of scrape_events below

# Create an empty list to hold results
rows = []

# Loop over all date containers on the page
for date_container in dates:
    
    # First check if this is one of the empty divs. If it is,
    # skip ahead to the next one
    if not date_container.text:
        continue
    
    # Same logic as above to extract the date
    date = date_container.find("div", class_="sticky-header").text
    date = date.strip("'̸")
    
    # This time, loop over all of the events
    events = date_container.findChildren("ul")
    for event in events:
        
        # Same logic as above to extract the name, venue, attendees
        name = event.find("h3").text
        venue_and_attendees = event.findAll("div", attrs={"height": 30})
        venue = venue_and_attendees[0].text
        try:
            num_attendees = int(venue_and_attendees[-1].text)
        except ValueError:
            num_attendees = np.nan
            
        # New piece here: appending the new information to rows list
        rows.append([name, venue, date, num_attendees])

# Make the list of lists into a dataframe and display
df = pd.DataFrame(rows)
df  
```


```python
# Bring it all together in a function that makes the request, gets the
# list of entries from the response, loops over that list to extract the
# name, venue, date, and number of attendees for each event, and returns
# that list of events as a dataframe

def scrape_events(events_page_url):
    #Your code here
    df.columns = ["Event_Name", "Venue", "Event_Date", "Number_of_Attendees"]
    return df
```


```python
# Test out your function
scrape_events(EVENTS_PAGE_URL)
```

## Write a Function to Retrieve the URL for the Next Page

As you scroll down, there should be a button labeled "Next Week" that will take you to the next page of events. Write code to find that button and extract the URL from it.

This is a relative path, so make sure you add `https://web.archive.org` to the front to get the URL.

![next page](images/ra_next.png)


```python
# Find the button, find the relative path, create the URL for the current `soup`

# This is tricky again, since there are not a lot of
# human-readable CSS classes

# One unique thing we notice is a > icon on the part where
# you click to go to the next page. It's an SVG with an 
# aria-label of "Right arrow", this soup.find() will have
# attrs={"aria-label": "Right arrow"} as an argument

avg = None

# That SVG is inside of a div
svg_parent = None

# And the tag right before that div (its "previous sibling")
# is an anchor (link) tag with the path we need
link = None

# Then we can extract the path from that link to build the full URL
relative_path = None
next_page_url = None
next_page_url
```


```python
# Fill in this function, to take in the current page's URL and return the
# next page's URL
def next_page(url):
    #Your code here
    return next_page_url
```


```python
# Test out your function
next_page(EVENTS_PAGE_URL)
```

## Scrape the Next 500 Events

In other words, repeatedly call `scrape_events` and `next_page` until you have assembled a dataframe with at least 500 rows.

Display the data sorted by the number of attendees, greatest to least.

We recommend adding a brief `time.sleep` call between `requests.get` calls to avoid rate limiting.


```python
# Your code here

# Make a dataframe to store results. We will concatenate
# additional dfs as they are returned
overall_df = pd.DataFrame()

current_url = EVENTS_PAGE_URL

# Now define a while look on overall_df
```


```python
# Display overall_df the specified sorted order
# Do so by Number of Attendees in descending order
```

## Summary 

Congratulations! In this lab, you successfully developed a pipeline to scrape a website for concert event information!
