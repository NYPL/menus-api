#Menus Beta API v1

##What Is This?
There's a lot of data behind The New York Public Library's [What's On The Menu?](http://menus.nypl.org), and here's your chance to explore it. This is built for programmers and power-users, so if you're looking for an easier way to explore the dataset you may want to take a look at our [Biweekly data exports](http://menus.nypl.org/data), which are in CSV format. However if you're looking to do powerful analysis of historical menu data, this is the tool for you.

If you haven't worked with an API before, this [REST API tutorial](http://www.restapitutorial.com/) is a great place to start. 

To *pretty print* your responses, take a look at the [jutil](https://github.com/misterfifths/jutil) repository or pipe (|) in *python -mjson.tool*:

```bash
echo '{"json":"obj"}' | python -mjson.tool
```

This is our first public API, so if you have any questions, please [get in touch](mailto:menus@nypl.org) with us; it'll help us as much as you. 

##Caveats About the Data
####There's a few things you should know about the data before you dive in:

You may notice empty/null fields for certain items. Much of the information for **dishes** and **menus**, to some extent, are still being normalized, cleaned, and updated.

Currently, *highest* and *lowest* prices for **dishes** don't take into account currencies other than the dollar.

This data structure is still being worked on and changes will be taking shape with both the Menus [application](http://menus.nypl.org) and the API. We will update this documentation and the [Menus data page](http://menus.nypl.org/data) before these changes and/or new versions go into effect.

If you're interested in cleaning up the data--by hand, with Google Refine, programmatically--[please send an email](mailto:menus@nypl.org) with the subject heading **I want to help NYPL better their dataset**. 


##Tokens
For this initial, beta release, please email [menusATnyplDOT.org](mailto:menus@nypl.org) with the subject heading **API ACCESS** in order to get a token to use the service. Your token will be a 26-character string, something like *flillduynijyk2u6zemo36mcg4*. In the email, please also give us a little background about yourself and what you plan to use the service for.

Eventually, there will be a sign-up site for obtaining tokens and getting updates on the service.

##Formats
**JSON** is the default and recommended response format for the service. You can obtain the response in **XML** by appending .xml. to the call.

####example curl

```bash
curl "http://api.menus.nypl.org/menus/12848.xml?token={YOUR_API_TOKEN}"
```

####example xml response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<menu>
  <id type="integer">12848</id>
  <name></name>
  <sponsor>HOTEL JERMYN</sponsor>
  <venue>COMMERCIAL</venue>
  <event>DINNER</event>
  <place>SCRANTON, PA</place>
  <occasion></occasion>
  <physical-description>CARD; 5.25 X 6.75;</physical-description>
  <notes></notes>
  <call-number>1900-2430</call-number>
  <keywords nil="true"></keywords>
  <language nil="true"></language>
  <date type="date">1900-03-14</date>
  <status>complete</status>
  <currency nil="true"></currency>
  <currency-symbol nil="true"></currency-symbol>
  <location>Hotel Jermyn</location>
  <location-type nil="true"></location-type>
  <page-count type="integer">2</page-count>
  <dish-count type="integer">60</dish-count>
  <thumbnail-src>http://images.nypl.org/index.php?id=466635&amp;t=r</thumbnail-src>
  <large-src>http://images.nypl.org/index.php?id=466635&amp;t=w</large-src>
  <first-page-aspect>portrait</first-page-aspect>
  <first-page-full-width type="integer">2215</first-page-full-width>
  <first-page-full-height type="integer">2887</first-page-full-height>
  <links type="array">
    <link>
      <href>http://api.menus.nypl.org/menus</href>
      <rel>index</rel>
    </link>
    <link>
      <href>http://api.menus.nypl.org/menus/12848/pages</href>
      <rel>pages</rel>
    </link>
    <link>
      <href>http://api.menus.nypl.org/menus/12848/dishes</href>
      <rel>dishes</rel>
    </link>
  </links>
</menu>
```


##Rate Limiting

We limit requests to **5000 per day** either per token or per IP address and to **2 requests per second**. You can check the returned HTTP headers of any API request to see your current status:

```bash
curl -i "http://api.menus.nypl.org/dishes/13?token={YOUR_API_TOKEN}"
HTTP/1.1 200 OK 
Content-Type: application/json
X-Ratelimit-Limit: 5000
X-Ratelimit-Remaining: 4932
```

*X-Ratelimit-Remaining* is determined by the number of requests left on your API token and decremented on all request, whatever the response may be.

##Authentication

####Basic Auth

*Details forthcoming*

####Oauth 2

*Details forthcoming*

##Pagination
Pagination Info is Contained in the Link **Header** for Indexes

**Notes**

- *rel* can refer to **first**, **prev**, **next**, and **last** pages
- *per_page* limit for all Menu objects is **50**; you can always get less
- *per_page* limit for all Dish objects is **100**; you can always get less
- if you attempt to access a page beyond the **last** page, you'll be returned an empty array of objects

####example curl

```bash
curl -i "http://api.menus.nypl.org/dishes?page=3&token={YOUR_API_TOKEN}"
```

####example Link Header

```bash	 
<http://api.menus.nypl.org/dishes?sort_by=date&per_page=100&page=1>; rel="first", 
<http://api.menus.nypl.org/dishes?sort_by=date&per_page=100&page=2>; rel="prev", 
<http://api.menus.nypl.org/dishes?sort_by=date&per_page=100&page=4>; rel="next", 
<http://api.menus.nypl.org/dishes?sort_by=date&per_page=100&page=3219>; rel="last"
```

For examples on how to parse the **link header**, [this gist](https://gist.github.com/2946021) showcases a javascript method (**parseLinkHeader**) and [this file](https://github.com/eclipse/egit-github/blob/master/org.eclipse.egit.github.core/src/org/eclipse/egit/github/core/client/PageLinks.java#L43-75) is a java version, written to be used with the Github API, which is where this pagination concept comes from.

##Menus

###GET /menus
 
```bash
$ curl "http://api.menus.nypl.org/menus?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```
	
####possible query parameters

```javascript	
min_year: "?min_year=1947"
max_year: "?max_year=1950" 
sort_by: "?sort_by=date" || "?sort_by=name" || "?sort_by=dish_count"
status: "?status=under_review" || "?status=complete" || "?status=to_transcribe"
```
	
**Notes**

- *stats* refers to top-level counts/info on the object
- *min_year* and *max_year* act as a date range for menus
- *min_year* is included in the range, *max_year* is not
- *sort_by* defaults to **date**; *?sort_by=date* returns objects from **most recent to oldest**
- *status* takes on different terms than those on [menus.nypl.org](http://menus.nypl.org) due to how they're actually best designated in the data structure
- *status* **to_transcribe** is a sum of menus that are in the state of **new** and **in progress**
- *first_page_full_height* and *first_page_full_width* are in pixels

####example response

```json
{
	"stats": {
        "count": 14450,
        "earliest_menu": 1851,
        "latest_menu": 2012,
        "to_transcribe": 38,
        "under_review": 6418,
        "complete": 7994
    },
    "menus": [
        {
            "id": 27228,
            "name": null,
            "sponsor": "Hotel Del Monte",
            "venue": "HOTEL",
            "event": "dinner",
            "place": "Hotel Del Monte",
            "occasion": "PATRIOTIC HOLIDAY",
            "physical_description": "9 1/2 x 6 in. ; col. ill.",
            "notes": "",
            "call_number": "Soete 50",
            "keywords": null,
            "language": null,
            "date": "2012-07-04",
            "status": "complete",
            "currency": null,
            "currency_symbol": null,
            "location": "Hotel Del Monte",
            "location_type": null,
            "page_count": 5,
            "dish_count": 51,
            "thumbnail_src": "http://images.nypl.org/index.php?id=3884434&t=r",
            "large_src": "http://images.nypl.org/index.php?id=3884434&t=w",
            "first_page_aspect": "portrait",
            "first_page_full_width": 2959,
            "first_page_full_height": 4978,
            "links": [
                {
                    "href": "http://api.menus.nypl.org/menus/27228",
                    "rel": "self"
                },
                {
                    "href": "http://api.menus.nypl.org/menus/27228/pages",
                    "rel": "pages"
                },
                {
                    "href": "http://api.menus.nypl.org/menus/27228/dishes",
                    "rel": "dishes"
                }
            ]
        },
        {
        	…
        }
	]
}
```
		
###GET /menus/{id}

```bash
$ curl "http://api.menus.nypl.org/menus/31729?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```
	
**Notes**

- *first_page_full_height* and *first_page_full_width* are in pixels
	
####example response

```json
{
	"id": 31729,
    "name": null,
    "sponsor": "Le Versailles",
    "venue": null,
    "event": null,
    "place": null,
    "occasion": null,
    "physical_description": "35.5x29.5cm",
    "notes": "  ",
    "call_number": "1920-0071_wotm",
    "keywords": null,
    "language": null,
    "date": "1920-06-14",
    "status": "under review",
    "currency": "Francs",
    "currency_symbol": "FF",
    "location": "Le Versailles",
    "location_type": null,
    "page_count": 2,
    "dish_count": 168,
    "thumbnail_src": "http://images.nypl.org/index.php?id=4029915&t=r",
    "large_src": "http://images.nypl.org/index.php?id=4029915&t=w",
    "first_page_aspect": "portrait",
    "first_page_full_width": 3911,
    "first_page_full_height": 4686,
    "links": [
        {
            "href": "http://api.menus.nypl.org/menus",
            "rel": "index"
        },
        {
            "href": "http://api.menus.nypl.org/menus/31729/pages",
            "rel": "pages"
        },
        {
            "href": "http://api.menus.nypl.org/menus/31729/dishes",
            "rel": "dishes"
        }
    ]
}
```

###GET /menus/{id}/pages

```bash
$ curl "http://api.menus.nypl.org/menus/31729/pages?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```
	
**Notes**

- *full_height* and *full_width* are in pixels
- *uuid* is attributed to NYPL Image Repository
- *price* and *high_price* are strings of **float** values if they exist. Price normalization is a future step for the project. The price type can be determined from the *currency* of the Menu object
- *created_at* and *updated_at* refer to when that specific transcription occurred in UTC
	
####example response 

```json
{
	"links": [
        {
            "href": "http://api.menus.nypl.org/menus/31729",
            "rel": "parent"
        },
        {
            "href": "http://api.menus.nypl.org/menus",
            "rel": "index"
        }
    ],
    "pages": [
        {
            "id": 64836,
            "page_number": 1,
            "image_id": "4029915",
            "full_height": 4686,
            "full_width": 3911,
            "uuid": "bf8b24be-cb02-4876-e040-e00a1806540b",
            "aspect": "portrait",
            "small_src": "http://images.nypl.org/index.php?id=4029915&t=t",
            "thumbnail_src": "http://images.nypl.org/index.php?id=4029915&t=r",
            "large_src": "http://images.nypl.org/index.php?id=4029915&t=w",
            "dishes": [
                {
					"name": "Eaux minerales la bout",
                    "price": "5.00",
                    "high_price": null,
                    "xpos": 0.642667,
                    "ypos": 0.0990408,
                    "created_at": "2012-07-03T02:51:59Z",
                    "updated_at": "2012-07-03T20:28:37Z",
                    "links": [
                        {
                        	"href": "http://api.menus.nypl.org/dishes/375814",
                            "rel": "self"
                        }
                    ]
                },
                {
               		…
                }
			]
		},
		{
			…
		}
	]
}
```
	
###GET /menus/{id}/dishes

```bash
$ curl "http://api.menus.nypl.org/menus/31729/dishes?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```

**Notes**

- *price* and *high_price* are strings of **float** values if they exist. Price normalization is a future step for the project. The price type can be determined from the *currency* of the Menu object
- *created_at* and *updated_at* refer to when that specific transcription occurred in UTC
	
####example response

```json
{	
	"links": [
        {
            "href": "http://api.menus.nypl.org/menus/31729", 
            "rel": "parent"
        }, 
        {
            "href": "http://api.menus.nypl.org/menus", 
            "rel": "index"
        }
    ],
	"dishes": [
        {
            "name": "Couvert",
            "price": null,
            "high_price": null,
            "xpos": 0.542667,
            "ypos": 0.0356102,
            "created_at": "2012-07-03T02:51:59Z",
          	"updated_at": "2012-07-03T20:28:37Z",
            "links": [
                {
                    "href": "http://api.menus.nypl.org/dishes/139476",
                    "rel": "self"
                }
            ]
        },
 		{
 			…
 		}
	]
}
```

###GET /menus/search

```bash
$ curl "http://api.menus.nypl.org/menus/search?query=dinner&token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
query: "?query={YOUR_SEARCH}"
```

####possible query parameters

```javascript		
sort_by: "?sort_by=date" || "?sort_by=name" || "?sort_by=relevance" || "?sort_by=dish_count"
```

**Notes**

- *stats - count* refers to the number of results in the search
- *first_page_full_height* and *first_page_full_width* are in pixels
- *sort_by* defaults to **relevance**
	
####example response

```json
{
	"stats": {
        "count": 4322
    },
    "menus": [
        {
            "id": 29387,
            "name": "",
            "sponsor": "Dinner",
            "venue": null,
            "event": "Dinner",
            "place": null,
            "occasion": null,
            "physical_description": "29.5x24cm folded; 29.5x47cm open",
            "notes": "",
            "call_number": "1998-0009_wotm",
            "keywords": null,
            "language": null,
            "date": "1998-01-01",
            "status": "under review",
            "location": "Dinner",
            "location_type": null,
            "currency": "Dollars",
            "currency_symbol": "$",
            "page_count": 4,
            "dish_count": 102,
            "thumbnail_src": "http://images.nypl.org/index.php?id=3953786&t=r",
            "large_src": "http://images.nypl.org/index.php?id=3953786&t=w",
            "first_page_aspect": "portrait",
            "first_page_full_width": 4200,
            "first_page_full_height": 5186,
            "links": [
                {
                    "href": "http://api.menus.nypl.org/menus/29387",
                    "rel": "self"
                },
                {
                    "href": "http://api.menus.nypl.org/menus/29387/pages",
                    "rel": "pages"
                },
                {
                    "href": "http://api.menus.nypl.org/menus/29387/dishes",
                    "rel": "dishes"
                }
            ]
        },
        {
        	…
        }
	]
}
```


##Dishes

###GET /dishes 

```bash
$ curl "http://api.menus.nypl.org/dishes?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```
	
####possible query parameters

```javascript	
min_year: "?min_year=1947"
max_year: "?max_year=1950" 
sort_by: "?sort_by=name" || "?sort_by=date" || "?sort_by=popularity" || "?sort_by=obscurity"
```  
	
**Notes**

- *stats* refers to top-level counts/info on the object
- *min_year* and *max_year* act as a date range for menus
- *sort_by* defaults to **date**; *?sort_by=date* returns objects from **most recent to oldest**
- *lowest_price* and *highest_price* are strings of **decimal** values if they exist and if they related to the dollar currency. Price normalization and multi-currency-comparison are future steps for the project. Non-dollar based dishes will be null. Honestly, the field is not very useful, as of yet.
- if *first_appeared* or *last_appeared* return **null**, then there is no year attached, either due to the associated menu not containing a date/year or that the dish item has not been updated yet. This is another aspect we're working on.

####example response

```json
{
	"stats": {
        "count": 323958
    },
    "dishes": [
        {
            "id": 351645,
            "name": "Apple cake with hazelnuts and ricotta cheese",
            "description": null,
            "menus_appeared": 1,
            "times_appeared": 1,
            "first_appeared": 2012,
            "last_appeared": 2012,
            "lowest_price": null,
            "highest_price": null,
            "links": [
                {
                    "href": "http://api.menus.nypl.org/dishes/351645",
                    "rel": "self"
                },
                {
                    "href": "http://api.menus.nypl.org/dishes/351645/menus",
                    "rel": "menus"
                }
            ]
        },
        {
        	…
        }
	]
}
```

###GET /dishes/{id}

```bash
$ curl "http://api.menus.nypl.org/dishes/329313?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```
	
**Notes**

- *lowest_price* and *highest_price* are strings of **decimal** values if they exist and if they related to the dollar currency. Price normalization and multi-currency-comparison are future steps for the project. Non-dollar based dishes will be null. Honestly, the field is not very useful, as of yet
- if *first_appeared* or *last_appeared* return **null**, then there is no year attached, either due to the associated menu not containing a date/year or that the dish item has not been updated yet. This is another aspect we're working on.

####example response

```json	
{
	"id": 329313,
    "name": "AA Milk (bottle)",
    "description": null,
    "menus_appeared": 1,
    "times_appeared": 1,
    "first_appeared": 1940,
    "last_appeared": 1940,
    "lowest_price": "$0.10",
    "highest_price": "$0.10",
    "links": [
        {
            "href": "http://api.menus.nypl.org/dishes",
            "rel": "index"
        },
        {
            "href": "http://api.menus.nypl.org/dishes/329313/menus",
            "rel": "menus"
        }
    ]
}
```

###GET /dishes/{id}/menus

```bash
$ curl "http://api.menus.nypl.org/dishes/195/menus?token={YOUR_API_TOKEN}"
```
	
####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
```
	
**Notes**

- *first_page_full_height* and *first_page_full_width* are in pixels


####example response	

```json
{
	"links": [
        {
            "href": "http://api.menus.nypl.org/dishes/195",
            "rel": "parent"
        },
        {
            "href": "http://api.menus.nypl.org/dishes",
            "rel": "index"
        }
    ],
	"menus": [
        {
            "id": 16674,
            "name": "",
            "sponsor": "NIPPON YUSEN KAISHA",
            "venue": "COMMERCIAL",
            "event": "BREAKFAST",
            "place": "ABOARD SS KAMAKURA MARU",
            "occasion": "",
            "physical_description": "BROADSIDE; 5.5X7.75;",
            "notes": "FIRST CLASS BILL OF FARE; HAND WRITTEN;",
            "call_number": "1900-4088",
            "keywords": null,
            "language": null,
            "date": "1900-08-19",
            "status": "complete",
            "currency": null,
            "currency_symbol": null,
            "location": "Nippon Yusen Kaisha",
            "location_type": null,
            "page_count": 2,
            "dish_count": 13,
            "thumbnail_src": "http://images.nypl.org/index.php?id=468654&t=r",
            "large_src": "http://images.nypl.org/index.php?id=468654&t=w",
            "first_page_aspect": "portrait",
            "first_page_full_width": 2321,
            "first_page_full_height": 3233,
            "links": [
                {
                    "href": "http://api.menus.nypl.org/menus/16674",
                    "rel": "self"
                }
            ]
        },
        {
        	…
        }
	] 
}
```

###GET /dishes/search

```bash
$ curl "http://api.menus.nypl.org/dishes/search?query=knish&token={YOUR_API_TOKEN}"
```

####required query parameters

```javascript	
token: "?token={YOUR_API_TOKEN}"
query: "?query={YOUR_SEARCH}"
```

####possible query parameters

```javascript		
sort_by: "?sort_by=date" || "?sort_by=name" || "?sort_by=relevance" || "?sort_by=popularity" || "?sort_by=obscurity"
```

**Notes**

- *stats - count* refers to the number of results in the search
- *lowest_price* and *highest_price* are strings of **decimal** values if they exist and if they related to the dollar currency. Price normalization and multi-currency-comparison are future steps for the project. Non-dollar based dishes will be null. Honestly, the field is not very useful, as of yet
- if *first_appeared* or *last_appeared* return **null**, then there is no year attached, either due to the associated menu not containing a date/year or that the dish item has not been updated yet. This is another aspect we're working on.

####example response

```json
{
	"stats": {
        "count": 8
    },
    "dishes": [
        {
            "id": 153405,
            "name": "Hot Deli-Variety Plate (serves two) kishke, knish, cabbage roll, kreplach",
            "description": null,
            "menus_appeared": 1,
            "times_appeared": 1,
            "first_appeared": null,
            "last_appeared": null,
            "lowest_price": null,
            "highest_price": null,
            "links": [
                {
                    "href": "http://api.menus.nypl.org/dishes/153405",
                    "rel": "self"
                },
                {
                    "href": "http://api.menus.nypl.org/dishes/153405/menus",
                    "rel": "menus"
                }
            ]
        },
        {
        	…
        }
	]
}
```
