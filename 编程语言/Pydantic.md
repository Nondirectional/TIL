# Pydantic 学习笔记

## Field 功能说明

Field 是 Pydantic 库中的一个核心功能，用于为数据模型的字段提供额外的元数据和验证规则。

### 主要用途

1. **设置默认值**
2. **添加验证规则**
3. **提供字段描述**
4. **控制字段行为**

### 基本语法
```python
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str = Field(..., description="用户姓名", min_length=1, max_length=50)
    age: int = Field(default=18, ge=0, le=120, description="用户年龄")
    email: str = Field(None, regex=r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$')
```

### 常用参数

- **`...`**: 必填字段标识
- **`default`**: 默认值
- **`description`**: 字段描述
- **`min_length/max_length`**: 字符串长度限制
- **`ge/le/gt/lt`**: 数值范围限制（大于等于/小于等于/大于/小于）
- **`regex`**: 正则表达式验证
- **`alias`**: 字段别名

### 实际应用示例

```python
from pydantic import BaseModel, Field, ValidationError

class Product(BaseModel):
    id: int = Field(..., description="商品ID", gt=0)
    name: str = Field(..., min_length=1, max_length=100, description="商品名称")
    price: float = Field(..., ge=0, description="商品价格")
    in_stock: bool = Field(default=True, description="是否有库存")
    tags: list[str] = Field(default=[], description="商品标签")

# 使用示例
try:
    product = Product(
        id=1,
        name="iPhone 15",
        price=5999.99,
        tags=["手机", "Apple"]
    )
    print(product.model_dump())
except ValidationError as e:
    print(f"验证错误: {e}")
```

Field 让你能够精细控制数据验证和行为，是 Pydantic 强大功能的重要组成部分。