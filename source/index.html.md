---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

# includes:
#   - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for World Bank PPP Project
---

# Introduction

Welcome to the documentation for the current version of the Purchasing Power Parity matching algorithm.

We have currently have language support for Python only. You can view code examples in the dark area to the right. Once supported, you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/slatedocs/slate). Feel free to edit it and use it as a base for your own API's documentation.

<!-- Joey's  Parsing and Similarity Documentation-->
# Normalization / Similarities

## get_data
> To get data from DynamoDB, use this code:

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

```python

from normalize_test import get_data

 # see get_data function description on choosing a valid query/category :)
query = 'beef'
category = 'meat'
# query API
data = get_data(query, category)
```

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

<!-- > This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams. -->

Returns a dictionary containing data from DynamoDB. The function requires a
query string, as well as a category. Ideally these should be an index and sort
key for our database.


#### PARAMETERS

`query -> str:` a product to query the datbase for 

`category -> str:` the category that the query string belongs to 
       
#### OUTPUT

Returns a dictionary containing data from DynamoDB.

<!-- -------------------------------- -->

## normalize_data
> To normalize Dynamo data, use this code:

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

```python

from normalize_test import normalize_data

# normalize our API data
frames = normalize_data(data)
```

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

<!-- > This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams. -->

A basic normalization of JSON product data.
It may need to be fine-tuned to grab deeper-nested data for some attributes.


#### PARAMETERS

`data dict:` data from DynamoDb converted from json -> dictionary 
       
#### OUTPUT

Returns an array of dictionaries, or an empty array if an error occurs.
Each dictionary item contains a 'store' key and a 'frame' key,
which store is the location data was taken from and frame is a pandas data frame.
        
<aside class="notice">
May need to be fine-tuned to grab deeper-nested data for some attributes.
</aside>
<!-- -------------------------------- -->

## process_frames
> To process normalized data in dataframes, use this code:

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

```python

from normalize_test import process_frames

# grab columns from databse matching `keyword`
headers = process_frames(frames, keyword)
```

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

<!-- > This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams. -->

This function processes a pandas dataframe and creates a dictionary. The dictionary contain
the store's name as the index, and contains an array value sorted by the percentage in similarity
(high to low) between column attributes and product description.


#### PARAMETERS

`frames -> dataframe:` the normalized data as a dataframe

`keyword -> str:` item to match on either "description" or "images" 
       
#### OUTPUT


Returns an array of json objects containing a store, column, and match percent
key.
        
Example response:
    {'walmart' : [{'column_name' : '<info product description>'}, ... ]}

<!--     
<aside class="notice">
You must run driver to get the dictionary containing a list of descriptions before doing bag of words.
</aside> -->
<!-- -------------------------------- -->

## match_description
> Currently an internally facing method

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

<!-- > This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams. -->

This function takes a string and calculates the similarity between it and
all strings in the matching_strings array.


#### PARAMETERS

`column_name -> str:` dataframe column to try to match with 
    ex: column_name = info primary_brand name  

`keyword -> str:` item to match on either "description" or "images" 
       
#### OUTPUT


This function returns a dictionary with
the following keys:
    'column_name': the name of the column
    'similarity': the average similarity between the provided string and strings
                  in the matching_strings array.
        
   
<aside class="notice">
As we see more attributes from various venders, we can add to the matching_strings array to 
provide reliable results over time.
</aside> 
<!-- -------------------------------- -->

## grab_product_descriptions
> To find a list of product descriptions, use this code:

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

```python

from normalize_test import match_description

# get product data
descriptions = grab_product_descriptions(frames, headers)
if len(descriptions) > 0:
    # format for printing
    formatted_descriptions = {query: []}
    for key in descriptions:
        formatted_descriptions[query].append(descriptions[key].values)

print(formatted_descriptions)
```

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

<!-- > This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams. -->

Formats a dataframe of product descriptions


#### PARAMETERS

`frames -> dataframe:` dataframe containing Dynamodb descriptions

`headers -> list:` DynamoDB headers used to index the DB.
       
#### OUTPUT


Returns a dictionary containing:
    'store name' : [{product description}, ...],
    where store name is Walmart, Target, etc...
    and product description is a pandas dataframe
        

<!-- -------------------------------- -->
## driver
> To use all previous methods and output a dictionary of {query : descriptions}, use this code:

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

```python

from normalize_test import driver

products = driver('description')

```

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

<!-- > This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams. -->

Formats a dataframe of product descriptions


#### PARAMETERS

`keyword -> str:` item to find either description or images
       
#### OUTPUT

Returns a dictionary containing:
    {"query" : [list of descriptions]}

<aside class="success">
This is the easiest way to get a formatted list of query descriptions or images.
</aside>


<!-- Michaels Bag of Words Documentation -->
# Bag of Word Analysis 

## get_categories
> To perform a bag of words analysis, use this code:

<!-- ```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
``` -->

```python

from normalize_test import driver
from bag_of_words import get_categories

# get a dictionary of products and a list of corresponding descriptions
products = driver("description")

# bag of words results as either word, word pair, or trigram counts
results = get_categories(products, 1, "df")
results2 = get_categories(products, 3, "list", 5)
```

<!-- ```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
``` -->

> This will result in a dataframe and list of the top 3 and 5 most frequent words and trigrams.

This function performs a bag of words analysis of the list of descriptions
  for a given query. Bag of words is a representation of of text as the
  frequency for which each word appears.


#### PARAMETERS

`descriptions -> dict:` of which the key is the query and value is an array of product descriptions

`n_words -> int]:` 1, 2, or 3 representing single words, word pairs, or trigrams

`return_type -> df | list`: specification of output form as either a dataframe or list

`n_results -> int:` number of counts to return

       
#### OUTPUT

The n_results most frequent words, word pairs, or trigrams as either a
dataframe or array. 
        
<aside class="warning">
You must run driver to get the dictionary containing a list of descriptions before doing bag of words.
</aside>

<!-- # Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete -->

