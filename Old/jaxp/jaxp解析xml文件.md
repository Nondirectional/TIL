# Jaxp解析xml文件

按照解析的类型可以把jaxp解析分成两种方式....

- 层级结构解析:Dom
- 事件驱动解析:Sax

## Dom方式解析

使用这种方式解析是讲xml文件的整个结构加载到内存当中的，把标签，属性，内容都包装成对象

### 基本实现

```java
import javax.xml.parsers.*;

public class dom_parser{
    public static void main(String[] args) throws Exception {
        DocumentBuiderFactory dbf = DocumentBuiderFactory.newInstance();
        DocumentBuider db = dbf.newDocumentBuider();
        Document d = db.parse(/src/test.xml);
        NodeList nl = d.getElementByTagName("name");
        for(int i = 0;i<nl.getLength();i++){
            System.out.println(nl.item(i).getTextContent());
        }
    }
}
```

1. `DocumentBuiderFactory dbf = DocumentBuiderFactory.newInstance();` 创建dom解析器工厂实例
2. `DocumentBuider db = dbf.newDocumentBuider();` 创建dom解析器实例
3. `Document d = db.parse(/src/test.xml);` 获得解析之后的doucment对象

上面所说的三个步骤是DOM解析的基本步骤

剩余的是使用Document对象的getElementByTagName()方法获取标签名称为name的所有标签对象，然后遍历所有标签名称为name的所有标签对象的内容

此外,还可以使用Document对象的getElementById()方法根据名称为ID的属性内容获取标签的对象 

### 对整个xml文档的遍历

首先需要了解，Document类是Node的派生类，一个Document对象是整个xml文档的最高层标签对象,其子标签就是xml文档的根标签

在这个例子当中，我们使用的递归的方式对整个文档进行遍历

```java
public static void listDocument(Node document) {
	NodeList nl = document.getChildNodes();
	for(int i = 0;i<nl.getLength();i++) {
		if(nl.item(i).getNodeType()==Node.TEXT_NODE) 
			System.out.println(nl.item(i).getTextContent());
		else			
			System.out.println(nl.item(i).getNodeName());
		listDocument(nl.item(i));
	}
	}
```

将Doucment对象传入改方法就能够实现对xml文档的简单遍历



### 对文档节点的增删改查

我们要对节点进行增删操作，第一步，我们需要拿到需要操作节点的父节点，然后使用父节点的方法对他的子节点进行操作，我们需要对节点进行内容修改操作，就要拿到想要修改的那个节点的对象

#### 修改节点内容

```java
public static void modifyNodeContent(Document d,String nodeName,String modifyContent) {
	NodeList nl = d.getElementsByTagName(nodeName);
	for(int i = 0;i<nl.getLength();i++) 
		nl.item(i).setTextContent(modifyContent);
	}
```

#### 添加节点

```java
	public static void addChildNode(Document d) {
		Node parentNode = d.getChildNodes().item(0);
        
		Node pname = d.createElement("psn:name");
		Node fname = d.createElement("psn:firstName");
		Node lname = d.createElement("psn:lastName");
		Node age = d.createElement("psn:age");
        
        
		age.setTextContent("19");
		fname.setTextContent("Zhang");
		lname.setTextContent("San");
		pname.appendChild(fname);
		pname.appendChild(lname);
		parentNode.appendChild(pname);
		parentNode.appendChild(age);
	}
```

我们添加节点首先需要做的是把需要添加的节点创建出来，然后使用欲操作的父节点的appenChild()方法把节点追加到这个节点当中，*注意* 是**追加** ，而不是插入!

#### 删除节点

```java
Node n = doc.getElementsByTagName("psn:firstName").item(0);
Node pn = n.getParentNode();
pn.removeChild(n);
```

删除节点的操作：

- 获取到欲要删除的节点对象
- 获取余姚删除的节点对象的父节点对象
- 调用父节点对象的removeChild()方法，欲要删除的节点对象作为参数



#### 文档回写

```java
TransformerFactory tf = TransformerFactory.newInstance();
Transformer t = tf.newTransformer();
t.transform(new DOMSource(document),new StreamResult("Person.xml"));
```

- DOMSource的构造方法接受的参数是xml文档的Document对象
- StreamResult的构造方法接受的参数是xml文档的File对象或者文件路径



## Sax方式解析

时间驱动型，一边读取xml文档，同时做解析工作

### 基本实现



```java
public class sax_parse {
	public static void main(String[] args) throws ParserConfigurationException, SAXException, IOException {
		SAXParserFactory spf = SAXParserFactory.newInstance();
		SAXParser sp = spf.newSAXParser();
		sp.parse("src/com/nondirectional/Person.xml", new myDefaultHandler());
	}
}

class myDefaultHandler extends DefaultHandler{

	@Override
	public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
		// TODO Auto-generated method stub
		System.out.print("<"+qName+">");
	}
	
	@Override
	public void characters(char[] ch, int start, int length) throws SAXException {
		// TODO Auto-generated method stub
		System.out.print(new String(ch, start, length));
	}

	@Override
	public void endElement(String uri, String localName, String qName) throws SAXException {
		// TODO Auto-generated method stub
		System.out.print("</"+qName+">");
	}
```



Sax解析器解析文件的时候自动触发DefaultHandler类里面的下面三个方法进行解析

- startElement()是遇到开始标签时触发的方法，

- endElement()是遇到结束标签时触发的方法，

- characters()是遇到文本内容的时候触发的方法，其中的参数ch是整个xml文档,start是遇到的文本内容在xml文档当中的开始位置，length是遇到的文本内容的长度

  