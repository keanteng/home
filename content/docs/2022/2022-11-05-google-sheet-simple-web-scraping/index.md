---
title: "Google Sheet Simple Web Scraping"
description: "Fetching Companies Market Capitalization Data"
date: "2022-11-05"
draft: false
author: "Kean Teng Blog"
tags: ["Google Sheets", "Web Scraping", "Website", "Xpath", "Market Cap"]
weight: 5
summary: "In this article, I will be showing the process of scraping some listed companies market capitalization in Malaysia data using Google Sheet. We will perform web scraping on the i3 Investor site. First of all, open a new google sheet and "
---

In this article, I will be showing the process of scraping some listed companies market capitalization in Malaysia data using Google Sheet. We will perform web scraping on the **[i3 Investor site](https://klse.i3investor.com/web/index)**.

<img src="images/img1.png"  class = "center"/>
<p style="text-align: center; color:grey;"><i>Images from Unsplash</i></p>

## Generate Webpage for Scraping
First of all, open a new google sheet and create a table like this:

<center><img src="images/img2.png"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Create a table like this</i></p>

Inside the table, we have a few companies name and their listed code. Notice that in cell C3, we put a link — this link will serve as a “prefix”. If the stock code is put at the back of the link, it will direct to the webpage of the particular page.

```
prefix link: https://klse.i3investor.com/web/stock/overview/
link to webpage: https://klse.i3investor.com/web/stock/overview/1023
```

Now, we use the concatenate function to append the code to the prefix links for all the companies in the table:

<img src="images/img3.png"  class = "center"/>
<p style="text-align: center; color:grey;"><i>Use CONCAT() function to create the links</i></p>

## Web Scraping in Action
Before we start web scraping, we need to learn about this function — IMPORTXML().

```
=importxml("url", "query")
```

Notice that we already have all the URLs needed as we have created the links on the table. Now, the missing piece is called “query” — simply means what do we want to know and where can it be found?

The query is called the XPath where it is used in web browser, now let’s hope into Maybank stock page and get the XPath query.

<img src="images/img4.png"  class = "center"/>
<p style="text-align: center; color:grey;"><i>Highlight the market capitalization amount</i></p>

After clicking inspect, a window will pop out highlighting a code segment. Here, we need to right click and select Copy full XPath.

<img src="images/img5.png"  class = "center"/>
<p style="text-align: center; color:grey;"><i>Click copy full XPath</i></p>

Let’s hope back to Google Sheet, you will be pasting the query as follow:

```
=importxml("url","/html/body/div[3]/div/div[2]/div[8]/div[1]/div[2]/div[1]/div[2]/p/strong")
Note: The URL will be the URLs in the Reference column
```

<img src="images/img6.png"  class = "center"/>

After applying the formula to the Market Cap column, you will manage to scrape all the Market Capitalization data on the sheet — the data will be updated live.

<img src="images/img7.png"  class = "center"/>
<p style="text-align: center; color:grey;"><i>Data will be loaded once your apply the formula</i></p>
