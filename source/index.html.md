---
title: Parkpow API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python

toc_footers:
  - <a href='https://parkpow.com/'>Sign Up for a Developer Key</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Parkpow API!

# Authentication

Parkpow.com API is only available to registered users. You first have to register and **[get an API key](https://app.parkpow.com/accounts/token/)**. It has to be included in all API calls. The HTTP **headers** must contain:

`Authorization: Token API_TOKEN`

<aside class="notice">
You must replace <code>API_TOKEN</code> with your personal API key.
</aside>

# Parkings

## Get All Parkings

```python
# pip install requests
import requests
from pprint import pprint

response = requests.post(
    'https://app.parkpow.com/api/v1/parking-list/',
    headers={'Authorization': 'Token API_TOKEN'})
pprint(response.json())
```

```shell
curl "https://app.parkpow.com/api/v1/parking-list/"
  -H "Authorization: Token API_TOKEN"
```

> The above command returns JSON structured like this:

```json
[
  {
    "camera_set": [
      {
        "code": "101",
        "name": "cam-101",
        "type": "Entrance",
        "latitude": "None",
        "longitude": "None",
        "notes": "notes"
      },
      {
        "code": "102",
        "name": "cam-102",
        "type": "Exit",
        "latitude": "None",
        "longitude": "None",
        "notes": "notes"
      },
    ], 
    "name": "first-parking",
    "parking_spaces": 100,
    "absent_timer": 3
  },
    {
    "camera_set": [
      {
        "code": "202",
        "name": "cam-202",
        "type": "Entrance",
        "latitude": "None",
        "longitude": "None",
        "notes": "notes"
      }
    ], 
    "name": "second-parking",
    "parking_spaces": 120,
    "absent_timer": 3
  },
]
```

This endpoint retrieves all parkings.

### HTTP Request

`https://app.parkpow.com/api/v1/parking-list/`


## Get a Visit list


```python
# pip install requests
import requests
from pprint import pprint

response = requests.post(
    'https://app.parkpow.com/api/v1/visit-list/',
    data={'start': '2019-06-10', 'end': '2019-06-11'},
    headers={'Authorization': 'Token API_TOKEN'})
pprint(response.json())
```

```shell
curl "https://app.parkpow.com/api/v1/visit-list/"
  -H "Authorization: Token API_TOKEN"
```


> The above command returns JSON structured like this:

```json
[{"id": 4, "vehicle": {"license_plate": "QWE1234", "status": "Authorized"}, "start_cam": {"code": "101", "name": "cam-101", "type": "Entrance", "latitude": "None", "longitude": "None", "notes": "notes"}, "end_cam": {"code": "102", "name": "cam-102", "type": "Exit", "latitude": "None", "longitude": "None", "notes": "notes"}, "start_img": "example.com/aaw1108_9YG0bJS.JPG", "end_img": "example.com/aaw1108_D5DiEqD.JPG", "duration": 4.0, "start_date": "2019-06-08T15:40:19.419745", "start_prediction": {"box": {"xmax": 257, "xmin": 166, "ymax": 260, "ymin": 223}, "plate": "ch102tc", "score": 0.853, "dscore": 0.936}, "end_date": "2019-06-09T15:40:19.419745", "end_prediction": "None"}]
```

This endpoint retrieves a list of Visits.

### HTTP Request

`GET https://app.parkpow.com/api/v1/visit-list/`

### GET Parameters

Parameter | Description
--------- | -----------
start | start of period
end | end of period

## Create/Update a Vehicle with current payment status


```python
# pip install requests
import requests
from pprint import pprint

response = requests.post(
    'https://app.parkpow.com/api/v1/create-vehicle/',
    data={'license_plate': 'ABC1234', 'payment_status': 0},
    headers={'Authorization': 'Token API_TOKEN'})
pprint(response.json())
```

```shell
curl "https://app.parkpow.com/api/v1/create-vehicle/"
  -H "Authorization: Token API_TOKEN"
```

> The above command returns JSON structured like this:

```json
{
  "license_plate": "ABC1234",
  "payment_status" : 0
}
```

This endpoint Create/Update a Vehicle with current payment status.

### HTTP Request

`POST https://app.parkpow.com/api/v1/create-vehicle/`

### POST Parameters

Parameter | Description
--------- | -----------
license_plate | license_plate of Vehicle
payment_status | payment status of Vehicle


## LPR API

```python
# pip install requests
import requests
from pprint import pprint

response = requests.post(
    'https://app.parkpow.com/api/v1/main-lpr-view',
    data={
            "results": [{
                "plate": "ASD1234",
            }],
            "camera": "camera.code",
            "filename": "image.name",
            "fileurl": "path/to/file"
        },
    headers={'Authorization': 'Token API_TOKEN'},
    files={"file": 'uploaded.file'})
pprint(response.json())
```

This endpoint monitors the selected folder for new images and sends them to recognition.

### POST Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
source | str | True | Where camera images are saved
archive | str | True | Where images are moved to archive after being processed
parkpow-token | str | True | API token for ParkPow
workers | int | False | Number of worker threads
alpr-api | str | False | URL of SDK API
api-url | str | False | API url

