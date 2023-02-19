# ElasticSearch
Run database
```sh
docker-compose up -d
```
Check if ElasticSearch works
```sh
curl --request GET --url http://localhost:9200/ | json_pp
```
Get all indexes
```sh
curl --request GET --url localhost:9200/_cat/indices
```
Get schema
```
curl --request GET --url localhost:9200/index_name/_mapping | json_pp
```
Get all nodes
```sh
curl --request GET --url http://localhost:9200/_cat/nodes
```
Create index
```sh
curl --request PUT --url http://localhost:9200/index_name | json_pp
```
Create index with mapping
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
Create index with mapping using json file
```sh
curl -X PUT "localhost:9200/products" -H 'Content-Type: application/json' -d @es.json | json_pp
```
Modyfing existing mapping
```sh
curl -X PUT "localhost:9200/products/_mapping" -H 'Content-Type: application/json' -d'
{
    "dynamic": "strict",
    "properties": {
      "field3": { "type": "date" }
    }
}' | json_pp
```
Get index mapping
```sh
curl -X GET "localhost:9200/products/_mapping" | json_pp
```
Delete index
```sh
curl --request DELETE --url http://localhost:9200/index_name
```
Get all documents in index (shorter version)
```sh
curl --request GET --url 'http://localhost:9200/index_name/_search' 
```
Get all documents in index with condition (shorter version)
```sh
# http://localhost:9200/resumes/_search?q=name:mike1 AND raw:Some text
curl --request GET --url 'http://localhost:9200/resumes/_search?q=name%3Amike1%20AND%20raw%3ASome%20text' 
```
Get all documents in index (DSL)
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
        "match_all": {}
    }
}' | json_pp
```
Get all documents by keys (id)
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
Get all documents with `term` condition (`term` are not analyzed by analyzer, so `term` would be exacted matched, be aware of uppercase)
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
Get all documents with `term` condition (`term` are not analyzed by analyzer, so `term` would be exacted matched), longer version
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
Get all documents matching multiple terms
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
Get all documents matching multiple terms when `names` is array
```sh
curl --request GET --url http://localhost:9200/resumes/_search --header 'Content-Type: application/json' \
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
Get all documents with `match` condition (`match` are analyzed by analyzer, so `match` wouldn't be exacted matched, use `match` for flexible queries)
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
Get all documents with `match_phrase` condition (`match_phrase` are analyzed by analyzer, but are exactly matched, use `match_phrase` to match capital letters because `term` won't find Capitalized matches because every stuff saved to elastic with default analyzer are saved as lowercase)
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
Create document without id
```sh
curl --request POST --url http://localhost:9200/index_name/_doc --header 'Content-Type: application/json' \
  --data '{
	"name": "one"
}'
```
Update document or create new with id
```sh
curl --request PUT \
  --url http://localhost:9200/index_name/_doc/7a9c8914-5fe2-4a67-8feb-d3f010645f48 \
  --header 'Content-Type: application/json' \
  --data '{
	"name": "two"
}'
```
Update document by id only
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
Get document by id
```sh
curl --request GET --url http://localhost:9200/index_name/_doc/7a9c8914-5fe2-4a67-8feb-d3f010645f48
```
Delete document by id
```sh
curl --request DELETE --url http://localhost:9200/index_name/_doc/7a9c8914-5fe2-4a67-8feb-d3f010645f48
```
Delete all documents in index
```sh
curl --request POST --url http://localhost:9200/index_name/_delete_by_query --header 'Content-Type: application/json' \
  --data '{
	"query": {
		"match_all": {}	
	}
}'
```
