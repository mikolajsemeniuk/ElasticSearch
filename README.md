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
Get all nodes
```sh
curl --request GET --url http://localhost:9200/_cat/nodes
```
Create index
```sh
curl --request PUT --url http://localhost:9200/index_name
```
Create index with mapping
```sh
curl -X PUT "localhost:9200/products" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "field1": { "type": "text" }
    }
  }
}'
```
Create index with mapping using json file
```sh
curl -X PUT "localhost:9200/products" -H 'Content-Type: application/json' -d @es.json
```
Get index mapping
```sh
curl -X GET "localhost:9200/products/_mapping" | json_pp
```
Delete index
```sh
curl --request DELETE --url http://localhost:9200/index_name
```
Get all documents in index
```sh
curl --request GET --url http://localhost:9200/index_name/_search --header 'Content-Type: application/json' \
  --data '{
    "query": {
        "match_all": {}
    }
}' | json_pp
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
