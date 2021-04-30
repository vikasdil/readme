# Covid-19 Data
Authors :- Jasminkumar Kantibhai Gondaliya, Parth Ajay Shah, Parth Himanshu Sampat, Vikas Dilip Patil


# Intoduction

The purpose of this project is to visualise the covid-19 data
the type of data that is being imported is a confirmed, Active, Death & Recoverd cases of Covid-19 for various countries.
Data used for this projected is from the website "covid-19-data.p.rapidapi.com"
#Explanation of the Code
The first garph from the code shows the worldwide data of confirmed, recovered and death due to covide pandemic.
The second graph indicate monthly data of confirmed, recovered, death and active cases.
The final graph gives country wise information.

The code, begins by importing necessary Python packages:





import requests

import json

import matplotlib.pyplot as plt

import numpy as np

from datetime import datetime

import time




Rapid API Covid-19 provides data of daily covid cases of various contries and it also provide information about total number of cases worldwide.




headers = {
    'x-rapidapi-key': "9a9deff63amshe883f563b7b3a32p1ddb05jsn5fbdb945e9c7",
    'x-rapidapi-host': "covid-19-data.p.rapidapi.com"
    }

 Now we are plotting pie chart which contain infromation about total confirmed, recoverd and death cases worldwide.
 Here we are using json to convert the data from web page in to text format.
 
 
 
 

```python
 def plotWorldPie():
    url = "https://covid-19-data.p.rapidapi.com/totals"

    data = json.loads(requests.request("GET", url, headers=headers).text)

    labels = ["confirmed", "deaths", "recovered"]
    values = [data[0]["confirmed"], data[0]["deaths"], data[0]["recovered"]]
    explode = [0, 0.1, 0]
    colors = ["yellow", "red", "green"]
   
    fig, ax = plt.subplots()
    ax.pie(values, explode=explode, colors=colors, autopct='%.2f', labels=labels, shadow=True, startangle=90)
    ax.axis('equal')
    fig.savefig('world_case_pie.jpg', dpi=fig.dpi)
```
   
   
Now we are plotting a line graph of each day for perticular month.
First we created an empty lists for diffrent parameter used in chart and later we append these lists.
02d is used to make the length of the string constant
querystring = {"date":dateStr} is used to convert the string in to python redable date format.



```python
def plotWorldLine():
    url = "https://covid-19-data.p.rapidapi.com/report/totals"

    dates = []
    confirmed = []
    active = []
    recovered = []
    deaths = []
    for d in range(1,31):
        dateStr = "2020-05-{:02d}".format(d) # 02d is to make the length of the string constant
        querystring = {"date":dateStr}
        time.sleep(1.5)
        worldTotals = requests.request("GET", url, headers=headers, params=querystring).text
        data = json.loads(worldTotals)
        dates.append(datetime(2020, 5, d))
        confirmed.append(data[0]["confirmed"])
        active.append(data[0]["active"])
        recovered.append(data[0]["recovered"])
        deaths.append(data[0]["deaths"])

    fig = plt.figure()

    plt.plot(dates, confirmed, 'b-o', label = "Confirmed")
    plt.plot(dates, active, 'y-^', label = "Active")
    plt.plot(dates, recovered, 'g-o', label = "Recovered")
    plt.plot(dates, deaths, 'r-o', label = "Deaths")

    plt.xlabel('Dates', fontsize=14)
    plt.ylabel('Cases')
    plt.title('Cases in May 2020', fontsize=18)
    fig.autofmt_xdate()
    plt.legend()
    fig.savefig('world_case_line.jpg', dpi=1000)
```
In the third graph which is a bar graph in which we are comapring cases of various countries.
Here we are comparing on basis of three parameters which are active cases, confirmed cases and death.
We initialize the three variables and then we are adding values to those variables and lastly we are appending the final values to the list.
Save fig command helps to save image in the directory of the jupyter notebook.



 ```python
 def compareCountries():
    url = "https://covid-19-data.p.rapidapi.com/report/country/name"
    countries = ['mexico', 'brazil', 'india', 'canada', 'italy', 'germany']
   
    confirmed = []
    deaths = []
    active = []
    for c in countries:
        querystring = {"date":"2020-05-01","name":c}
        time.sleep(1.5)
        countryData = requests.request("GET", url, headers=headers, params=querystring).text
        data = json.loads(countryData)
        country_confirmed = 0
        country_active = 0
        country_deaths = 0
        for d in data[0]["provinces"]:
            country_confirmed += d["confirmed"]
            country_active += d["active"]
            country_deaths += d["deaths"]
        confirmed.append(country_confirmed)
        deaths.append(country_deaths)
        active.append(country_active)

    x = np.arange(len(countries))
    width = 0.2
    fig = plt.figure()
    plt.bar(x-0.2, confirmed, width, color='cyan')
    plt.bar(x, active, width, color='yellow')
    plt.bar(x+0.2, deaths, width, color='red')
    plt.xticks(x, countries)
    plt.xlabel("Countries")
    plt.ylabel("Cases")
    plt.legend(["Confirmed", "Active", "Deaths"])
    fig.savefig('country_case_comparison.jpg')
    plotWorldPie()
    plotWorldLine()
    compareCountries()
```
