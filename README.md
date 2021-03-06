# ElasticSearch
### 在laravel框架下写的model
在config目录下增加 elasticsearch.php配置文件，内容如下
```php
<?php
return [
    'host'=>env('ELASTICSEARCH_HOST','127.0.0.1'),
    'port'=>9200
];
```

### 创建索引

```php
$rs = Elastic::db()->createIndex([
            'index'=>'testdb',
            'type'=>'news',
            'properties'=>[
                'id'=>[
                    'type'=>'long',
                ],
                'theme_id'=>[
                    'type'=>'long',
                ],
                'cat_unique_name'=>[
                    'type'=>"text",
                ],
                'title'=>[
                    'type'=>'text',
                    'analyzer'=>'ik_max_word',
                    'search_analyzer'=>'ik_max_word',
                ],
                'summary'=>[
                    'type'=>'text',
                    'analyzer'=>'ik_max_word',
                    'search_analyzer'=>'ik_max_word',
                ],
                'keywords'=>[
                    'type'=>'text',
                    'analyzer'=>'ik_max_word',
                    'search_analyzer'=>'ik_max_word',
                ],
                'unique_name'=>[
                    'type'=>'text',
                ],
                'html'=>[
                    'type'=>'text',
                    'analyzer'=>'ik_max_word',
                    'search_analyzer'=>'ik_max_word',
                ]
            ]
        ]);

dd($rs);
```
Result:

```
array:2 [▼
  "status" => 1
  "data" => array:3 [▼
    "acknowledged" => true
    "shards_acknowledged" => true
    "index" => "testdb"
  ]
]
```


### 删除索引
```php
Elastic::db()->index('testdb')->deleteIndex();
#或者
Elastic::db('testdb')->deleteIndex();
```
### 搜索

```php
$rs = Elastic::db('theme')->type('article')->search([
            'query'=>[
                'match'=>['title'=>'git']
            ],
            'size'=>10
        ]);
```

```php
$rs = Elastic::db('theme')->type('article')->search([
            'query'=>[
                "bool"=>[
                    'must'=>[
                        "term"=>["title"=>"redis"]
                    ],
                    "must_not"=>[
                        "term"=>["keywords"=>"get"]
                    ]
                ]
            ],
            'size'=>10
        ]);
```

```php
$rs = Elastic::db('theme')->type('article')->search([
            'query'=>[
                "bool"=>[
                    'should'=>[
                        "match"=>["title"=>"redis"]
                    ],
                    "should"=>[
                        "match"=>["html"=>"redis"]
                    ]
                ]
            ],
            'size'=>10
        ]);
```

Result 搜索结果以分页形式返回

```php
dump($rs->items()); #数据内容
dump($rs->links()); #分页
```
