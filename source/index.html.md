---
title: ParkPow API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - python

toc_footers:
  - <a href='https://parkpow.com/'>Sign Up for a Developer Key</a>

search: true
---

# API DOCUMENTATION

Welcome to the ParkPow API! ParkPow is a software to manage and enforce parking lots. It lets you track vehicles, get custom alerts, enforce your parking rules.


# Authentication

ParkPow.com API is only available to registered users. You first have to register and **[get an API key](https://app.parkpow.com/accounts/token/)**. It has to be included in all API calls. The HTTP **headers** must contain:

`Authorization: Token API_TOKEN`

<aside class="notice">
You must replace <code>API_TOKEN</code> with your personal API key.
</aside>

# Import

## Create or Update Vehicle Details

```python
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

> Returns the following JSON:

```json
{
  "license_plate": "ABC1234",
  "payment_status" : 0
}
```

Purpose: Incorporate 3rd party data (e.g. tenant database, HR database, payment database) into ParkPow so that you can more fully understand the vehicles captured in the dashboard.

### HTTP Request

`POST https://app.parkpow.com/api/v1/create-vehicle/`

### POST Parameters

Parameter | Description
--------- | -----------
license_plate | License plate of vehicle.
payment_status | Payment status.
field1 | Custom field 1.
field2 | Custom field 2.
field3 | Custom field 3.
field4 | Custom field 4.
field5 | Custom field 5.
field6 | Custom field 6.

# Export

## Export All Parking Data
```python
import requests
from pprint import pprint

response = requests.get(
    'https://app.parkpow.com/api/v1/parking-list/',
    headers={'Authorization': 'Token API_TOKEN'})
pprint(response.json())
```

```shell
curl "https://app.parkpow.com/api/v1/parking-list/"
  -H "Authorization: Token API_TOKEN"
```

> Returns the following JSON:

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

Purpose: Retrieve all parking data including the cameras associated with each parking lot.

### HTTP Request

`GET https://app.parkpow.com/api/v1/parking-list/`

<!--
## Export License Plate Data

Purpose: Retrieve entry and exit information for a particular license plate from the last given number of occurrences. This can be utilized to help end consumers locate the parking lot of their vehicle for a specific license plate. The “last given number of occurrences” is in descending order from the present, so to get the last, most recent occurrence, set the parameter to 1.
-->

## Export Vehicle Visits

```python
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


> Returns the following JSON:

```json
[{"id": 4, "vehicle": {"license_plate": "QWE1234", "status": "Authorized"}, "start_cam": {"code": "101", "name": "cam-101", "type": "Entrance", "latitude": "None", "longitude": "None", "notes": "notes"}, "end_cam": {"code": "102", "name": "cam-102", "type": "Exit", "latitude": "None", "longitude": "None", "notes": "notes"}, "start_img": "example.com/aaw1108_9YG0bJS.JPG", "end_img": "example.com/aaw1108_D5DiEqD.JPG", "duration": 4.0, "start_date": "2019-06-08T15:40:19.419745", "start_prediction": {"box": {"xmax": 257, "xmin": 166, "ymax": 260, "ymin": 223}, "plate": "ch102tc", "score": 0.853, "dscore": 0.936}, "end_date": "2019-06-09T15:40:19.419745", "end_prediction": "None"}]
```

Purpose: retrieve all visits made during a period. If the period is omitted, retrieve all visits made during the last 24 hours.

### HTTP Request

`GET https://app.parkpow.com/api/v1/visit-list/`

### GET Parameters

Parameter | Description
--------- | -----------
start | Start of period.
end | End of period.

# Integration

## Send Camera Images and License Plate Data

```python
import requests
from pprint import pprint
import os

path = '/tmp/my-image.jpg'
alpr_results = [dict(plate='abcd1234')]
filename, _ = os.path.split(path)
response = requests.post(
    'https://app.parkpow.com/api/v1/main-lpr-view',
    data={
            "results": alpr_results,
            "camera": "camera_code",
        },
    headers={'Authorization': 'Token API_TOKEN'},
    files={"image": (filename, open(path, 'rb'), 'application/octet-stream')})
pprint(response.json())
```
```shell
# See Python example
```

> Returns the string:
`OK`


Purpose: integrate your cameras and ALPR software with ParkPow.

### POST Parameters

Parameter | Type | Required | Description
--------- | ---- | -------- | -----------
camera | string | True | Code of the camera capturing the image.
image | file | True | Image captured by the camera.
results| JSON | True | License plate detected by the ALPR software (for example Plate Recognizer SDK).

## Automatic Image Transfer

Automatic Image Transfer is a command line tool that runs our [ALPR SDK](https://platerecognizer.com) and sends the results to ParkPow.

It monitors a directory and all its sub-directories. When a new image is added, it passes them to the SDK that reads license plates. The data is then sent to our parking management service Parkpow. Once processed, images are moved to an archive directory.

To get started call `python transfer.py --help`

### Requirements:
- Plate Recognizer SDK
- A Parkpow account
- Python3
