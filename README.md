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

```


```python
# Find a list of events by date within that container

```


```python
# Extract the date (e.g. Sat, 30 Mar) from one of those containers

```


```python
# Extract the name, venue, and number of attendees from one of the
# events within that container

```


```python
# Loop over all of the event entries, extract this information
# from each, and assemble a dataframe

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
```

## Summary 

Congratulations! In this lab, you successfully developed a pipeline to scrape a website for concert event information!
