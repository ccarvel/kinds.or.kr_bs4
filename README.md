# kinds.or.kr_bs4
## notes on scraping dynamic news db


For dynamic sites that use a lot of busy javascript libraries and frameworks, scraping is sometimes easier if you download the url as an html file in a local directory. Assuming you have a directory full of html files that won't scrape by connecting to the online page in a within a subdirectory of your ```~/Downloads``` directory (ie ```/Users/username/Downloads/html-to-scrape```), you can use Beautiful Soup 4 to extract the content to something more structured in a CSV--

**So, for example--**
Save the difficult pages manually or come up with an automated way to save these hard to scrape pages in bulk. For the following page, I just loaded it in Chrome and saved as a webpage: [https://www.kinds.or.kr/v2/news/newsDetailView.do?newsId=01101001.19960316000000306](https://www.kinds.or.kr/v2/news/newsDetailView.do?newsId=01101001.19960316000000306)
I put it in a subdirectory of /Users/username/Downloads named 'html-to-scrape'

When you've reviewed the files and the html tags you want to organize into a CSV, (for this particular page) you can get the content of tags related to this particular page, but I think this sets up a method for adding additional tags as needed.


snip:
```filename: kinds.or.kr_bs4.py```
```
import os
import csv
from bs4 import BeautifulSoup

##### SET THIS PATH TO YOUR PARTICULAR DIRECTORY OF SAVED HTML FILES
html_dir_path = '/Users/codycarvel/Downloads/html-to-scrape'

# SET THE NAME OF THE OUTPUT CSV FILE
csv_file_name = 'output.csv'

##### ADD HEADER INFO HERE BASED ON TAGS YOU WANT TO EXTRACT
#### CREATE LISTS TO HOLD THE EXTRACTED TITLES, BODIES, AND FILENAMES
titles = []
dates = []
bodies = []
filenames = []

# LOOP THROUGH EACH FILE IN THE DIRECTORY
for filename in os.listdir(html_dir_path):
    # CHECK IF THE FILE IS AN HTML FILE
    if filename.endswith('.html'):
        # OPEN THE HTML FILE
        with open(os.path.join(html_dir_path, filename), 'r') as file:
            # PARSE THE HTML USING BEAUTIFUL SOUP 4
            soup = BeautifulSoup(file.read(), 'html.parser')
            ##### BELOW YOU WILL ADD OR REMOVE TAGS YOU NEED FROM YOUR DIFFICULT HTML
            # FIND THE TITLE TAG AND EXTRACT ITS TEXT
            title = soup.title.text if soup.title else ''
            ##### AND ADD WHAT GETS FOUND TO THE LIST OF HEADERS ABOVE
            # ADD THE TITLE TO THE LIST OF TITLES
            titles.append(title)
            # FIND THE DATE TAG AND EXTRACT TEXT
            date = soup.find('li', {'class': 'info_date'}).text.strip() if soup.find('li', {'class': 'info_date'}) else ''
            # ADD THE TEXT FOUND IN THE DATE-RELATED TAG TO THE DATES LIST
            dates.append(date)
            # FIND THE DIV TAG WITH CLASS 'DOC_DESC' AND ID 'CONTENT', AND EXTRACT ITS TEXT
            body text = soup.find('div', {'class': 'doc_desc', 'id': 'content'}).text.strip() if soup.find('div', {'class': 'doc_desc', 'id': 'content'}) else ''
            # ADD THE BODY TO THE LIST OF BODIES
            bodies.append(body)
            # ADD THE FILENAME TO THE LIST OF FILENAMES
            filenames.append(filename)

# WRITE THE EXTRACTED TITLES, BODIES, AND FILENAMES TO A CSV FILE
with open(csv_file_name, 'w', newline='') as file:
    writer = csv.writer(file)
    writer.writerow(['file', 'title', 'body'])
    for filename, title, date, body in zip(filenames, dates, titles, bodies):
        writer.writerow([filename, date, title, body])
