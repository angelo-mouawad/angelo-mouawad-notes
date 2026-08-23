# What is Data Collection ?

Data collection is the systematic process of gathering, measuring, and analyzing accurate information from various sources to answer research questions, test hypotheses, or evaluate outcomes. It is the foundational first stage of the data lifecycle, enabling evidence based decision making and AI training.

There are 3 different ways of collecting data.
- Collecting data from an API.
- Collecting data by web scraping.
- Collecting data using real time data ingestion.

---

## Collecting Data From an API

You can fetch data from a public API, process it, and store it in a CSV file. This will require the python requests library.

Importing Requests.
```python
import requests
```

To start with we will learn how to simply fetch data from an API.
```python
url = "https://path/to/url"
response = requests.get(url)
data = response.json()
```

You can also provide a path variable with the URL while fetching from an API that accepts path variables.
```python
name = "angelo"
url = f"https://path/to/url?name={name}"
response = requests.get(url)
data = response.json()
```

Finally, you can save your results to a CSV file using two methods. The first method is to make a Data frame and then use the `to_csv` function to convert that Data frame to a CSV file.
```python
import requests
import pandas as pd

url = "https://api.open-meteo.com/v1/forecast"
params = {
	"param1": param,
	"param2": param
}

response = requests.get(url, params=params)
data = response.json()

  
df = pd.DataFrame({
    "Col1": data,
    "Col2": data
})

df.to_csv("name.csv", index=False)
```

As you can see from the above example, you can also add parameters to fetch with your URL in case you need to be more specific with the data needed.

The second method to do this is to write the data straight into a CSV file using the CSV function from the `csv` library.
```python
import requests
import csv

url = "https://api.open-meteo.com/v1/forecast"
params = {
	"param": param,
	"param": param
}

response = requests.get(url, params=params)
data = response.json()

data1 = data
data2 = data

with open("name.csv", "w", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["Col1", "Col2"])
    for d1, d2 in zip(data1, data2):
        writer.writerow([d1, d2])
```

Sometimes an API can limit the number of requests a user sends to its servers. API rate limiting happens to prevent servers from being overloaded by too many requests in a short period of time. It also protects against abuse, such as bots, scraping, or denial-of-service attacks.

If an API rate limits requests, it typically returns an HTTP `429 Too Many Requests` status code, meaning you have exceeded the allowed number of requests. To handle this, your program should detect the 429 response and wait before retrying. This wait can also be automated in python code using a `Retry-After`.

---

## Collecting Data by Web Scraping

Web scraping is the process of automatically extracting data from websites using a program instead of manually copying it. It works by sending a request to a webpage, downloading the HTML, and parsing it to collect specific information. The extracted data can then be stored in formats like CSV or databases for analysis.

Web scraping uses the Beautiful Soup library, this is how to import it.
```python
from bs4 import BeautifulSoup
```

We will now learn how to scrape a table from a Wikipedia website.
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
  
headers = {
    "User-Agent": "Mozilla/5.0"
}

url = "https://en.wikipedia.org/path/to/url"
response = requests.get(url, headers=headers)

soup = BeautifulSoup(response.text, "html.parser")

table = soup.find("table", {"class": "wikitable"})

rows = []

for tr in table.find_all("tr")[1:]:
    cols = tr.find_all("td")
    if len(cols) >= 3:
        Col1 = cols[0].get_text(strip=True)
        Col2 = cols[1].get_text(strip=True)
        rows.append([Col1, Col2])

df = pd.DataFrame(rows, columns=["Col1", "Col2"])

df.to_csv("name.csv", index=False)
```

Websites block scrapers by detecting unusual behavior, such as sending too many requests in a short time or making requests without a proper browser **User-Agent** header. They may use CAPTCHAs, IP blocking, or rate limiting to prevent automated access. Some sites also rely on JavaScript rendering or authentication requirements to make scraping more difficult.

---

## Collecting Data Using Real Time Data Ingestion

 We will now learn how to **build a simple real-time data pipeline using RabbitMQ**, where simulated **IoT devices send sensor data**, and another program **receives and processes it**.

RabbitMQ is a **message queue system** that allows applications to **send messages to each other asynchronously**. In data pipelines, it is often used for **real-time data ingestion**.

A producer pipelines simulates IoT devices and sends data, and a consumer pipeline receives and processes the data in real time. RabbitMQ acts as the **message broker between them**.

The process works in this order.
1. IoT Device Simulator acts as Producer.
2. RabbitMQ Message Queue.
3. Consumer Application.
4. Processing, storage, and analytics.

The key advantages of using RabbitMQ are that the producer and consumer **do not need to run at the same speed**. And messages are **stored temporarily in the queue**.

This method uses the Pika library, this is how to import it.
```python
import pika
```

First we will create a producer that produces data. The code first connects to RabbitMQ, then creates a queue, and finally sends messages to the queue.
```python
import pika
import json
import random
import time

# connect to RabbitMQ
connection = pika.BlockingConnection(
    pika.ConnectionParameters('localhost')
)

channel = connection.channel()

# create queue
channel.queue_declare(queue='iot_data')

while True:
    
    data = {
        "device_id": f"sensor_{random.randint(1,5)}",
        "temperature": round(random.uniform(20,30),2)
    }

    message = json.dumps(data)

    channel.basic_publish(
        exchange='',
        routing_key='iot_data',
        body=message
    )

    print("Sent:", message)

    time.sleep(2)
```

Now we will created a consumer that listens to the queue and processes messages. The code first connects to RabbitMQ, then it waits for messages, when the producer sends messages, the consumer will print them.
```python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters('localhost')
)

channel = connection.channel()

channel.queue_declare(queue='iot_data')

def callback(ch, method, properties, body):
    print("Received:", body.decode())

channel.basic_consume(
    queue='iot_data',
    on_message_callback=callback,
    auto_ack=True
)

print("Waiting for messages...")
channel.start_consuming()
```

---
