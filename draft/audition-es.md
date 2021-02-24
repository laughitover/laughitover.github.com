es中keyword和text区别

1.ES5.0及以后的版本取消了string类型，将原先的string类型拆分为text和keyword两种类型。它们的区别在于text会对字段进行分词处理而keyword则不会。
2.当你没有以IndexTemplate等形式为你的索引字段预先指定mapping的话，ES就会使用Dynamic Mapping，通过推断你传入的文档中字段的值对字段进行动态映射。例如传入的文档中字段price的值为12，那么price将被映射为long类型；字段addr的值为"192.168.0.1"，那么addr将被映射为ip类型。然而对于不满足ip和date格式的普通字符串来说，情况有些不同：ES会将它们映射为text类型，但为了保留对这些字段做精确查询以及聚合的能力，又同时对它们做了keyword类型的映射，作为该字段的fields属性写到_mapping中。例如，当ES遇到一个新的字段"foobar": "some string"时，会对它做如下的Dynamic Mapping：

{
    "foobar": {
        "type" "text",
        "fields": {
            "keyword": {
                "type": "keyword",
                "ignore_above": 256
            }
        }
    }
}
在之后的查询中使用foobar是将foobar作为text类型查询，而使用foobar.keyword则是将foobar作为keyword类型查询。前者会对查询内容做分词处理之后再匹配，而后者则是直接对查询结果做精确匹配。
3.ES的term query做的是精确匹配而不是分词查询，因此对text类型的字段做term查询将是查不到结果的（除非字段本身经过分词器处理后不变，未被转换或分词）。此时，必须使用foobar.keyword来对foobar字段以keyword类型进行精确匹配。

