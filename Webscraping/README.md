#Deliverable 1 - Mars News

# Imported for parsing through data
from bs4 import Beautifulsoup
# Technical Documentation from Slpinter: states Selenium is need to run the Chromedriver.
from splinter import Browser
from selenium import webdriver
# Technical documentation from Splinter: states "Selenium uses the Service class to manaege chromdriver"
from selenium.webdriver.chrome.service import Service
# Technical documentation: States this Helps to run the Chrome version installed on my Windows OS
from webdriver_manager.chrome import ChromeDriverManager

# Initialize Chrome browser using the Service object for ChromeDriver
service = Service(ChromeDriverManager().install())
browser = webdriver.Chrome(service=service)

# Visit the target URL
url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
browser.get(url)

# In order pull the HTML page_source instance is called from the chrome browser connected the url - 
html = broswer.page_source

soup = BeautifulSoup(html, "html.parser')
# setting variable to extract all text content from html
extracted_text = soup.get_text()
# call print function to show all HTML content
print(extracted_text)

# create the empty list 
article_data = []



articles = soup.find_all('div', class_='list_text')  



# Loop through each article and extract the title and preview
for article in articles:
    title = article.find('div', class_='content_title').text.strip() if article.find('div', class_='content_title') else 'No title found'
    preview = article.find('div', class_='article_teaser_body').text.strip() if article.find('div', class_='article_teaser_body') else 'No preview found'
    
    # Add the extracted data as a dictionary to the list
    article_data.append({'title': title, 'preview': preview})

# Print the list of dictionaries with preview under the title
print("[")
for article in article_data:
    print(f"    {{'title': '{article['title']}',")
    print(f"     'preview': '{article['preview']}'}}")
    
print("]")

# Close broswer
browser.quit()
___________________________________________________________
# Deliverable 2 - 

# Import relevant libraries
from splinter import Browser
from bs4 import BeautifulSoup
import matplotlib.pyplot as plt
import pandas as pd
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

# Initialize Chrome browser using the Service object for ChromeDriver
service = Service(ChromeDriverManager().install())
browser = webdriver.Chrome(service=service)

# '.visit ' was changed to '.get' because the method is supported Selenium and the Webdriver.
url = 'https://static.bc-edx.com/data/web/mars_facts/temperature.html'
browser.get(url)
# Create a beautifulSoup Object to extract the table
html = browser.page_source
soup = BeautifulSoup(html, "html.parser")


# locate the first table tag
table = soup.find('table')  

# Pulling the headers and all rows of data
headers = []
for th in table.find_all('th'):
    headers.append(th.get_text(strip=True))
    rows = []
for tr in table.find_all('tr'):
    cells = tr.find_all('td')
    row = [cell.get_text(strip=True) for cell in cells]
    if row:  # Avoid adding empty rows (sometimes there may be empty <tr> elements)
        rows.append(row)

# Step 5: Create a DataFrame from the extracted rows and headers
df = pd.DataFrame(rows, columns=headers)
print(df)
# Create an empty list
rows = []
 # Loop through the rows of the table
for tr in table.find_all('tr'):
    # Extract the text from each cell in the row
    cells = tr.find_all('td')
    row = [cell.get_text(strip=True) for cell in cells]
    # Add the row to the list of rows if it is not empty
    if row:
        rows.append(row)
 headers = ['Column 1', 'Column 2', 'Column 3']  # Replace with actual headers from your table

# Create a DataFrame using the rows and the headers
df = pd.DataFrame(rows, columns=headers) 
# Print the DataFrame to confirm
print(df)
# Print the data type of each column in the DataFrame
print(df.dtypes)
# Convert the data types of the columns for analysis
# "id" is a string
df['id'] = df['id'].astype('object')   
  # Convert to datetime
df['terrestrial_date'] = pd.to_datetime(df['terrestrial_date']) 
 # Convert 'sol' to int32
df['sol'] = df['sol'].astype('int32')  
# Convert 'ls' to int32
df['ls'] = df['ls'].astype('int32')  
# Convert 'month' to int32
df['month'] = df['month'].astype('int32')
 # Convert 'min_temp' to float64
df['min_temp'] = df['min_temp'].astype('float64') 
# Convert 'pressure' to float64
df['pressure'] = df['pressure'].astype('float64') 

# Check the data types of all columns after conversion
print(df.dtypes)

# Find the unique value of months are on mars
# Check the values in the 'Months' column 
print(df['months'].nunique())
# How many sol days in months (martian days)
print(df['month'].count())

# Group by the 'month' column and calculate the mean of 'min_temp' for each group
average_min_temp_by_month = df.groupby('month')['min_temp'].mean()

# Print the result
print(average_min_temp_by_month)
import matplotlib.pyplot as plt

# Group by the 'month' column and calculate the mean of 'min_temp' for each month
average_min_temp_by_month = df.groupby('month')['min_temp'].mean()

# Plotting the data
# Set the figure size
plt.figure(figsize=(10, 6))  
plt.bar(average_min_temp_by_month.index, average_min_temp_by_month.values, color='blue')

# Adding labels and title
plt.title('Average Minimum Temperature by Month', fontsize=16)
plt.xlabel('Month', fontsize=16)
plt.ylabel('Average Minimum Temperature (°C)', fontsize=16)

# Displaying the plot
# the x-axis has the months (1 to 12)
plt.xticks(range(1, 13))  # Ensure that the x-axis shows the months (1 to 12)

plt.show()

coldest to hottest comeback

# Group by 'month' and calculate the mean of 'pressure' for each month
average_pressure_by_month = df.groupby('month')['pressure'].mean()

# Print the result
print(average_pressure_by_month)
import matplotlib.pyplot as plt

# Plotting the average pressure by month
plt.figure(figsize=(10, 6))  # Set the figure size
plt.bar(average_pressure_by_month.index, average_pressure_by_month.values, color='lightgreen')

# Adding labels and title
plt.title('Average Pressure by Month', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Average Pressure (Pa)', fontsize=14)

# Displaying the plot
plt.xticks(range(1, 13))  # Ensure that the x-axis shows the months (1 to 12)

plt.show()
import matplotlib.pyplot as plt

# Group by 'month' and calculate the mean of 'pressure' for each month
average_pressure_by_month = df.groupby('month')['pressure'].mean()

# Sort the months by their average pressure values from lowest to highest
sorted_months = average_pressure_by_month.sort_values()

# Plotting the sorted average pressure by month, from lowest to highest
plt.figure(figsize=(10, 6))  # Set the figure size
plt.bar(sorted_months.index.astype(str), sorted_months.values, color='blue')  # Convert months to string for display

# Adding labels and title
plt.title('Average Pressure by Month', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Average Pressure (Pa)', fontsize=14)

# Adjusting the x-axis ticks to show months ordered by pressure (lowest to highest)
plt.xticks(sorted_months.index.astype(str))  # Ensure months are shown in sorted order as strings



# Display the plot
plt.show()
# Ensure 'terrestrial_date' is in datetime format
df['terrestrial_date'] = pd.to_datetime(df['terrestrial_date'])

# Convert 'terrestrial_date' to the number of days since the start date
start_date = df['terrestrial_date'].min()
df['days_since_start'] = (df['terrestrial_date'] - start_date).dt.days

# Plot the daily minimum temperature against terrestrial days
plt.figure(figsize=(10, 6))  # Set the figure size
plt.plot(df['days_since_start'], df['min_temp'], color='blue', linewidth=0.8)

# Adding labels and title
plt.title('Minimum Temperature Over Time', fontsize=16)
plt.xlabel('Number of Terrestrial Days ', fontsize=16)
plt.ylabel( 'Minimum Temperature (°C)', fontsize=16)

# Set the x-axis limits from 0 to 1750 and adjust the ticks
plt.xlim(0, 1750)  # Ensure x-axis goes from 0 to 1750
plt.xticks(range(0, 1751, 250))  # Set x-axis ticks from 0 to 1750 with intervals of 250

plt.show()
    