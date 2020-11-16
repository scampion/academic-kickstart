---
title: Real Time Python processing for machine learning
description: A short memo about faust and how to use Kubernetes and horizontal scaling
date: "2019-05-02T19:25:30+02:00"
publishDate: "2019-05-02T19:25:30+02:00"
---

Our goal is to process messages received from a Kafka stream.
We need to submit that message through an HTTP API interface supported by Kubernetes and a Ingress router. 
We will use horizontal scaling feature provided by Kubernetes to ensure a constant time treatment.

To achieve this goal, we can use the python library [Faust](https://faust.readthedocs.io/)
    
    Faust is a stream processing library, porting the ideas from Kafka Streams to Python.
    It is used at Robinhood to build high performance distributed systems and real-time data pipelines that process billions of events every day.


### Pseudo code

```
for each message m received in topic Kafka input:
  for each model in production:
    predict(model, m)
      send to topic Kafka output, message m and all predictions
```



### Source code

{{< gist scampion 34a570f6557e0dd5f59af6ad42d0a913 >}}


`load_url` could be a simple function like this one : 

```python 
def load_url(url, data, timeout):
    r = requests.post(url, json=adata, timeout=timeout)
```
