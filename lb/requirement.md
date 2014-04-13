# Listening Bord プロジェクト定義
- 時系列データ・シリーズデータを保存し提示するREST APIストレージサービス
	- オープンデータ提供サービス
		- オープンデータのクローリングと格納
			- 次世代利用APIサービス
			- e-stat等からのスクレイピング
			- クラウドソーシングサービスを利用した手による入力
				- 入力しやすいインタフェース(Google Docs等の活用)の研究
				- 実はここの仕組みをつくるのが一番時間がかかりそう…
		- オープンデータのAPIによる供給
		- オープンデータのダッシュボードによる提示
	- IoT社会の到来を目指した時系列データ・シリーズデータを格納するストレージ
		- IoT端末からのデータ格納
			- サーバーからのデータ格納
			- スマホからのデータ格納
			- センサーデバイスからのデータの格納
			- POS端末からのデータ格納
		- API経由によるデータの検索・取得
			- プログラムAPI
			- ダッシュボード
- 目指さないもの
	- tdagentやloglyなどのサーバーメトリックサービス
	- マイニング・解析等の機能は当初はやらない

# Listening Bord 要件
- プロトタイプ @odersky
	- tornado <-> hyperdex系
		- hyperdex schema
		- async_put, async_get
	- tornado <-> elasticsearch, tornado <-> hyperdex系
		- elasticsearch schema
		- メタデータ検索
	- 
- オープンデータのクローリング
- ダッシュボード
- hyperdex シリーズデータのTB以上の拡張・運用
	- watchdogはどうする？
	- ノードの追加は？
	- failするサーバの数は？ 
- elastic searchによるメタデータ検索
- 柔軟なAPI

# REST API

## Put
|         | API Info    |
| ------- |:-------------:|
|Method| PUT |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/put?|
|Format  | JSON |

### parameters

|parameter | example |
| ------- |:-------------:|
|apikey *| API_KEY |

### body

````
{
   "streams" : [ 
      { "cid" : "ch1",
        "points":[
          {"t":"2013-04-22T00:35:43Z","v":"41"},
          {"t":"2013-04-22T00:55:43Z","v":"84"},
          {"t":"2013-04-22T01:15:43Z","v":"41"},
          {"t":"2013-04-22T01:35:43Z","v":"83"}
        ],
        "current" : "33"
      },
      { "cid" : "ch2",
        "points":[
          {"t":"2013-04-22T00:35:43Z","v":"revalue"},
          {"t":"2013-04-22T00:55:43Z","v":"string value"},
          {"t":"2013-04-22T01:15:43Z","v":"any string"},
          {"t":"2013-04-22T01:35:43Z","v"":"structured data"}
        ],
        "current" : "test"      
      }
   ]
}

````

````
{
   "streams" : [ 
     { "cid" : "ch1",
       "current" : "33"
     },
     {  "cid" : "ch2",
        "current" : "test"      
     }
   ]
}

````

### response

## Get (points)
|         | API Info    |
| ------- |:-------------:|
|Method| GET |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/getpoint?|
|Format  | JSON |

### parameters

|parameter | example |
|-|:-:|
|apikey *| API_KEY |
|time *| 2014-05-20T11:01:46Z, now |

### body

````

````

### response

````
{
  "id": 121601,
　"title": "Demo",
　"private": "false",
　"feed": "https://api.xively.com/v2/feeds/121601.json",
　"status": "frozen",
　"updated": "2013-04-23T03:25:48.686462Z",
　"created": "2013-03-29T15:50:43.398788Z",
　"datastreams": [
 {
 	"id": "ch1",
 	"current": "333",
 	"t": "2013-04-23T01:10:02.986063Z",
 	"max": "333.0",
 	"min": "333.0"},
 {
 	"id": "ch2",
 	"current_value": "value",
 	"at": "2013-04-23T00:40:34.032979Z"},
 ]
}
````

## Get (range)
|         | API Info    |
| ------- |:-------------:|
|Method| GET |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/getrange?|
|Format  | JSON |

### parameters

|parameter | example |
|-|:-:|
|apikey *| API_KEY |
|from| 2013-04-22T00:35:46Z |
|to | 2013-04-22T01:50:46Z|
|delta | +13343 / -13333 [sec]|


### body

````

````

### response

````
{
　  "streams" : [ 
     { "cid" : "ch1",
       "points":[
         {"t":"2013-04-22T00:35:43Z","v":"41"},
         {"t":"2013-04-22T00:55:43Z","v":"84"},
         {"t":"2013-04-22T01:15:43Z","v":"41"},
         {"t":"2013-04-22T01:35:43Z","v":"83"}
       ],
       "current" : "33"
     },
     { "cid" : "ch2",
       "points":[
         {"t":"2013-04-22T00:35:43Z","v":"revalue"},
         {"t":"2013-04-22T00:55:43Z","v":"string value"},
         {"t":"2013-04-22T01:15:43Z","v":"any string"},
         {"t":"2013-04-22T01:35:43Z","v"":"structured data"}
       ],
       "current" : "test"
     }
   ]
}
````
## Delete (point)
|         | API Info    |
| ------- |:-------------:|
|Method| DELETE |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/delpoint?|
|Format  | JSON |

### parameters

|parameter | example |
|-|:-:|
|apikey *| API_KEY |
|cid *| ch1 |
|time *| 2013-04-22T00:35:46Z |

### body

````
````

### response

````
200 OK
````

## Delete (range)
|         | API Info    |
| ------- |:-------------:|
|Method| DELETE |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/delrange?|
|Format  | JSON |

### parameters

|parameter | example |
|-|:-:|
|apikey *| API_KEY |
|cid *| ch1 |
|from| 2013-04-22T00:35:46Z |
|to | 2013-04-22T01:50:46Z|
|delta | +13343 / -13333 [sec]|


### body

````
````

### response

````
200 OK
````

## Delete (stream)
|         | API Info    |
| ------- |:-------------:|
|Method| DELETE |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/delstream?|
|Format  | JSON |

### parameters

|parameter | example |
|-|:-:|
|apikey *| API_KEY |

### body

````
````

### response

````
200 OK
````

# MetaSearch API
## search
|         | API Info    |
| ------- |:-------------:|
|Method| GET / POST |
|Base URL| https://lb.bakfoo.com|
|Endpoint| /api/v2/sereis/SERIES_ID/search?|
|Format  | JSON |

### parameters

|parameter | example |
|-|:-:|
|apikey *| API_KEY |
|q| 検索パラメータ，AND, ORが使える |
|from | 2013-04-22T01:50:46Z|
|to | 2013-04-22T01:50:46Z|


### body

````

````

### response

````
{
　  "streams" : [ 
     { "cid" : "ch1",
       "points":[
         {"t":"2013-04-22T00:35:43Z","v":"41"},
         {"t":"2013-04-22T00:55:43Z","v":"84"},
         {"t":"2013-04-22T01:15:43Z","v":"41"},
         {"t":"2013-04-22T01:35:43Z","v":"83"}
       ],
       "current" : "33"
     },
     { "cid" : "ch2",
       "points":[
         {"t":"2013-04-22T00:35:43Z","v":"revalue"},
         {"t":"2013-04-22T00:55:43Z","v":"string value"},
         {"t":"2013-04-22T01:15:43Z","v":"any string"},
         {"t":"2013-04-22T01:35:43Z","v"":"structured data"}
       ],
       "current" : "test"
     }
   ]
}
````

# Streaming API
# Range Streaming API
# Trigger API
# MTQQ Bridge