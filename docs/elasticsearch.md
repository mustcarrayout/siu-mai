```
GET condo/_search
{
    "query": {
        "bool" : {
            "must" : {
                "match_phrase" : {
                    "address" : "车阳路"
                }
            }
        }
    }
}

```



```
GET condo/_search
{
    "query": {
        "bool" : {
            "must" : [{ "term": { "address": "（删除）天山支路39弄38号403"}}]
        }
    }
}
```