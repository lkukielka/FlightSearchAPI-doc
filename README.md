# FlightSearchAPI
##### Free API for flexible flights search engine. 
If you want to get the apiKey or have any questions let me know at leskukie[at]gmail[dot]com

## _Already supported airlines_


[![N|Solid](https://i.ibb.co/YLbtxMB/Ryanair-Logo-2013-present-2-1.jpg)](https://www.ryanair.com/gb/en)
[![N|Solid](https://i.ibb.co/N97JGnb/Wizz-Air-logo-svg.png)](https://www.wizzair.com/)



## Authentication
| Security Scheme Type | Name|
| ------ | ------ |
| Url parameter | apiKey |

## Url params for both requests
Request url params:
| Name | Type| Required | Default| Description|
| ------ | ------ | ------ | ------ | ------ |
| apiKey| string | true | - | authentication token, you can get it by sending me mail at leskukie[at]gmail[dot]com|
| page| integer | false | 0 | page number|
| size| integer | false | 20 | page size|
| sortBy| string | false | priceInEuro | result's sort field, possible values: _priceInEuro, date, departure, destination_|
| order| string | false | asc | result's sort order, possible values: _asc, desc_|


# **Search for one way flights**
Search for one-way flights in given city's iata code combinations and dates
### POST <api_addres>/v1/flights/oneway
Request body:
| Name | Type| Required |  Description|
| ------ | ------ | ------ |  ------ |
| departure| string list | true | departure airports iata codes, for example LTN or CFU |
| destination| string list | false | destination airport iata codes, like above. if empty api returns flights to all possible destinations|
| endDate| string | true | earliest possible flight date in yyyy-MM-dd format|
| startDate| string | true | latest possible flights date in yyyy-MM-dd format |

Example request body:
```json
{
	"departure":["ACE"],
	"destination":["AGP", "VCE", "NOC"],
	"startDate": "2023-02-21",
	"endDate": "2023-03-30"
}
```

Response if scuccess:
| Name | Type| Description|
| ------ | ------ |  ------ |
| flights| list of flights (described below) | list with flights details |
| last| boolean | if current page is the last one|
| totalFlights| integer | total flights found|
| page| integer | page number |

Flight type: 
| Name | Type| Description|
| ------ | ------ |  ------ |
| updatedAt| date  | flight details last update date |
| departure| Airport | departure airport details|
| destination| Airport | destination airport details|
| priceInEuro | float | flight price  |
| airline | string | airline name  |

Example response: 
```json
{
	"flights": [
		{
			"updatedAt": "2023-02-22",
			"date": "2023-03-17",
			"departure": {
				"iata": "ACE",
				"name": "Lanzarote Airport",
				"city": "Lanzarote Island",
				"country": "ES",
				"tz": "Atlantic/Canary",
				"lat": 28.9455,
				"lon": -13.6052
			},
			"destination": {
				"iata": "AGP",
				"name": "Malaga Airport",
				"city": "Malaga",
				"country": "ES",
				"tz": "Europe/Madrid",
				"lat": 36.6749,
				"lon": -4.49911
			},
			"priceInEuro": 9.99,
			"airline": "RYANAIR"
		}]
	"last": true,
	"totalFlights": 1,
	"page": 0
}
```

# **Search for round flights**
Search for round flights in given city's iata code combinations and dates. Be aware of flexible field value. If false, engine will search only revert flights for the same two airports like in found flight in first way. If true, it will search for flights from found destination to all airports from departure list. Example request:
```
destination: [A,B] -> departure [X,Y]
```
* _flexible: false_
if found first way flight has connection A -> X then engine will search for return flights X -> A
* _flexible: true_
if found first way flight has connection A -> X then engine will search for return flights X -> A and X -> B
### POST <api_addres>/v1/flights/round
Request body:
| Name | Type| Required |  Description|
| ------ | ------ | ------ |  ------ |
| departure| string list | true | departure airports iata codes, for example LTN or CFU |
| destination| string list | false | destination airport iata codes, like above. if empty api returns flights to all possible destinations|
| endDate| string | true | earliest possible flight date in yyyy-MM-dd format|
| startDate| string | true | latest possible flights date in yyyy-MM-dd format |
| minDays| integer | true | mininimum trip duration in days|
| maxDays| integer | true | maximum trip duration in days |
| flexible| boolean | true | if flexible search mode turn on |

Example request body:
```json
{
	"departure":["ACE"],
	"destination":["AGP", "VCE", "NOC"],
	"startDate": "2023-02-21",
	"endDate": "2023-03-30",
	"minDays":2,
	"maxDays":5,
	"flexible": true
}
```
Response if scuccess:
| Name | Type| Description|
| ------ | ------ |  ------ |
| flights| list of round flights (described below) | list with flights details |
| last| boolean | is current page the last one|
| totalFlights| integer | total flights found|
| page| integer | page number |

Round flight type:
| Name | Type| Description|
| ------ | ------ |  ------ |
| firstFlight| Flight  | first flight details described in one way section |
| returnFlight | Flight | round flight details described in one way section|

Example response:
```json
{
	"flights": [
		{
			"firstFlight": {
				"updatedAt": "2023-02-22",
				"date": "2023-03-17",
				"departure": {
					"iata": "ACE",
					"name": "Lanzarote Airport",
					"city": "Lanzarote Island",
					"country": "ES",
					"tz": "Atlantic/Canary",
					"lat": 28.9455,
					"lon": -13.6052
				},
				"destination": {
					"iata": "AGP",
					"name": "Malaga Airport",
					"city": "Malaga",
					"country": "ES",
					"tz": "Europe/Madrid",
					"lat": 36.6749,
					"lon": -4.49911
				},
				"priceInEuro": 9.99,
				"airline": "RYANAIR"
			},
			"returnFlight": {
				"updatedAt": "2023-02-22",
				"date": "2023-03-20",
				"departure": {
					"iata": "AGP",
					"name": "Malaga Airport",
					"city": "Malaga",
					"country": "ES",
					"tz": "Europe/Madrid",
					"lat": 36.6749,
					"lon": -4.49911
				},
				"destination": {
					"iata": "ACE",
					"name": "Lanzarote Airport",
					"city": "Lanzarote Island",
					"country": "ES",
					"tz": "Atlantic/Canary",
					"lat": 28.9455,
					"lon": -13.6052
				},
				"priceInEuro": 9.99,
				"airline": "RYANAIR"
			},
			"totalPriceInEuro": 19.98
		}
	],
	"last": true,
	"totalFlights": 13,
	"page": 0
}
```
