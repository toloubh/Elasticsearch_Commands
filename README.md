# ELASTICSEARCH COMMANDS CHEAT SHEET
Here show some of the most common ElasticSearch commands using curl. ElasticSearch is
sometimes complicated. So here make it simple.

* Using ElasticSearch with Basic Authentication
If you have turned on security with ElasticSearch then you need to supply the user and password
like shown below to every curl command:
```
curl -XGET 'http://localhost:9200/_cat/indices?v' -u elastic:(password)
```

* Pretty Print
Add ?pretty=true to any search to pretty print the JSON. Like this:
```
curl -XGET -u elastic:password 'http://localhost:9200/(index)/_search'?pretty=true
```

* delete index
Below the index is named index_(index).
```
curl -XDELETE -u elastic:password 'http://localhost:9200/index_(index)'
```

* list all indexes
```
curl -XGET -u elastic:password 'http://localhost:9200/_cat/indices?v'
```
* list all docs in index
```
curl -XGET -u elastic:password 'http://localhost:9200/(index)/_search'
```

* query using URL parameters
Here we use Lucene query format to write q=uni:x.
```
curl -XGET -u elastic:password http://localhost:9200/index_(index)/_search?q=uni:x
```

* Query with JSON aka Elasticsearch Query DSL
You can query using parameters on the URL. But you can also use JSON, as shown in the next example.
JSON would be easier to read and debug when you have a complex query than one giant string of URL parameters.
```
curl -XGET -u elastic:password --header 'Content-Type: application/json'
http://localhost:9200/index_(index)/_search -d '{
"query" : {
	"match" : { "uni": "x" }
	}	
}'
```

* list index mapping
All Elasticsearch fields are indexes. So this lists all fields and their types in an index.
```
curl -XGET -u elastic:password http://localhost:9200/index_(index)
```

* Add Data
```
curl -XPUT -u elastic:password --header 'Content-Type: application/json' http://localhost:9200/index_(index)/_doc/1 -d '{
	"school" : "Harvard"
}'
``` 

* Update Doc
Here is how to add fields to an existing document. First we create a new one. Then we update it.
```
curl -XPUT -u elastic:password --header 'Content-Type: application/json'
http://localhost:9200/(index)s/_doc/2 -d '
{
	"school": "Clemson"
}
```
```
curl -XPOST -u elastic:password --header 'Content-Type: application/json'
http://localhost:9200/(index)s/_doc/2/_update -d '{
"doc" : {
	"students": 50000}
}'
```

* backup index
```
curl -u elastic:password -XPOST --header 'Content-Type: application/json' http://localhost:9200/_reindex -d '{
"source": {
	"index": "(index)s"
},
"dest": {
	"index": "(index)s_backup"
	}
}'
```

* Show cluster health
```
curl -XGET -u elastic:password -H 'Content-Type: application/json' http://localhost:9200/_cluster/health?pretty
```

* To query and return only certain fields
To return only certain fields put them into the _source array:
```
GET filebeat-7.6.2-2020.05.05-000001/_search
{
	"_source": ,
	"query": {
	 	"match" : { "source.geo.country_iso_code": "GR" }
	}
}
```

* To Query by Date
When the field is of type date you can use date math, like this:
```
GET filebeat-7.6.2-2020.05.05-000001/_search
{
	"query": {
		"range" : {
			"event.created": {
				"gte" : "now-7d/d"
					}
			}
		}
}
```
