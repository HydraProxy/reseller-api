# HydraProxy API Documentation v1.0

### Table Of Contents
- [Account General Info](#account-general-info)
- [Product General Info](#product-general-info)
- [API General Usage Info](#api-general-usage-info)
- [Required Headers](#required-headers)
- [API Endpoints](#api-endpoints)


---

### Account General Info

- This API is available only for reseller accounts (for any business that wants to resell access to our networks).
- Reseller accounts are created manually. Please contact us at api@hydraproxy.com or visit our website at [hydraproxy.com](https://hydraproxy.com/) and reach us via our webchat.
- You need an API Authorization Token to access the API endpoints. It will be provided once your account is created.
- Funds are deposited manually, upon requests. Accepted payment methods: Cards (via Stripe) and Cryptocurrencies. Minimum deposit for reseller accounts is $100.

### Product General Usage Info

- Only ACTIVE orders can be edited.
- Mobile proxies use port-based pricing. You get unlimited bandwidth, but your access is limited by time (you choose the number of days to access the network).
- Mobile proxies protocols: HTTPS and MOBILE.
- Residential proxies use bandwidth-based pricing. You are charged for bandwidth, but you have unlimited time (no time limit for how long you can access the available bandwidth).
- Residential proxies protocols: HTTPS.

### API General Usage Info

- All requests must be sent via HTTPS
- API Key is included in the **Authorization** header with the format `Token api_token_string`
- **GET** or **POST** method must be specified. URLs for **POST** methods must end with a forward slash **/**
- The API recognizes requests sent only from whitelisted IP addresses (you can provide a maximum of two IP addresses from where the API will be called).
- Whitelisting an IP address is done manually. You can request a change at any time.
- All dates/hours/timestamps are in UTC time.
- API responses are in json format
- The first item of the response is the request status ("OK" or "ERROR").
- "ERROR" responses return as second item the url of the endpoint.
- All POST digit parameters must be sent as **strings of digits** (NOT integers).

**ERROR Response Example**

```
{  "status":"ERROR",
    "url" : "order_details/",
    "message":"unauthorised_request"
}
```

# How to use the API

### Required Headers

All API calls must include the following two headers.

```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
### API Endpoints

1. [GET **get_account_info**](#get_account_info)
2. [GET **geo_proxy_list**](#geo_proxy_list)
3. [GET **carrier_proxy_list**](#carrier_proxy_list)
4. [POST **buy_proxy/**](#buy_proxy-mobile)
5. [POST **get_orders_status/**](#get_orders_status)
6. [POST **order_details/**](#order_details)
7. [POST **extend_order/**](#extend_order)
8. [POST **cancel_order/**](#cancel_order)
9. [POST **update_whitelist_ip/**](#update_whitelist_ip)
10. [POST **change_protocol/**](#change_protocol)
11. [POST **update_rotation/**](#update_rotation)
12. [POST **update_proxy_geo/**](#update_proxy_geo)
13. [POST **reset_proxy_pass/**](#reset_proxy_pass)
14. [POST **view_usage_history/**](#view_usage_history)

---

## get_account_info

Returns the details of your reseller account

**GET** ```https://api.hydraproxy.com/get_account_info```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```

**Response**

```
{
    "status": "OK",
    "email": "vendor@domain.com",
    "balance_usd": 180.0,
    "server_ip": "123.45.67.89"
}
```


## geo_proxy_list

Returns the list of available mobile Ports (grouped by US state)

**GET** ```https://api.hydraproxy.com/geo_proxy_list```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
**Response**

```
{
    "status": "OK",
    "all_geo": [
        {
            "state_name": "Alabama",
            "geo_code": "AL",
            "available_proxy_count": 20,
            "type_proxy_count": {
                "mobile": 10,
                "wifi": 10
            },
            "protocol_proxy_count": {
                "https": 10,
                "socks5": 10
            }
        },
        {
            "state_name": "California",
            "geo_code": "CA",
            "available_proxy_count": 20,
            "type_proxy_count": {
                "mobile": 10,
                "wifi": 10
            },
            "protocol_proxy_count": {
                "https": 10,
                "socks5": 10
            }
        },
}
```


## carrier_proxy_list

Returns the list of dedicated carriers (AT&T, T-Mobile and Verizon)

**GET** ```https://api.hydraproxy.com/carrier_proxy_list```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
**Response**

```
{
    "status": "OK",
    "dedicated_carriers": [
        {
            "status": "OK",
            "carrier": "VERIZON",
            "available_proxy_count": 20,
            "protocol_proxy_count": {
                "https": 10,
                "socks5": 10
            }
        },
        {
            "status": "OK",
            "carrier": "TMOBILE",
            "available_proxy_count": 20,
            "protocol_proxy_count": {
                "https": 10,
                "socks5": 10
            }
        },
        {
            "status": "OK",
            "carrier": "ATT",
            "available_proxy_count": 20,
            "protocol_proxy_count": {
                "https": 10,
                "socks5": 10
            }
        }
    ]
}
```


## buy_proxy/ (MOBILE)

Endpoint used for placing orders for mobile proxies.

Supports only POST requests and data sent as --form, payload {}

VALUES ARE ACCEPTED ONLY IN CAPITAL LETTERS, UNLESS INDICATED OTHERWISE.

**POST** ```https://api.hydraproxy.com/buy_proxy/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------                 | -----------  |
| request_id      | unique_digits     | 1000 to 100000000000                  | Unique ID created from digits. It can be anything, preferably a timestamp.             |
| network         | defined_string    | MOBILE                                | Always specify the network.             |
| connection_type | defined_string    | 4G_MOBILE, WIFI, MIX_4G_WIFI, CARRIER | Dictates what IPs will be allocated for this order.             |
| ports_nr        | digits            | 1 to 25                               | Number of ports for the order. Max 25. Each port gives access to a different mobile IP address.             |
| days_nr         | digits            | 1 to 31                               | Number of access days for your order.             |
| protocol        | defined_string    | HTTPS, SOCKS5                         | Your protocol of choice.             |
| whitelist_ip    | ipv4_ip_address   | IPv4 IP Address                       | The IP address used as authentication for accessing your proxies.             |
| ip_rotation     | defined_bool      | TRUE, FALSE                           | TRUE = 30 min IP rotation. FALSE = extended rotation of up to 6 hours.             |
| geo_code        | defined_string*    | RANDOM or US State Code               | RANDOM for US-wide rotation (in all states) or choose a particular US state. The list of available US mobile proxies by state can be retrived by calling the "/geo_proxy_list" endpoint. **Use the geo_code form of 2 letters in your request.**|
| carrier         | defined_string*    | RANDOM or ATT, TMOBILE, VERIZON       | RANDOM for IPs from all US carriers. (Optional) you can select a carrier and all your IPs will be from this carrier. You can get a list of dedicated carriers by calling the "/carrier_proxy_list" endpoint |
| rotation_premium | defined_string | TRUE, FALSE | [OPTIONAL] This parameter triggers short rotation times and premium order placement (at a cost - please ask support for it) selection of 5min, 10min or standard 30min and extended. By default premium orders get activated with 5min rotation - which you can change immediately after order activation. |

**NOTE**
- You must use all the above data keys when placing an order
- You can select both a particular geo_code and a carrier.
- Once an order is active, you can change your order's location at every 30min.
- Carriers can't be changed after order placement.

**Response**
```
{
    "status": "OK",
    "network": "MOBILE",
    "order_id": 1,
    "order_info": {
        "order_status": "ACTIVE",
        "date_created": "2020-10-29 11:35:12",
        "date_end": "2020-10-30 11:35:12",
        "connection_type": "4G_MOBILE",
        "ports_nr": 1,
        "days_nr": 1,
        "price_usd": 2.35
    },
    "proxy_info": {
        "protocol": "HTTPS",
        "whitelist_ip": "123.45.67.89",
        "geo_code": "RANDOM",
        "carrier": "RANDOM",
        "lock_ip_timeout": "2020-10-29 12:05:12",
        "lock_geo_timeout": "2020-10-29 12:05:12",
        "ip_rotation": "TRUE",
        "ip_rotation_time": "5",
        "premium": TRUE
        
    },
    "proxy": {
        "host": "4g.hydraproxy.com",
        "port": 1111
    }
}
```

## buy_proxy/ (RESIDENTIAL)

Endpoint used for placing orders for residential proxies.

Supports only POST requests and data sent as --form, payload {}

VALUES ARE ACCEPTED ONLY IN CAPITAL LETTERS, UNLESS INDICATED OTHERWISE.

**POST** ```https://api.hydraproxy.com/buy_proxy/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| request_id      | unique_digits     | 1000 to 100000000000    | Unique ID created from digits. It can be anything, preferably a timestamp.             |
| network         | defined_string    | RESIDENTIAL             | Always specify the network. |
| bandwidth       | digits            | 1 to 1000               | The amount of bandwidth in GB you want to use. |

**Response**
```
{
    "status": "OK",
    "network": "RESIDENTIAL",
    "order_id": 20,
    "order_info": {
        "order_status": "ACTIVE",
        "date_created": "2020-10-29 11:55:45",
        "ports_nr": 1,
        "price_usd": 3.0
    },
    "proxy_info": {
        "protocol": "HTTPS",
        "username": "john_doe_ip",
        "password": "PasswordKey",
        "bandwidth_purchased": 1,
        "bandwidth_available": 1.0
    },
    "proxy": {
        "host": "isp2.hydraproxy.com",
        "port": 2222
    }
}
```

## get_orders_status/

Get a list with all your orders filtered by status.

**POST** ```https://api.hydraproxy.com/get_orders_status/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| status          | defined_string    | ACTIVE, CANCELED, PENDING, PROCESSING, NO_FUNDS, TERMINATED | None |

**Response**
```
[
    {
        "id": 10,
        "network_type": "MOBILE",
        "status": "ACTIVE",
        "price_usd": 2.95
    }
]
```

## order_details/

Get details for a particular order

**POST** ```https://api.hydraproxy.com/order_details/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |

**Response for MOBILE proxies**
```
{
    "status": "OK",
    "network": "MOBILE",
    "order_id": 10,
    "order_info": {
        "order_status": "ACTIVE",
        "date_created": "2020-10-29 11:35:21",
        "date_end": "2020-10-30 11:35:12",
        "connection_type": "4G_MOBILE",
        "ports_nr": 1,
        "days_nr": 1,
        "price_usd": 2.95
    },
    "proxy_info": {
        "protocol": "HTTPS",
        "ip_rotation": "TRUE",
        "whitelist_ip": "123.45.67.89",
        "geo_code": "RANDOM",
        "carrier": "RANDOM",
        "lock_ip_timeout": "2020-10-29 12:05:12",
        "lock_geo_timeout": "2020-10-29 12:05:12"
    },
    "proxy": {
        "host": "4g.hydraproxy.com",
        "port": "1111"
    },
    "extensions": {}
}
```

**Response for RESIDENTIAL proxies**
```
{
    "status": "OK",
    "network": "RESIDENTIAL",
    "order_id": 20,
    "order_info": {
        "order_status": "ACTIVE",
        "date_created": "2020-10-29 11:55:45",
        "ports_nr": 1,
        "price_usd": 3.0,
        "initial_bandwidth": 1
    },
    "proxy_info": {
        "protocol": "HTTPS",
        "username": "john_doe_ip",
        "password": "PasswordKey",
        "bandwidth_available": 1
    },
    "proxy": {
        "host": "isp2.hydraproxy.com",
        "port": 2222
    },
    "extensions": {}
}
```


## extend_order/

Extend an order to continue using the access details provided.

**POST** ```https://api.hydraproxy.com/extend_order/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| request_id      | unique_digits     | 1000 to 100000000000    | Unique ID created from digits. It can be anything, preferably a timestamp.             |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |
| bandwidth       | digits*            | 1 to 1000               | The amount of bandwidth in GB you want to use. |
| days_nr         | digits*           | 1 to 31                 | The amount of bandwidth in GB you want to use. |

**NOTE**
- Use the bandwidth or days_nr key depending on the order type (for mobile proxies use days_nr to indicate the number of days to extend the order and for residential proxies the number of additional GB you want to add to your order).
- Don't use both bandwidth and days_nr in a request because it will be rejected.

**Response for MOBILE proxies (days_nr key used)**
```
{
    "status": "OK",
    "order_id": 1,
    "new_date_end": "2020-10-31 11:35:12",
    "price_usd": 2.95
}
```
**Response for RESIDENTIAL proxies (bandwidth key used)**
```
{
    "status": "OK",
    "order_id": 2,
    "bandwidth_added_gb": 1,
    "price_usd": 3.0
}
```

## cancel_order/

Cancel an order.

**NOTE**
- Only 30-31 days mobile proxy orders can be canceled and only in the first 24 hours.
- Order extensions for mobile proxies are not refunded when orders are canceled.
- Canceled residential proxies will return the remaining bandwidth and not the already used data.


**POST** ```https://api.hydraproxy.com/cancel_order/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| request_id      | unique_digits     | 1000 to 100000000000    | Unique ID created from digits. It can be anything, preferably a timestamp.             |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |

**Response**
```
{
    "status": "OK",
    "order_id": 2,
    "cancel_confirmation": "YES",
    "refund_credited_usd": 6.0
}
```

## update_whitelist_ip/

For mobile proxies only. Change the whitelisted IP address of the proxy user. 

Can be changed at minimum 30 minutes intervals.

**POST** ```https://api.hydraproxy.com/update_whitelist_ip/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |
| new_ip          | ipv4_ip_address   | IPv4 IP Address  | The NEW IP address used as authentication for accessing your proxies. |

**Response**
```
{
    "status": "OK",
    "new_whitelist_ip": "52.52.52.52",
    "order_id": 10,
    "lock_ip_timeout": "2020-10-29 13:18:28"
}
```

## change_protocol/

Available only ONCE per order and ONLY for mobile proxy orders. 

Change the protocol of your mobile proxy from HTTPS to SOCKS5 or vice versa.

**POST** ```https://api.hydraproxy.com/change_protocol/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |
| new_protocol    | defined_string    | HTTPS, SOCKS5                         | Your protocol of choice.             |

**Response**
```
{
    "status": "OK",
    "order_id": 10,
    "new_protocol": "SOCKS5"
}
```

## update_rotation/

Change the proxy rotation of your mobile proxy from 30 minutes rotation to extended or vice versa.

**NOTE**
- TRUE for 30 minutes force rotation
- FALSE for extended IP rotation (up to 6 hours). Your proxy will change IP only when the current gateway mobile IP is no longer online.

**POST** ```https://api.hydraproxy.com/update_rotation/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |
| ip_rotation     | defined_bool      | 5, 10, 30, TRUE, FALSE  | TRUE = 30 min IP rotation. FALSE = extended rotation of up to 6 hours. [NOTE: Premium orders can select shorter rotations as 5min and 30min.] |

**Response for FALSE (extended rotation)**
```
{
    "status": "OK",
    "order_id": 10,
    "ip_rotation": "FALSE",
    "next_ip_rotation": ""
}
```
**Response for TRUE (when 30 minutes rotation is selected)**
```
{
    "status": "OK",
    "order_id": 1,
    "ip_rotation": "TRUE",
    "next_ip_rotation": "2020-10-29 12:23:46"
}
```

## update_proxy_geo/

Change the proxy geo (US State) of your mobile proxies.

**NOTE**
- This endpoint can be called at minimum 30 minutes intervals.
- Use the geo_code for your preferred state. You can get a list of all available
- A proxy with RANDOM geo_code proxies can change the state and get IPs from a particular state, but a proxy with a particular geo_code (eg FL or CA) can't be reversed back to 

**POST** ```https://api.hydraproxy.com/update_proxy_geo/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX                 | Insert the ID of the order for which you want to retrieve the details. |
| new_geo_code    | defined_string*   | Any US State Code       | RANDOM for US-wide rotation (in all states) or choose a particular US state. The list of available US mobile proxies by state can be retrived by calling the "/geo_proxy_list" endpoint. **Use the geo_code form of 2 letters in your request.**|

**Response**
```
{
    "status": "OK",
    "new_geo_code": "FL",
    "lock_geo_timeout": "2020-10-29 13:15:48"
}
```

## reset_proxy_pass/

For residential proxies only. Change the authorization password used by the user to access the proxy network.


**POST** ```https://api.hydraproxy.com/reset_proxy_pass/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |

**Response**
```
{
    "status": "OK",
    "order_id": 20,
    "proxy_info": {
        "username": "john_doe_ip",
        "password": "New_PasswordKey2"
    }
}
```

## view_usage_history/

For residential proxies only. Retrieve the historical bandwidth usage in MB per day with a 1 to 2% tolerance.

**NOTE 1**: For current day usage history there is a delay in displaying the exact usage. Final usage for the day can be retrived at the end of day (UTC time).

**NOTE 2**: The History usage won’t be available for all the data set. An order's old history gets deleted to avoid large API responses, so you'll probably need to save it in the database.

**POST** ```https://api.hydraproxy.com/view_usage_history/```

*Headers*
```
'Accept: application/json'
'Authorization: Token extended_api_token_string'
```
*Accepted key:value Data*

| Key (low caps)  | Value Type (CAPS or digits)   | Options     | Description  |
| -------         | -------           | ---------------------   | -----------  |
| order_id        | digits            | 1 to XX | Insert the ID of the order for which you want to retrieve the details. |

**Response for unused bandwidth**
```
{
    "status": "OK",
    "order_id": 22,
    "bandwidth_history": null
}
```

**Response for used bandwidth**
```
{
    "status": "OK",
    "order_id": 90,
    "bandwidth_history": {
        "2020-11-06 00:00:00": {
            "bandwidth_upload_mb": 75.16,
            "bandwidth_download_mb": 702.76
        },
        "2020-10-30 00:00:00": {
            "bandwidth_upload_mb": 17.08,
            "bandwidth_download_mb": 191.12
        },
        "2020-10-28 00:00:00": {
            "bandwidth_upload_mb": 20.94,
            "bandwidth_download_mb": 238.54
        }
    }
}
```
