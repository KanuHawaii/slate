---
title: SEI Grid Maestro API Reference V1.41

#language_tabs:


toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

The Shifted Energy Grid Maestro™ API exposes a select number of grid service features for third party clients and partners. This API does not expose every feature of Grid Maestro™. Additional features can be exposed over the API as needed. For example, Emergency Demand Response and Fast Frequency Response are available visually over the Grid Maestro™ Portal (https://www.ShiftedEnergy.com/setpoint/) but not currently over the API. Adding additional API calls for existing functionality can be completed quickly as existing features already have underlying API calls. The Grid Maestro™ API does not use any third party APIs and thus customizations are not restricted in any way by third party systems.

###General Response Notes:

All calls and responses are in JSON format. If another format is required, it can be implemented. Most calls are already available in XML.
The core Grid Maestro™ security layer requires an authorized Grid Maestro™ API username and password for every API request over SSL. This is considered the lowest acceptable security layer.  Additional security measures such as OAuth2 can be added as deemed necessary.
All dates in the API are in the ISO 8601 format in UTC unless otherwise specified. Some control calls are sent in the local time of the group.

###Changes from Prior Versions
Upgrading version 1.2 to version 1.3 created minor changes to syntax. These changes were made to make naming conventions consistent.

Naming conventions were standardized. The following changes were made API wide
group are changed to group_id  across multiple calls, for example:
get_baseline_forecast
get_group_load_shift_report
get_group_load_shift_forecast
initiate_load_shift_event
controllers in the URL and data packets are changed to device across multiple calls
number_controllers changed to number_of_devices  across multiple calls
instances of name for group commands are changed to group_name across multiple calls


# Authentication

> To validate username and password:

```shell
curl -X POST https://api.shiftedenergy.com/validate_user \
-H 'Content-Type: application/json' \
-d '{
    "root": {
        "user": {
            "email": "your@email.com",
            "password": "your_password!"
        }
    }
}'
```

Grid Maestro requires a username and password combination in every API call.
To acquire such login credentials, please contact your project administrator.

<aside class="notice">
You must replace <code>email</code> and <code>password</code> with your login credentials.
</aside>

# Device Data Access

## Get Controller Meta Information

```shell
curl -X POST https://api.shiftedenergy.com/get_device_info \
-H 'Content-Type: application/json' \
-d '{
    "root": {
        "user": {
            "email": "your@email.com",
            "password": "your_password!"
        },
        "api_id": "device1"
    }
}'
```

> The above command returns a JSON object structured as follows:

```json
{
    "message": {   
        "api_id":"device1",
        "serial_number":"SER_11111111",
        "timezone":"Pacific/Honolulu",
        "group_id":"group_1",
        "local_first_data_date":"2019-12-31",
        "network_status":"online",
        "last_telemetry_date":"2019-01-01",
        "frequency":60,
        "voltage":245,
        "last_signal": {
            "signal_date": "2019-01-01T12:00:00.000Z",
            "network": "LTE",
            "strength": 67.01,
            "rssi": -76,
            "signal_summary": "good",
        }
    }
}
```

Retrieve meta information for a single device. Api_id, serial_number, timezone, group, and first_data_date are constant and definite. Status, last_frequency, last_voltage, and signal are last known stored data and may change regularly. See Example Success Response below. 

### HTTP Request

`POST https://api.shiftedenergy.com/get_device_info`

### JSON Body

Parameter | Required | Description
--------- | ------- | -----------
api_id | true | Digital id of the controller

<!--<aside class="success">-->
<!--Remember — a happy kitten is an authenticated kitten!-->
<!--</aside>-->

<aside class="warning">
Tempo and Ara JSON responses may vary slightly.
</aside>

### Response Notes
* Online: device is reporting telemetry to SEI normally
* Offline: device has not sent telemetry to within the last hour, likely due to cellular signal interruptions
* Decommissioned: device will power on, but will not send telemetry to SEI
* Not Found: device has not yet been commissioned; or an incorrect api_id was entered



## Get Tempo Controller Historical Telemetry

```shell
curl -X POST 
https://api.shiftedenergy.com/get_device_telemetry \
-H 'Content-Type: application/json' \
-d '{
    "root": {
    "user": {
        "email": "your@email.com",
        "password": "your_password!"
    },
    "api_id": "device_1",
    "local_start_date":"2019-01-01",
    "local_end_date":"2019-01-03"
    }
}'
```

> The above command returns a JSON object structured as follows:

```json
{
    "message": {
        "api_id": "device_1",
        "timezone":"Pacific/Honolulu",
        "local_start_date":"2019-01-01",
        "local_end_date":"2019-01-03",
        "telemetry": [
            {
            "reason": "auto",
            "type": "update",
            "frequency": 59.99,
            "on": "true",
            "watthour_data": 0,
            "voltage": 208.64,
            "local_timestamp": "2019-01-01T00:00:00"
            },
            {
            "reason": "reboot",
            "type": "relay",
            "frequency": null,
            "on": "true",
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T00:15:00"
            },
            {
            "reason": "ffr",
            "type": "trigger",
            "frequency": 59.688,
            "on": null,
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T00:21:29"
            },
            {
            "reason": "ffr",
            "type": "relay",
            "frequency": null,
            "on": "false",
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T00:21:29"
            },
            {
            "reason": "ffrr",
            "type": "trigger",
            "frequency": 60.012,
            "on": null,
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T00:24:41"
            },
            {
            "reason": "ffrr",
            "type": "relay",
            "frequency": null,
            "on": "false",
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T00:24:41"
            },
            {
            "reason": "auto",
            "type": "update",
            "frequency": 59.81,
            "on": "false",
            "watthour_data": 0,
            "ambient_temperature": 105,
            "voltage": 212.11,
            "local_timestamp": "2019-01-01T00:30:00"
            },
            {
            "reason": "api",
            "type": "relay",
            "frequency": null,
            "on": "false",
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T00:45:00"
            },
            {
            "reason": "power_limit",
            "type": "relay",
            "frequency": null,
            "on": "false",
            "watthour_data": null,
            "voltage": null,
            "local_timestamp": "2019-01-01T01:00:00"
            }
        ]
    }
}

```

Returns five or fifteen minute telemetry for a single device, up to 4 consecutive days for a range of historical date. Only devices that are configured for five minute telemetry return five minute telemetry. All others return 15 minute telemetry. Works for both Tempo and Ara controllers.

<!--<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>-->

### HTTP Request

`POST https://api.shiftedenergy.com/get_device_telemetry`

### JSON Body

Parameter | Required | Description
--------- | ------- | -----------
api_id | true | Digital id of the controller
local_start_date | true | Start date in yyyy-mm-dd format, in your local timezone
local_end_date | true | End date in yyyy-mm-dd format, in your local timezone

### Response Notes
* Reason For Relay Actuation:
    * auto - An automated push of telemetry from the device to the cloud
    * ffr - Relay turned off due to Fast Frequency Response (FFR)
    * ffrr - Relay turned back on due to a return to normal frequency (FFR related)
    * reboot - The device rebooted based on internal watchdogs or a scheduled periodic reboot
* On:
    * true: relay status is on
    * false: relay status is off
* Watthour_data: watthour data consumed during each five/fifteen minute increment depending on preferred implementation
* Temperature: optional ambient temperature sensor. In degrees fahrenheit
* Returns telemetry from local_start_date up to and including local_end_date


## Get Tempo Controller Historical Watt-hour Data

```shell
curl -X POST https://api.shiftedenergy.com/get_historical_data \
-H 'Content-Type: application/json' \
-d '{
    "root": {
        "user": {
            "email": "your@email.com",
            "password": "your_password!"
        },
        "local_start_date": "2019-12-13",
        "local_end_date": "2019-12-14",
        "api_id": "device_1"
    }
}'
```

> The above command returns a JSON object structured as follows:

```json
{
    "message": {   
        "api_id":"device_1",
        "local_start_date":"2019-12-13",
        "local_end_date": "2019-12-14",
        "timezone":"Pacific/Honolulu",
        "wh_data":[
            {
                "local_date": "2019-10-13",
                "telemetry": [ 
                    {
                        "timeslot": "00:00",
                        "actual_wh": 0
                    },
                    {
                        "timeslot": "00:15",
                        "actual_wh": "null"
                    },
                    ...
                    {
                        "timeslot": "23:15",
                        "actual_wh": 0
                    },
                    {
                        "timeslot": "23:30",
                        "actual_wh": 0
                    },
                    {
                        "timeslot": "23:45",
                        "actual_wh": 0
                    }
                ]
            },
            {
                "local_date": "2019-10-13",
                "telemetry": [ 
                    {
                        "timeslot": "00:00",
                        "actual_wh": 0
                    },
                    {
                        "timeslot": "00:15",
                        "actual_wh": "null"
                    },
                    ...
                    {
                        "timeslot": "23:15",
                        "actual_wh": 0
                    },
                    {
                        "timeslot": "23:30",
                        "actual_wh": 0
                    },
                    {
                        "timeslot": "23:45",
                        "actual_wh": 0
                    }
                ]
            }
        ]
    }
}

```

This function retrieves historical 15-minute interval watt-hour data for up to 4 consecutive days, and does not include data like voltage and frequency.

### HTTP Request

`POST https://api.shiftedenergy.com/get_device_telemetry`

### JSON Body

Parameter | Required | Description
--------- | ------- | -----------
api_id | true | Digital id of the controller
local_start_date | true | Start date in yyyy-mm-dd format, in your local timezone
local_end_date | true | End date in yyyy-mm-dd format, in your local timezone

###Response Notes
* actual_wh: if a unit was offline for any telemetry period, the actual_wh value will be NULL.
* Returns telemetry from local_start_date up to and including local_end_date

