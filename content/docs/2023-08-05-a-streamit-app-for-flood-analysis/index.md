---
title: "A Streamlit App For Flood Analysis"
description: "Malaysia Flood Incidents 2015-2021"
date: "2023-08-05"
draft: false
author: "Kean Teng Blog"
tags: ["Streamlit", "Flood Analysis", "GitHub", "Pandas"]
weight: 5
summary: ""
---

<center><img src="https://images.unsplash.com/photo-1558448495-5ef3fce92344?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1170&q=80"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Images from Unsplash</i></p>

In this project, I make use of [Streamlit](https://streamlit.io/), which is an open-source Python library that allows us to build and deploy powerful apps with speed and efficiency. It also offers a cloud deployment feature for you to host the Streamlit app that you created online publicly through [Streamlit Community Cloud](https://streamlit.io/cloud). The process of deploying the app is a series of workflow, as follows:
- Flood Data Collection
- App Building via Local Deployment
- Adding Additional Feature via Cloud Deployment

<center><img src="images/img1.png"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Webapp Overview</i></p>

Refer to my GitHub repository, for my work on [this project](https://github.com/keanteng/floodmap_v2)

## 1. Flood Data Collection
The data used to visualize the flood incidents in Malaysia from 2015 to 2021 can be collected by the annual report published by the [Department of Irrigation and Drainage, JPS](https://www.water.gov.my/). Due to no flood data files available online, the flood data could only be extracted by establishing data connection using Power Query on Excel. Moreover, the tables in the report has inconsistent format and typing, a series of process would need to be implemented to clean up the data set:

<center><img src="images/img2.png"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Data Cleaning Workflow</i></p>

The second issue to overcome would be the huge amount of `.xlsx` files to merge assuming you created each file for flood incidents in each state and each year.

```
TOTAL STATES = 15 (13 States and 2 Federal Territories)
TOTAL YEAR = 7
AMOUNT OF FILES = 15 * 7 = 105
```

We can use Python `openpyxl` packages to quickly merge all these files with the following code:

```python
# importing the required modules
import glob
import pandas as pd
 
# specifying the path to csv files
path = folder
 
# csv files in the path
file_list = glob.glob(path + "/*.xlsx")
 
# list of excel files we want to merge.
# pd.read_excel(file_path) reads the 
# excel data into pandas dataframe.
excl_list = []
 
for file in file_list:
    excl_list.append(pd.read_excel(file, sheet_name= 'Sheet1'))
 
# concatenate all DataFrames in the list
# into a single DataFrame, returns new
# DataFrame.
excl_merged = pd.concat(excl_list, ignore_index=True)
 
# exports the dataframe into excel file
# with specified name.
excl_merged.to_excel('FILENAME_WITH_ALL_THE_DATA.xlsx', index=False)
```

## 2. App Building via Local Deployment
To use Streamlit, we have to install it first `py -m pip install streamlit` on Windows terminal. You can check out its [documentation](https://docs.streamlit.io/) to see the list of components that can be used to build interactive and powerful web app. 

To run the app you have created, simply type `py -m streamlit run filename.py` on terminal. You will deploy the app locally. 

In my app, I included bar charts using `plotly` package to visualize the flood statistics that I gathered. Heatmap and Marker Cluster map were also plotted to indicated the location of flood and the areas in a particular state that experienced a higher density of flood incidents. The relevant code can be found in my [Git repository](https://github.com/keanteng/floodmap_v2/tree/main). 

## 3. Adding Additional Feature via Cloud Deployment
In my project, I added some tools for flood extent analysis and features supported by Google Earth Engine using resources from [mapaction/flood mapping tool](https://github.com/mapaction/flood-mapping-tool) and [opengeos/streamlit-geospatial](https://github.com/opengeos/streamlit-geospatial).

The reason these features need to be added via cloud deployment of Streamlit is because of a missing package `fcntl module` that is not available on my Windows machine but on Linux system. Of course, after deploying these feature to cloud, authentication token from Google Earth Engine would also be required. To get the key, got to Windows Terminal:

```python
py -m pip install ee
import ee
ee.Authenticate()
```

You will need to paste the authorization code back on the terminal. Once the step is complete, you can find the token on your local machine at `C:\\Users\\Username\\.congif\\earthengine\\credentials`. It is also important to note that the token at `C:\\Users\\Username\\.congif\\earthengine\\credentials` would need to be put at the secret section of the app that you deployed on cloud using the following format:

```toml
EARTHENGINE_TOKEN = 'PASTE WHAT YOU COPY HERE'
ee_keys = 'PASTE WHAT YOU COPY HERE'
```

The secret section can be found by clicking the app setting > secret. 

After that, the app is ready to be shared and used!