# Schema约束简单入门



创建的schema文件的后缀名为`.xsd`

## Schema元素

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
		targetNamespace="http://www.example.org/Person"
		elementFormDefault="qualified" attributeFormDefault="qualified">
	<element name="Person">
		<complexType>
			<sequence>
				<element name="name" type="string"></element>
				<element name="age" type="int"></element>
			</sequence>
		</complexType>
	</element>
</schema>
```

- `xmlns="http://www.w3.org/2001/XMLSchema"`表示当前xml文件是一个约束文件,指定默认的**命名空间**
- `targetNamespace="http://www.example.org/Person"`说明当前xsd文件约束的文件的命名空间
- `elementFormDefault="qualified"` 说明元素是否严格使用命名空间
- `attributeFormDefault="qualified" ` 说明属性是否严格使用命名空间 



## 引入Schema

在xml的根文件里面添加一系列特殊的属性

```xml
<?xml version="1.0" encoding="UTF-8"?>
<psn:Person 
	xmlns:psn="http://www.example.org/Person"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.example.org/Person Person.xsd">
	<psn:name>Hari</psn:name>	
	<psn:age>19</psn:age>
</psn:Person>
	
```

* `xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"` 表示当前的xml文件是一个被约束文件
* `xmlns:psn="http://www.example.org/Person"` 相当于schema文件里面的targetNamespace,在这里我们给这个命名空间取了别名psn
* `xsi:schemaLocation="http://www.example.org/Person Person.xsd"` targetNamespace 空格  约束文档的地址路径

## 简单的元素

```xml
<element name="xxx" type="xxx"></element>
```

- name:元素的名称
- type:元素的数据类型

## 元素默认值或固定值

```xml
<element name="xxx" type="xxx" default="默认值"></element>
<element name="xxx" type="xxx" fixed="固定值"></element>
```

- 默认值是在没有填写值的情况下默认的值
- 固定值是固定内容为fixed里面的内容不可更改



## 元素属性

1. 简单元素没有元素属性
2. 只有复杂元素才有元素属性
3. 写在` </complexType>` 之前



```xml
<attribute name="ID" type="int" use="required"></attribute> 
```

- name:属性名称
- int:属性数据类型
- use="required":属性必须出现

而且属性还能够设置默认值和固定值，参考元素的默认值和固定值

## 内容限定

限定（restriction）用于为 XML 元素或者属性定义可接受的值。对 XML 元素的限定被称为 facet

对于某些可以被正则表达式限定取代的限定，这里就不多做描述了

### 值的上限下限

下面例子定义了一个叫age的元素，限制其值在0-120之间

```xml
<element name="age">
	<simpleType>
    	<restriction base="integer">
        	<minInclusive value="0"></minInclusive>
            <maxInclusive value="12"></maxInclusive>
        </restriction>
    </simpleType>
</element>
```



### 枚举限定

```xml
<element name="foodType">
	<simpleType>
    	<restriction base="string">
        	<enumeration value="apple"></enumeration>
            <enumeration value="banana"></enumeration>
            <enumeration value="orange"></enumeration>
        </restriction>
    </simpleType>
</element>
```



### 正则表达式约束

```xml
<element name="telephoneNumber">
	<simpleType>
    	<restriction base="string">
        	<pattern value="[\d]{11}"></pattern>
        </restriction>
    </simpleType>
</element>
```



### 对空白字符的限定

如需规定对空白字符（whitespace characters）的处理方式，我们需要使用 whiteSpace 限定。

下面的例子定义了带有一个限定的名为 "address" 的元素。这个 whiteSpace 限定被设置为 "preserve"，这意味着 XML 处理器不会移除任何空白字符：

```xml
 <element name="address">
  <simpleType>
     <restriction base="string">
       <whiteSpace value="preserve"/>
     </restriction>
   </simpleType>
</element> 
```



这个例子也定义了带有一个限定的名为 "address" 的元素。这个 whiteSpace 限定被设置为 "replace"，这意味着 XML 处理器将移除所有空白字符（换行、回车、空格以及制表符）：

```xml
 <element name="address">
  <simpleType>
     <restriction base="string">
       <whiteSpace value="replace"/>
      </restriction>
   </simpleType>
</element> 
```

## 复合类型

 可以使用已有的元素组成复合类型

### 只继承不扩展

```xml
<element name="person" type="personInfo"></element>

<complexType name="personInfo">
	<sequence>
        <element name="name" type="string"></element>
        <element name="age" type="int"></element>
    </sequence>
</complexType>
```

下面那一部分的内容就是复合类型

`<complexType name="personInfo">`说明复合类型的名称为*personInfo* 

`<element name="person" type="personInfo">` 表示映入复合类型*personInfo* 

### 继承且扩展

```xml
<element name="employee" type="supPersonInfo"></element>

<complexType name="personInfo">
	<sequence>
        <element name="name" type="string"></element>
        <element name="age" type="int"></element>
    </sequence>
</complexType>

<!--继承扩展内容-->
<complexType name="supPersonInfo">
<complexContent>
	<extension base="personInfo">
        <!--扩展内容-->
		<element name="address" type="string"></element>
        <element name="telephoneNumber" type="string"></element>
    </extension>
</complexContent>
</complexType>
```

`<complexType name="supPersonInfo">` 说明复合类型名称

`<extension base="personInfo">` 说明所继承的复合类型

## 定义空元素的三种方法

### 第一种

```xml
<element name="emptyElement">
<complexType>
	<attibute name="ID" type="string"></attibute>    
</complexType>
</element>
```

复杂元素需要定义内含的元素

但是complexType标签下没有定义元素

所以这个元素内容只能为空

### 第二种

```xml
<element name="emptyElement">
	<complexType>
    	<complexContent>
        	<restriction base="integer">
            	<attribute name="ID"></attribute>
            </restriction>
        </complexContent>
    </complexType>
</element>
```

complexContent表明下面将要对元素进行限制

restriction把元素限制成只能为integer类型

complex元素不能有内容,限制成integer不能有标签

两者叠加则为空元素

### 第三种

```xml
<element name="emptyElement" type="extendElement"></element>

<complexType name="extendElement"></complexType>
```

参考第一种和第二种就能够理解的了了

