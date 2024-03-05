

GET kibana_sample_data_flights/_mapping
GET kibana_sample_data_ecommerce/_mapping
GET kibana_sample_data_logs/_mapping

POST kibana_sample_data_flights/_search
{
  "query": {
    "match_all": {}
  },
  "track_total_hits": true
}
POST kibana_sample_data_ecommerce/_search
{
  "query": {
    "match_all": {}
  },
  "track_total_hits": true
}
POST kibana_sample_data_logs/_search
{
  "query": {
    "match_all": {}
  },
  "track_total_hits": true
}

# start----- Sort the results of a query by a given set of requirements  -----

# 如果同时查询两个索引，但是某个字段使用了不同类型的，比如一个long 一个doube
# 创建long类型的索引
PUT longindex
{
   "mappings": {
    "properties": {
      "product": {
        "type": "long"
      }
    }
  }
}
# 写入数据
POST longindex/_doc
{
  "product": 4
}
# 创建double类型的索引
PUT doubleindex
{
   "mappings": {
    "properties": {
      "product": {
        "type": "double"
      }
    }
  }
}
# 写入数据
POST doubleindex/_doc
{
  "product": 7.0
}

# 同时查询这两个索引并排序（如果不指定numeric_type则会报错）
POST longindex,doubleindex/_search
{
  "query": {"match_all": {}},
  "sort": [
    {
      "product": {
        "order": "desc",
        "numeric_type": "long"
      }
    }
  ]
}
# 如果mapping没有这个排序字段，可以定义unmapped_type来防止报错。比如同时查询两个索引，只有一个索引都定义了排序字段的mapping类型
#如果mapping中有排序字段 而文档中没有这个字段也不会报错
POST longindex,doubleindex/_search
{
  "query": {"match_all": {}},
  "sort": [
    {
      "abc": {
        "missing" : "_last",
        "order": "desc",
        "unmapped_type": "long"
      }
    }
  ]
}

# 对多个值的字段排序
# 创建有多个值的域的索引
PUT multivaluesindex
{
  "mappings": {
    "properties": {
      "product": {
        "type": "long"
      },
      "@timestamp": {
        "type": "date"
      }
    }
  }
}

# 写入文档
POST multivaluesindex/_doc
{
   "@timestamp": 1591890612001,
   "product":[1,9]
}
POST multivaluesindex/_doc
{
   "@timestamp": 1591890612001,
   "product":[2,7]
}

# 根据多值域排序
POST multivaluesindex/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "product": {
        "order": "asc",
        "mode": "avg"
      }
    }
  ]
}

# 使用脚本进行排序
POST multivaluesindex/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_script": {
        "type": "number",
        "script": {
          "lang": "painless",
          "source": "doc['product'].get(1) * params.factor",
          "params": {
            "factor": 3
          }
        },
        "order": "asc"
      }
    }
  ]
}

# nested类型的排序
# 创建mapping类型为nested的索引（kibana的默认数据中没有这种类型的mapping）
PUT nestedindex
{
  "mappings": {
    "properties": {
      "product": {
        "type": "nested"
      },
      "@timestamp": {
        "type": "date"
      }
    }
  }
}
# 添加文档
POST nestedindex/_doc
{
  "@timestamp": 1591890612000,
  "product": [
    {
      "price": 1,
      "color": "yellow"
    },
    {
      "price": 5,
      "color": "red"
    }
  ]
}
POST nestedindex/_doc
{
  "@timestamp": 1591890612001,
  "product": [
    {
      "price": 2,
      "color": "yellow"
    },
    {
      "price": 4,
      "color": "red"
    }
  ]
}
POST nestedindex/_search

# 根据nested类型的值排序
POST nestedindex/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "product.price": {
        "mode": "min",
        "order": "asc",
        "nested": {
          "path": "product",
          "filter": {
            "match": {
              "product.color": "yellow red"
            }
          }
        }
      }
    }
  ]
}

# nested下还有nested的例子
# 创建索引
PUT nestednestedindex
{
   "mappings": {
    "properties": {
      "product": {
        "type": "nested",
        "properties": {
          "company":{
            "type":"nested"
          }
        }
      },
      "@timestamp": {
        "type": "date"
      }
    }
  }
}
# 添加文档
POST nestednestedindex/_doc
{
  "@timestamp": 1591890612000,
  "product": [
    {
      "price": 1,
      "color": "yellow",
      "company": [
        {
          "name": "a",
          "age": 44
        },
        {
          "name": "b",
          "age": 22
        }
      ]
    },
    {
      "price": 5,
      "color": "red",
      "company": [
        {
          "name": "b",
          "age": 55
        },
        {
          "name": "c",
          "age": 11
        }
      ]
    }
  ]
}
POST nestednestedindex/_doc
{
  "@timestamp": 1591890612000,
  "product": [
    {
      "price": 1,
      "color": "yellow",
      "company": [
        {
          "name": "a",
          "age": 66
        },
        {
          "name": "b",
          "age": 11
        }
      ]
    },
    {
      "price": 5,
      "color": "red",
      "company": [
        {
          "name": "b",
          "age": 44
        },
        {
          "name": "c",
          "age": 0
        }
      ]
    }
  ]
}
POST nestednestedindex/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "product.company.age": {
        "mode": "min",
        "order": "desc",
        "nested": {
          "path": "product",
          "filter": {
            "match": {
              "product.color": {
                "query":"yellow red"
                
              }
            }
          },
          "nested": {
            "path": "product.company",
            "filter": {
              "match": {
                "product.company.name": "b"
              }
            }
          }
        }
      }
    }
  ]
}
# end ----- Sort the results of a query by a given set of requirements  -----

# start --- Implement pagination of the results of a search query---
# 如果查询结果不超过10000个，可以使用from跟size
GET kibana_sample_data_ecommerce/_search
{
  "from": 0,
  "size": 20,
  "query": {
    "match": {
      "customer_full_name": {
        "query": "Eddie Underwood",
        "operator": "and"
      }
    }
  }
}
# 超过10000条则使用search after
POST /kibana_sample_data_flights/_pit?keep_alive=5m

# 使用pit进行搜索
GET /_search
{
  "size": 10000,
  "query": {
    "match_all": {}
  },
  "pit": {
    "id": "n_XoAwEaa2liYW5hX3NhbXBsZV9kYXRhX2ZsaWdodHMWRmJGNGxCMDRRc3VZcXYydVV6NnkwQQAWUjFqaFRIaENRM2VYeTVxRHJRS1pPQQAAAAAAAABIvhZmOEZoMmctMlJYcTFVTnFobWNzb3Z3AAEWRmJGNGxCMDRRc3VZcXYydVV6NnkwQQAA",
    "keep_alive": "5m"
  },
  "sort": [
    {
      "timestamp": {
        "order": "asc",
        "format": "strict_date_optional_time_nanos"
      }
    },
    {
      "_shard_doc": "desc"
    }
  ]
}

# 获取下一页的内容，使用上一个请求中最后一个hit中的sort字段中的值作为下一个请求中search_after的值
GET /_search
{
  "size": 10000,
  "query": {
    "match_all": {}
  },
  "pit": {
    "id": "n_XoAwEaa2liYW5hX3NhbXBsZV9kYXRhX2ZsaWdodHMWRmJGNGxCMDRRc3VZcXYydVV6NnkwQQAWUjFqaFRIaENRM2VYeTVxRHJRS1pPQQAAAAAAAABIvhZmOEZoMmctMlJYcTFVTnFobWNzb3Z3AAEWRmJGNGxCMDRRc3VZcXYydVV6NnkwQQAA",
    "keep_alive": "5m"
  },
  "sort": [
    {
      "timestamp": {
        "order": "asc",
        "format": "strict_date_optional_time_nanos"
      }
    },
    {
      "_shard_doc": "desc"
    }
  ],
  "search_after": [
  "2024-02-25T23:50:12.000Z",
          13020
  ]
}

# 删除PIT
DELETE /_pit
{
    "id" : "n_XoAwEaa2liYW5hX3NhbXBsZV9kYXRhX2ZsaWdodHMWRmJGNGxCMDRRc3VZcXYydVV6NnkwQQAWUjFqaFRIaENRM2VYeTVxRHJRS1pPQQAAAAAAAABMzxZmOEZoMmctMlJYcTFVTnFobWNzb3Z3AAEWRmJGNGxCMDRRc3VZcXYydVV6NnkwQQAA"
}

# 获取目前集群中的PIT，见open_contexts
GET /_nodes/stats/indices/search
# end --- Implement pagination of the results of a search query---

# start------------- Define and use a search template   -----------
# 创建一个search template
POST kibana_sample_data_ecommerce/_search?size=1

PUT _scripts/my-search-template
{
  "script": {
    "lang": "mustache",
    "source": {
      "{{my_query}}": {
        "match": {
          "{{field_name}}":{
            "query": "{{query_string}}",
             "operator":"{{operator}}"
          }
        }
      },
      "from": "{{from}}",
      "size": "{{size}}"
    },
    "params": {
      "query_string": "My query string"
    }
  }
}

# 测试search template
POST _render/template
{
  "id": "my-search-template",
  "params": {
    "my_query":"query",
    "field_name":"category",
    "query_string": "Men's Clothing",
    "operator": "AND",
    "from": 0,
    "size": 10
  }
}

# 运行这个模板
GET kibana_sample_data_ecommerce/_search/template
{
  "id": "my-search-template",
  "params": {
    "my_query":"query",
    "field_name":"category",
    "query_string": "Men's Clothing",
    "operator": "AND",
    "from": 0,
    "size": 10
  }
}

# end------------- Define and use a search template   -----------

# start------------- Define and use index aliases-----------
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "mytime34",
        "alias": "my-alias1212"
      }
    }
  ]
}
# 根据别名搜索：
POST my-alias1212/_search

# 查看某个索引当前所有的别名
GET mytime34/_alias

# 查看所有的别名
GET _alias

# end------------- Define and use index aliases-----------

# start------Use the Reindex API and Update By Query API to reindex and/or update documents----
GET kibana_sample_data_ecommerce/_mapping

POST kibana_sample_data_ecommerce/_search
{
  "query": {"term": {
    "customer_first_name.keyword": {
      "value": "Rabbia Al"
    }
  }}
}
# keword类型过滤
POST _reindex?
{
  "source": {
    "index": "kibana_sample_data_ecommerce",
    "query": {"term": {
      "customer_first_name.keyword": {
        "value": "Rabbia Al"
      }
    }}
  },
  "dest": {
    "index": "new-kibana_sample_data_ecommerce"
  }
}
# text类型过滤
POST _reindex?
{
  "source": {
    "index": "kibana_sample_data_ecommerce",
    "query": {
      "match": {
        "customer_full_name": "Rabbia Al Ruiz"
      }
    }
  },
  "dest": {
    "index": "new-kibana_sample_data_ecommerce"
  }
}

#查看新的索引中的数据
POST new-kibana_sample_data_ecommerce/_search
{
  "query": {"match_all": {}}
}

# 使用Update By Query API 
POST new-kibana_sample_data_ecommerce/_update_by_query?
{
  "script": {
    "source": "ctx._source.products[0].base_price=100000",
    "lang": "painless"
  },
  "query": {
   "match": {
     "category": "Men's Shoes"
   }
  }
}
#查看新的索引中的数据
POST new-kibana_sample_data_ecommerce/_search?
{
  "query": {"match_all": {}},
  "sort": [
    {
      "products.base_price": {
        "order": "desc"
      }
    }
  ]
}
 
 
# end------Use the Reindex API and Update By Query API to reindex and/or update documents----

# start------Define an index template that creates a new data stream
# 创建一个用于data stream的模板
PUT _index_template/template-for-data-stream
{
  "index_patterns":["mydatastream*"],
  "priority": 2,
  "template": {
    "settings": {
      "number_of_shards": 3,
      "number_of_replicas": 2,
      "refresh_interval": "1s"
    },
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "content": {
          "type": "text"
        },
        "text": {
          "type": "text"
        }
      }
    },
    "aliases": {
      "my_alias11111": {}
    }
  },
  "data_stream": {}
}

# 写入文档
POST mydatastrea456/_doc/
{
  "text": "dfddd thed request",
  "@timestamp": "1591890612000",
  "content":"ce"
}

# 测试别名是否正确
POST my_alias11111/_search
{
  "query": {"match_all": {}}
}

# 模拟下这个模板会匹配那些索引
POST /_index_template/_simulate_index/template-for-data-stream
# 查看模板的settings、aliases、mappings
POST /_index_template/_simulate/template-for-data-stream
# end------Define an index template that creates a new data stream


# start------------------------Write an asynchronous search------------------------
GET kibana_sample_data_ecommerce/_mapping
GET kibana_sample_data_ecommerce/_search?size=1
# 异步查询
POST /kibana_sample_data_ecommerce/_async_search?
{
  "size": 0, 
  "sort": [
    { "order_date": { "order": "asc" } }
  ],
  "aggs": {
    "taxful_total_price_sum": {
      "sum": {
        "field": "taxful_total_price"
      }
    }
  }
}

# end------------------------Write an asynchronous search------------------------

# start------------------------------------ Backup and restore a cluster and/or specific indices ---------------------------

# 获取快照状态
GET _snapshot/mysnapshot1/my_snapshot1/_status

POST /_snapshot/mysnapshot1/my_snapshot1/_restore?wait_for_completion=true
{
  "indices": "mytime345", 
  "ignore_unavailable": true,
  "include_global_state": true,
  "rename_pattern": "mytime345",
  "rename_replacement": "restored_index_mytime345",
  "include_aliases": true
}

GET _cat/indices/



# end------------------------------------ Backup and restore a cluster and/or specific indices ---------------------------


# start------------------------------------ searchable snapshot ---------------------------
# 手动创建一个snapshot，mysnapshot1为仓库名称
PUT /_snapshot/mysnapshot1/my_snapshot3
# 获取快照状态
GET _snapshot/mysnapshot1/my_snapshot2/_status

# 挂载一个快照
#下面的例子中由于要求分配副本副本，要确保cluster.routing.allocation.enable参数是否允许（重要）
POST /_snapshot/mysnapshot1/my_snapshot3/_mount?wait_for_completion=true
{
  "index": "mytime4", 
  "renamed_index": "mytime1-3-snapshot", 
  "index_settings": { 
    "index.number_of_replicas": 1
  },
  "ignore_index_settings": [ "index.refresh_interval" ] 
}


# 获取刚刚挂载的searchable snapshot的状态,mytime1-3-snapshot为挂载新的索引名称
GET /mytime1-3-snapshot/_searchable_snapshots/stats
GET /_searchable_snapshots/stats


# 查询刚刚挂载的索引
POST mytime1-3-snapshot/_search
{
  "query": {"match_all": {}}
}
# 查询原始索引mytime4
POST mytime4/_search
{
  "query": {"match_all": {}}
}


# 挂载一个data stream
POST /_snapshot/mysnapshot1/my_snapshot2/_mount?wait_for_completion=true
{
  "index": ".ds-mytime345-2024.03.04-000001", 
  "renamed_index": "mytime345-data-stream-snapshot", 
  "index_settings": { 
    "index.number_of_replicas": 1
  },
  "ignore_index_settings": [ "index.refresh_interval" ] 
}

POST mytime345-data-stream-snapshot/_search
{
 "query": {"match_all": {}}
}

# end------------------------------------ searchable snapshot ---------------------------

PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "all"
  }
}

GET .ds-mytime-2024.03.04-000001/_mapping

GET .ds-mytime--*/_ilm/explain

GET _ilm/policy/my-ilm

GET mytime34/_ilm/explain



HEAD _alias/my_alias_abc

GET _alias/my*

GET my_alias_abc1


POST mytime4/_doc/
{
  "text": "dfddd thed request",
  "@timestamp": "1591890612000"
}

PUT _index_template/my-ilm-template5
{
  "priority": 2,
  "template": {
    "settings": {
      "index": {
        "number_of_shards": "1",
        "lifecycle.name": "my-ilm"
      }
    },
    "mappings": {
      "properties": {
        "TestTest": {
          "type": "text"
        }
      }
    },
    "aliases": {
      "my_alias_abc1": {
        "filter": {
          "term": {
            "text": "abc"
          }
        }
      },
      "my_alias_abc": {}
    }
  },
  "index_patterns": [
    "mytime345"
  ],
  "data_stream": {
    "hidden": false,
    "allow_custom_routing": false
  }
}

PUT _ilm/policy/my-ilm
{"policy":{"phases":{"hot":{"min_age":"0ms","actions":{"set_priority":{"priority":100},"rollover":{"max_age":"30d","max_primary_shard_size":"50gb"}}},"warm":{"min_age":"30d","actions":{"set_priority":{"priority":50}}},"cold":{"min_age":"50d","actions":{"set_priority":{"priority":0}}}}}}