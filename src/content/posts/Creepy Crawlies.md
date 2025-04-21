---
title: Creepy Crawlies
published: 2025-04-21
description: "A guide to web Creepy crawlies techniques and best practices."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

## Creepy Crawlies: Web Crawling and Reconnaissance

Web crawling is a powerful technique used to automatically gather data from websites, making it faster and more efficient. With the help of various crawling tools, you can focus on analyzing the extracted data rather than manually collecting it. These tools vary in capabilities, and each serves different use cases, particularly when it comes to security and reconnaissance tasks.

### Popular Web Crawlers

Here are some of the most widely used web crawlers for reconnaissance and web scraping:

### 1. **Burp Suite Spider**

- **Purpose**: A part of the Burp Suite web application testing platform, Spider is designed to crawl web applications. It maps out the website, finds hidden content, and identifies potential security vulnerabilities.
- **Use Case**: Web application security testing and vulnerability discovery.

### 2. **OWASP ZAP (Zed Attack Proxy)**

- **Purpose**: An open-source web application security scanner that includes a spidering component. It can crawl and test web applications for vulnerabilities in both automated and manual modes.
- **Use Case**: Comprehensive web security testing.

### 3. **Scrapy (Python Framework)**

- **Purpose**: A versatile Python framework for building custom web crawlers. Scrapy allows you to handle complex crawling scenarios and extract structured data from websites. It’s highly flexible and ideal for tailored reconnaissance tasks.
- **Use Case**: Custom web scraping and reconnaissance with high scalability.

### 4. **Apache Nutch (Scalable Crawler)**

- **Purpose**: A highly extensible open-source web crawler written in Java. Nutch is designed to scale for large web crawls and can be customized for specific domain crawling.
- **Use Case**: Large-scale web crawling and reconnaissance projects.

### Ethical Crawling Practices

Regardless of the tool used, it’s essential to follow ethical and responsible crawling practices:

- **Obtain permission**: Before crawling a website, ensure you have the necessary permissions, especially for large or intrusive scans.
- **Avoid overloading servers**: Be mindful of the number of requests made to the website to prevent overwhelming its server.

### Using Scrapy for Web Crawling

Scrapy is a powerful and customizable Python framework, ideal for reconnaissance tasks. Below, we will walk through setting up Scrapy and using it to crawl a target website like `inlanefreight.com`.

### Installing Scrapy

To install Scrapy, use the following command:

```jsx
pip3 install scrapy
```

This command installs Scrapy and all necessary dependencies.

### Setting Up ReconSpider

Next, download a custom Scrapy spider designed for reconnaissance tasks (in this case, `ReconSpider`) and extract the files:

```jsx
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/1
unzip ReconSpider.zip
```

This will extract the necessary files to your working directory.

### Running the Spider

Once you have extracted the files, run the spider using the following command:

```jsx
python3 ReconSpider.py http://inlanefreight.com
```

Replace **inlanefreight.com** with any domain you wish to crawl. The spider will crawl the target website and collect valuable information.

### Analyzing the Results

The results of the crawl will be saved in a JSON file called **results.json**. This file contains various types of data extracted from the target website, such as:

```jsx
{
  "emails": [
    "lily.floid@inlanefreight.com",
    "cvs@inlanefreight.com"
  ],
  "links": [
    "https://www.themeansar.com",
    "https://www.inlanefreight.com/index.php/offices/"
  ],
  "external_files": [
    "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf"
  ],
  "js_files": [
    "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3"
  ],
  "form_fields": [],
  "images": [
    "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png"
  ],
  "videos": [],
  "audio": [],
  "comments": [
    "<!-- #masthead -->"
  ]
}
```

### JSON Structure Breakdown


| JSON Key        | Description                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| emails          | Lists email addresses found on the domain.                                 |
| links           | Lists URLs of links found within the domain.                               |
| external_files  | Lists URLs of external files such as PDFs.                                 |
| js_files        | Lists URLs of JavaScript files used by the website.                        |
| form_fields     | Lists form fields found on the domain (empty in this example).             |
| images          | Lists URLs of images found on the domain.                                  |
| videos          | Lists URLs of videos found on the domain (empty in this example).          |
| audio           | Lists URLs of audio files found on the domain (empty in this example).     |
| comments        | Lists HTML comments found in the source code.                              |