# ElasticSearch
* [Run database](###run-database)
* [Check if ElasticSearch works](###check-if-elasticsearch-works)
* [Get all indexes](###get-all-indexes)
* [Get all nodes](###get-all-nodes)
* [Create index](###create-index)
* [Create index with mapping](###create-index-with-mapping)
* [Create index with mapping using json file](###create-index-with-mapping-using-json-file)
* [Modyfing existing mapping](###modyfing-existing-mapping)
* [Get index mapping](###get-index-mapping)
* [Delete index](###delete-index)
* [Get all documents in index (shorter version)](###get-all-documents-in-index-(shorter-version))
* [Get all documents in index with condition (shorter version)](###get-all-documents-in-index-with-condition-(shorter-version))
* [Get all documents in index (DSL)](###get-all-documents-in-index-(dsl))
* [Get all documents by keys (id)](###get-all-documents-by-keys-(id))
* [Get all documents with term condition](###get-all-documents-with-term-condition)
* [Get all documents with term condition (longer version)](###get-all-documents-with-term-condition-(longer-version))
* [Get all documents matching multiple terms](###get-all-documents-matching-multiple-terms)
* [Get all documents matching multiple terms when names is array](###get-all-documents-matching-multiple-terms-when-names-is-array)
* [Get all documents via numeric values](###get-all-documents-via-numeric-values)
* [Get all documents via dates](###get-all-documents-via-dates)
* [Get all documents via dates minues 1 year and 1 day](###get-all-documents-via-dates-minues-1-year-and-1-day)
* [Get all documents via dates minus 1 year and round up to month](###get-all-documents-via-dates-minus-1-year-and-round-up-to-month)
* [Get all documents with non null values or which have at least one element in array](###get-all-documents-with-non-null-values-or-which-have-at-least-one-element-in-array)
* [Get all documents starts with prefix](###get-all-documents-starts-with-prefix)
* [Get all documents starts with wildcard](###get-all-documents-starts-with-wildcard)
* [Get all documents starts with regular expression](###get-all-documents-starts-with-regular-expression)
* [Get all documents with match condition](###get-all-documents-with-match-condition)
* [Get all documents with match condition AND operator](###get-all-documents-with-match-condition-and-operator)
* [Get all documents with match_phrase condition](###get-all-documents-with-match_phrase-condition)
* [Create document without id](###create-document-without-id)
* [Update document or create new with id](###update-document-or-create-new-with-id)
* [Update document by id only](###update-document-by-id-only)
* [Get document by id](###get-document-by-id)
* [Delete document by id](###delete-document-by-id)
* [Delete all documents in index](###delete-all-documents-in-index)

### Run database
```sh
docker-compose up -d
```
### Check if elasticsearch works
```sh
curl --request GET --url http://localhost:9200/ | json_pp
```
### Get all indexes
```sh
curl --request GET --url localhost:9200/_cat/indices
```
### Get all nodes
```sh
curl --request GET --url http://localhost:9200/_cat/nodes
```
### Create index
```sh
curl --request PUT --url http://localhost:9200/index_name | json_pp
```
### Create index with mapping
```sh
curl -X PUT "localhost:9200/products" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "dynamic": "strict",
    "properties": {
      "field1": { "type": "text" },
      "field2": { "type": "long", "coerce": false }
    }
  }
}' | json_pp
```
### Create index with mapping using json file
```sh
curl -X PUT "localhost:9200/products" -H 'Content-Type: application/json' -d @es.json | json_pp
```
### Modyfing existing mapping
```sh
curl -X PUT "localhost:9200/products/_mapping" -H 'Content-Type: application/json' -d'
{
    "dynamic": "strict",
    "properties": {
      "field3": { "type": "date" }
    }
}' | json_pp
```
### Get index mapping
```sh
curl -X GET "localhost:9200/index_name/_mapping" | json_pp
```
### Delete index
```sh
curl --request DELETE --url http://localhost:9200/index_name
```
### Get all documents in index (shorter version)
```sh
curl --request GET --url 'http://localhost:9200/index_name/_search' 
```
### Get all documents in index with condition (shorter version)
```sh
# http://localhost:9200/resumes/_search?q=name:mike1 AND raw:Some text
curl --request GET --url 'http://localhost:9200/resumes/_search?q=name%3Amike1%20AND%20raw%3ASome%20text' 
```
### Get all documents in index (DSL)
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
        "match_all": {}
    }
}' | json_pp
```
### Get all documents by keys (id)
```sh
curl --request GET --url http://localhost:9200/resumes/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "ids": {
        "values": [
          "d715b4dc-e69a-40f5-91cb-64de6ea3d0f6"
        ]
      }
    }
}'
```
### Get all documents with `term` condition
`term` are not analyzed by analyzer, so `term` would be exacted matched, be aware of uppercase
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "term": {
        "name": "mike"
      }
    }
}'
```
### Get all documents with `term` condition (longer version)
`term` are not analyzed by analyzer, so `term` would be exacted matched
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "term": {
        "name": {
	  "value": "mike"
	}
      }
    }
}'
```
### Get all documents matching multiple terms
```sh
curl --request GET --url http://localhost:9200/resumes/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "terms": {
        "name": [
	  "mike",
          "mike1"
	]
      }
    }
}'
```
### Get all documents matching multiple terms when `names` is array
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "terms": {
        "names.keywords": [
	  "mike",
          "mike1"
	]
      }
    }
}'
```
### Get all documents via numeric values
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "range": {
        "similarity": {
	  "gte": 1,
          "lte": 10
	}
      }
    }
}'
```
### Get all documents via dates
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "range": {
        "created": {
	  "gte": "2023-02-18",
          "lte": "2023-02-20",
	  "format": "yyyy-MM-dd"
	}
      }
    }
}'
```
### Get all documents via dates minues 1 year and 1 day
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "range": {
        "created": {
	  "gte": "2023-02-18||-1y-1d",
	  "format": "yyyy-MM-dd"
	}
      }
    }
}'
```
### Get all documents via dates minus 1 year and round up to month
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "range": {
        "created": {
	  "gte": "now/M-1y"
	}
      }
    }
}'
```
### Get all documents with non null values or which have at least one element in array
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "exists": {
        "field": "tags"
      }
    }
}'
```
### Get all documents starts with prefix
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "prefix": {
        "description": "vege"
      }
    }
}'
```
### Get all documents starts with wildcard
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "wildcard": {
        "description": "v*get?ble"
      }
    }
}'
```
### Get all documents starts with regular expression
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "regexp": {
        "description": "veget[a-zA-Z]ble"
      }
    }
}'
```
### Get all documents with `match` condition
`match` are analyzed by analyzer, so `match` wouldn't be exacted matched, use `match` for flexible queries
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "match": {
        "name": "mike mock"
      }
    }
}'
```
### Get all documents with `match` condition AND operator
by default `match` uses `OR` operator which means elastic will look up on name which contains `mike` or `mock`
we can ensure that elastic will find records which has to have `mike` and `mock` in `name`
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "match": {
        "name": "mike"
      }
    }
}'
```
### Get all documents with `match_phrase` condition 
`match_phrase` are analyzed by analyzer, but are exactly matched, use `match_phrase` to match capital letters because `term` won't find Capitalized matches because every stuff saved to elastic with default analyzer are saved as lowercase
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
      "match_phrase": {
        "name": "mike"
      }
    }
}'
```
### Create document without id
```sh
curl --request POST --url http://localhost:9200/index_name/_doc --header 'Content-Type: application/json' \
  --data '{
	"name": "one"
}'
```
### Update document or create new with id
```sh
curl --request PUT \
  --url http://localhost:9200/index_name/_doc/7a9c8914-5fe2-4a67-8feb-d3f010645f48 \
  --header 'Content-Type: application/json' \
  --data '{
	"name": "two"
}'
```
### Update document by id only
```sh
curl --request POST \
  --url http://localhost:9200/index_name/_update/7a9c8914-5fe2-4a67-8feb-d3f010645f48 \
  --header 'Content-Type: application/json' \
  --data '{
	"doc": {
		"name": "three"	
	}
}'
```
### Get document by id
```sh
curl --request GET --url http://localhost:9200/index_name/_doc/7a9c8914-5fe2-4a67-8feb-d3f010645f48
```
### Delete document by id
```sh
curl --request DELETE --url http://localhost:9200/index_name/_doc/7a9c8914-5fe2-4a67-8feb-d3f010645f48
```
### Delete all documents in index
```sh
curl --request POST --url http://localhost:9200/index_name/_delete_by_query --header 'Content-Type: application/json' \
  --data '{
	"query": {
		"match_all": {}	
	}
}'
```
