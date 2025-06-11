# Elasticsearch

## 核心数据类型 (Core Datatypes)

* **字符串类型 (String Datatypes)**
    * `text`: 用于索引全文内容的字段，例如文章内容、产品描述等。这类字段会经过分词器处理，支持全文搜索。
    * `keyword`: 用于索引结构化内容的字段，例如电子邮件地址、主机名、状态码、标签等。这类字段不会被分词，常用于过滤、排序和精确匹配。

* **数值类型 (Numeric Datatypes)**
    支持多种数值类型，以适应不同范围和精度的数字存储：
    * `long`: 长整型 64 位。
    * `integer`: 整型 32 位。
    * `short`: 短整型 16 位。
    * `byte`: 字节型 8 位。
    * `double`: 双精度浮点型 64 位。
    * `float`: 单精度浮点型 32 位。
    * `half_float`: 半精度浮点型 16 位。
    * `scaled_float`: 缩放的浮点型，使用一个 long 存储，并通过一个缩放因子进行缩放。

* **日期类型 (Date Datatype)**
    * `date`: 用于存储日期和时间信息。支持多种格式的日期字符串、长整型的毫秒数或纳秒数。

* **布尔类型 (Boolean Datatype)**
    * `boolean`: 用于存储布尔值 (`true` 或 `false`)。

* **二进制类型 (Binary Datatype)**
    * `binary`: 用于存储 Base64 编码的二进制值。默认不存储也不可搜索。

**复杂数据类型 (Complex Datatypes)**

* **对象类型 (Object Datatype)**
    * `object`: 用于存储 JSON 对象。Elasticsearch 会自动展平对象，但会保留其结构关系。

* **嵌套类型 (Nested Datatype)**
    * `nested`: 类似于 `object` 类型，但会将数组中的每个对象作为独立隐藏文档进行索引。这允许您独立地查询数组中的对象，适用于对象数组的场景，以避免展平带来的问题。

**地理数据类型 (Geo Datatypes)**

* **地理坐标类型 (Geo-point Datatype)**
    * `geo_point`: 用于存储地理位置坐标（纬度和经度）。支持地理位置搜索，如查找附近的地点。

* **地理形状类型 (Geo-shape Datatype)**
    * `geo_shape`: 用于存储复杂的地理形状，如多边形。支持地理形状相关的查询，例如判断某个点是否在某个区域内。

**专业数据类型 (Specialized Datatypes)**

* **IP 地址类型 (IP Datatype)**
    * `ip`: 用于存储 IPv4 或 IPv6 地址。

* **Completion Suggester 类型 (Completion Suggester)**
    * `completion`: 一种优化前缀自动补全的类型。

* **Token Count 类型 (Token Count)**
    * `token_count`: 用于存储分析后字段中的词条数量。

* **Attachmnet 类型 (Attachment)**
    * `attachment`: (已废弃，推荐使用 Ingest Attachment Processor) 用于索引附件内容，如 PDF、Office 文档等。

* **Range 类型 (Range Datatypes)**
    用于存储范围数据：
    * `integer_range`
    * `float_range`
    * `long_range`
    * `double_range`
    * `date_range`
    * `ip_range`

* **Dense Vector 类型 (Dense Vector)**
    * `dense_vector`: 用于存储浮点数值的稠密向量，常用于机器学习和相似度搜索。

* **Sparse Vector 类型 (Sparse Vector)**
    * `sparse_vector`: 用于存储浮点数值的稀疏向量。

* **Search As You Type 类型 (Search As You Type)**
    * `search_as_you_type`: 优化 "边输入边搜索" 体验的文本类型。

* **Alias 类型 (Alias)**
    * `alias`: 为现有字段创建一个别名，不会索引实际数据。

* **Rank Feature 类型 (Rank Feature)**
    * `rank_feature`: 用于指示数字字段是否应该被视为一个特征来影响搜索结果的排名。

* **Rank Features 类型 (Rank Features)**
    * `rank_features`: 用于指示包含数字特征的对象字段是否应该被视为特征来影响搜索结果的排名。

* **Flattened 类型 (Flattened)**
    * `flattened`: 用于将整个 JSON 对象作为单个字段进行索引，保留原始结构但不可单独搜索子字段。

* **Shape 类型 (Shape)**
    * `shape`: 用于存储任意地理形状，功能比 `geo_shape` 更强大但索引开销也更大。

* **Point 类型 (Point)**
    * `point`: 用于存储 cartesian 坐标系统的点。

* **Shape 类型 (Shape)**
    * `shape`: 用于存储 cartesian 坐标系统的形状。

在创建索引映射 (Mapping) 时，需要为每个字段指定合适的数据类型。这将决定 Elasticsearch 如何存储和索引数据，以及如何对其进行搜索和分析。



使用Dev Tools 快速创建API key

```
# 创建拥有所有权限的 api key
PUT /_security/api_key
{
  "name": "all_access",
  "role_descriptors": {
    "all_access_role": {
      "cluster": ["all"],
      "index": [
        {
          "names": ["*"],
          "privileges": [	"all"]
        }
      ]
    }
  }
}

{
  "id": "df9rNJcB8Su0B327IzF1",
  "name": "all_access",
  "api_key": "-8S3TTCXRgmSR5Tlc1eytw",
  "encoded": "ZGY5ck5KY0I4U3UwQjMyN0l6RjE6LThTM1RUQ1hSZ21TUjVUbGMxZXl0dw=="
}

```



## Elasticsearch DSL(Python Library)

### 搜索

```python
from elasticsearch import Elasticsearch
from elasticsearch_dsl import Q, Search


# 创建客户端
client = Elasticsearch(
    hosts="http://localhost:9200",
    api_key="TU43Tk9KY0JPbWZac2FmQ0xRRW06VEZpbkJRTlF5d2lvQXRUcEJyWlZmdw==",
)

# 构造搜索条件
s = (
    Search(using=client, index="xxx")
    .filter(Q("term", knowledge_base_id=1))
    .query(
        Q(
            "bool",
            should=[
                Q("match", title={"query", "xxx"}),
                Q("match", content={"query", "xxx"}),
            ],
        )
    )
)

# 执行查询
response = s.execute()
```

