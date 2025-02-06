# Data_Scraping_Project
# Book Scraping Project

This project scrapes book data from [Books to Scrape](https://books.toscrape.com/), a dummy website designed for web scraping practice. The script extracts book titles, prices, and star ratings from 50 pages of the website and saves the data into a CSV file.

## Features
- Scrapes book titles, prices, and star ratings.
- Handles pagination to scrape data from multiple pages.
- Saves the scraped data into a CSV file (`books.csv`).

## Code Overview

The script uses the following libraries:
- `requests`: To send HTTP requests and fetch the webpage content.
- `BeautifulSoup` (from `bs4`): To parse the HTML content and extract relevant data.
- `pandas`: To store the scraped data in a structured format and export it to a CSV file.

### Code Snippet
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

books = []

for i in range(1, 51):  # Loop through 50 pages
    url = f"https://books.toscrape.com/catalogue/page-{i}.html"  # Dynamically update the URL
    response = requests.get(url)
    
    if response.status_code != 200:  # Check if the request was successful
        print(f"Failed to fetch page {i}")
        continue
    
    soup = BeautifulSoup(response.content, 'html.parser')
    ol = soup.find('ol')
    
    if ol is None:
        print(f"No book list found on page {i}")
        continue
    
    articles = ol.find_all('article', class_='product_pod')

    for article in articles:  # Corrected indentation
        image = article.find('img')
        title = image.attrs['alt'] if image else "No Title"

        star = article.find('p')
        star = star['class'][1] if star and 'class' in star.attrs else "No Rating"

        price = article.find('p', class_='price_color')
        price = price.text.strip()[1:] if price else "No Price"

        books.append([title, price, star])

# Print the first 5 books as a sample output
for book in books[:5]:
    print(book)

df = pd.DataFrame(books, columns=['Title', 'Price', 'Star Rating']) 
df.to_csv('books.csv')
