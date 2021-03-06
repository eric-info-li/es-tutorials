## create another test index for reindexing
```
curl --location --request PUT 'http://<yourhost>:9200/bookdb_index_new_test' \
--header 'Content-Type: application/json' \
--data-raw '  { "settings": { "number_of_shards": 1 }}'
```
*******************************************************************************************
## create mapping for bookdb_index_new_test
```
curl --location --request PUT 'http://<yourhost>:9200/bookdb_index_new_test/_mapping' \
--header 'Content-Type: application/json' \
--data-raw '{
  "properties": {
    "title":  { "type": "text"},
    "authors":  { "type": "text"},
    "summary":  { "type": "text"},
    "publish_date":  { "type": "text"},
    "num_reviews":  { "type": "integer"},
    "publisher":  { "type": "text"}
  }
}'
```
*************************************************************************************************
## get mapping for an index
```
curl --location --request GET 'http://<yourhost>:9200/bookdb_index_new_test/_mapping' \
--data-raw ''
```

*************************************************************************************************
## reindex bookdb_index_new into bookdb_index_new_test
```
curl --location --request POST 'http://<yourhost>:9200/_reindex' \
--header 'Content-Type: application/json' \
--data-raw '{
  "source": {
    "index": "bookdb_index_new"
  },
  "dest": {
    "index": "bookdb_index_new_test"
  }
}'
```
*************************************************************************************************************
## check all data in new index
```
curl --location --request GET 'http://<yourhost>:9200/bookdb_index_new_test/_search' \
--data-raw ''
```
*************************************************************************************************************
## delete_by_query
## delete the books with title having Definitive keyword
```
curl --location --request POST 'http://<yourhost>:9200/bookdb_index_new_test/_delete_by_query?scroll_size=5000' \
--header 'Content-Type: application/json' \
--data-raw '{
    "query": {
    "term": {
      "publisher": "oreilly"
    }
  }
}'
```
*******************************************************************************************************
## check records in bookdb_index_new_test index

```
curl --location --request GET 'http://<yourhost>:9200/bookdb_index_new_test/_search' \
--data-raw ''
```
************************************************************************************************************
## check records in bookdb_index_new index
## it is not deleted from original source index

```
curl --location --request GET 'http://<yourhost>:9200/bookdb_index_new/_search' \
--data-raw ''
```
****************************************************************************************************************
## add another mapping ranking to bookdb_index_new_test index
```
curl --location --request PUT 'http://<yourhost>:9200/bookdb_index_new_test/_mapping' \
--header 'Content-Type: application/json' \
--data-raw '{
    "properties": {
    "ranking":  { "type": "integer"}
    }
}'
```
**************************************************************************************************************
## check the data  bookdb_index_new_test index - no rankings field shown
```
curl --location --request GET 'http://<yourhost>:9200/bookdb_index_new_test/_search' \
--data-raw ''
```
****************************************************************************************************************
##update_by_query
## update the ranking as 1 for the book which has num_reviews  =23
```
curl --location --request POST 'http://<yourhost>:9200/bookdb_index_new_test/_update_by_query?conflicts=proceed' \
--header 'Content-Type: application/json' \
--data-raw '{
  "query": { 
    "term": {
      "num_reviews": "23"
    }
  },
  "script": "ctx._source.ranking = '\''1'\''"
}'
```
****************************************************************************************************************************
## check the ranking for the updated data
```
curl --location --request GET 'http://<yourhost>:9200/bookdb_index_new_test/_search' \
--data-raw ''
```
*******************************************************************************************************************************
