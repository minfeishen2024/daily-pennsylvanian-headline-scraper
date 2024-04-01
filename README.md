## Changes Made to Scraper

Original ```scrape_data_point()``` code

```python
def scrape_data_point():
    """
    Scrapes the main headline from The Daily Pennsylvanian home page.

    Returns:
        str: The headline text if found, otherwise an empty string.
    """
    req = requests.get("https://www.thedp.com")
    loguru.logger.info(f"Request URL: {req.url}")
    loguru.logger.info(f"Request status code: {req.status_code}")

    if req.ok:
        soup = bs4.BeautifulSoup(req.text, "html.parser")
        target_element = soup.find("a", class_="frontpage-link")
        data_point = "" if target_element is None else target_element.text
        loguru.logger.info(f"Data point: {data_point}")
        return data_point
```

Modified ```scrape_data_point()``` code

```python
def scrape_data_point():
    """
    Scrapes the title of the newest podcast.

    Returns:
        str: The headline text if found, otherwise an empty string.
    """
    req = requests.get("https://www.thedp.com/section/podcasts")
    loguru.logger.info(f"Request URL: {req.url}")
    loguru.logger.info(f"Request status code: {req.status_code}")

    if req.ok:
        soup = bs4.BeautifulSoup(req.text, "html.parser")
        #find all h3 element with class of standard link
        h3_element = soup.find_all("h3", class_="standard-link")

        #extract the first h3 element
        if h3_element:
            target = h3_element[0].find("a")
            if target:
                data_point = "" if target is None else target.text
        
        loguru.logger.info(f"Data point : {data_point}")
        return data_point

```


## Modification Explanation
In order to obtain the newest podcast episode from the DP, I first modified the link in the request.get() function to get to the podcast pages. After inspecting the page, I found that the title for each of the podcast episode is contained within <h3> element with class of "standard-link". Within the h3 element, the title of the podcast itself is contained within <a href> element. Therefore, I modified the scraper to first find all h3 element with a corresponding class of standard-link, as reflected in h3_element = soup.find_all("h3", class_="standard-link") line. To extract the newest title, I then asked the scraper to access the first h3_element it found and extract the text from the "a" element contained within that h3 element :
```python
        h3_element = soup.find_all("h3", class_="standard-link")

        #extract the first h3 element
        if h3_element:
            target = h3_element[0].find("a")
            if target:
                data_point = "" if target is None else target.text
```
The scraper now returns the newest podcast episode from the DP.
