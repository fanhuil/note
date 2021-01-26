# 索引



## 	查看索引

```shell
 # 查看索引
 curl -X GET "localhost:9200/_cat/indices?v"
```



## 	创建索引

```shell
# 创建名字为company的索引
curl -X PUT "localhost:9200/company?pretty"
```



## 	删除索引

```shell
# 删除名字为company的索引
curl -X DELETE "localhost:9200/company?pretty"		
```



# 数据



## 	查看数据

```shell
# 查看数据
curl -X GET "localhost:9200/company/_doc/1?pretty=true"
```



## 	新建数据

```shell
# 新建数据
curl -H "Content-Type: application/json" -X PUT 'localhost:9200/company/_doc/1' -d '
{
 "name":"fanhuilin",
 "sex":"man",
 "age":"27"
}'
```



## 	修改数据(修改/创建文档)

```shell
# id值可填可不填。若id值已存在，则会更新已存在的文档,使用PUT；id值不存在则会创建新的文档，使用POST
curl -X PUT "localhost:9200/company/_doc/1?pretty" -H 'Content-Type: application/json' -d'{  "name": "Jane Doe"}'
```



## 	删除数据

```shell
# 删除数据
curl -X DELETE "localhost:9200/company/_doc/1?pretty"
```



## 	根据json文件添加数据

```shell
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@accounts.json"
```



# 查询数据















