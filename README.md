# autocomplete-sample
Autocomplete using elasticsearch


## Steps.

#### 1.. Create a custom analyzer.

' PUT /bank
{
   "settings": {
      "analysis": {
         "filter": {
            "autocomplete_filter": {
               "type": "edge_ngram",
               "min_gram": 1,
               "max_gram": 10
            }
         },
         "analyzer": {
            "autocomplete": {
               "type": "custom",
               "tokenizer": "standard",
               "filter": [
                  "lowercase",
                  "autocomplete_filter"
               ]
            }
         }
      }
   }
} '

#### 2.. Test the custom analyzer

'GET /bank/_analyze?analyzer=autocomplete&text='swapnil pande' '


#### 3... Create custom mapping

'PUT bank/account/_mapping
{
   "account": {
      "properties": {
         "account_number": {
            "type": "long"
         },
         "address": {
            "type": "string"
         },
         "age": {
            "type": "long"
         },
         "balance": {
            "type": "long"
         },
         "city": {
            "type": "string"
         },
         "email": {
            "type": "string"
         },
         "employer": {
            "type": "string"
         },
         "firstname": {
            "fields": {
               "autocomplete": {
                  "type": "string",
                  "index_analyzer": "autocomplete",
                  "search_analyzer": "standard"
               },
               "exact": {
                  "type": "string",
                  "index": "not_analyzed"
               }
            },
            "type": "multi_field"
         },
         "gender": {
            "type": "string"
         },
         "lastname": {
            "type": "string"
         },
         "state": {
            "type": "string"
         }
      }
   }
}'


#### 4 .... Insert the json file into ES

'  curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json" '


#### 5 .... Hit the instance with the query..

'GET /bank/account/_search
{
    "query": {
        "term": {
            "firstname.autocomplete": "s"
        }
    },
    "aggs":{
        "autoResults":{
            "terms":{
                "field":
                    "firstname.autocomplete",
                "size":5
            }
        }
    }
}'
