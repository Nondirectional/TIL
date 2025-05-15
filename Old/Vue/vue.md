## 声明式渲染

vue:

```js
const app01 = new Vue(
{
    el: "#demo01"
    // 目标元素
    data: 
    	msg: "Hello World!"
    //数据
    methods: 
    	method01: function (){
    		windows.alert("Hello Wordld");	
	}
    //方法
}
)
```

html:

```html
<div id="#demo01">
    <p>
        {{msg}}
    </p>
    <button v-on:click="method01">
        Click here to alert
    </button>
</div>
```

## 绑定：

单向绑定`v-bind="data"`

双向绑定`v-model="data"`



## 事件监听：

v-on:click="xxx()"监听点击事件@click="xxx()"等效



自定义组件

```html
<body>
	<div class="div01">
		<ol>
			<todo-item 
			v-for="item in todoList"
			v-bind:todo="item"
			v-bind:key="item.id"></todo-item>
             <!--
			v-for  从app01中的todoList中取出数据到item
				v-for="item in todoList"
			v-bind:todo  把每一个item绑定到自定义组件todo-item中的todo属性中
				v-bind:todo="item"
			然后自定义组件中todo.content取出数据作为标签体内容
			-->
		</ol>
	</div>
</body>
<script src="js/vue.js"></script>
<script>
	Vue.component("todo-item",{
		template: '<li>{{ todo.content }}</li>',
		props: ["todo"]
	})
		
	const app01 = new Vue({
		el: ".div01",
		data: {
			todoList: [
				{id:0,content:"Wake up"},
				{id:1,content:"Eat"},
				{id:2,content:"Dink"},
				{id:3,content:"Early to bed"}
			]
		}
	})
</script>
```



## 生命周期

Vue实例的生命周期如下图

![](.\images\Home_selected.png)

在生命周期的各个阶段，可以给每个阶段绑定一个函数如created

```html
<script>
	var vm = new Vue({
        created: function(){
            console.log("created");
        }
    })
</script>
```

需要注意，绑定的函数建议不要使用箭头函数，如果使用箭头函数的话是无法使用this关键字的，即在方法体内无法拿到当前实例的引用



仅加载数据一次

使用v-once指定数据仅加载一次，即数据加载到页面当中后，数据发生改变也不会到影响加载到页面中的数据



输出html标签

v-html，绑定包含html标签的字符串数据 

```html
<body>
	<div class="div01">
			<p v-html="htmlstr"></p>
        	<span v-html="htmlstr"></span>
	</div>
</body>
<script src="js/vue.js"></script>
<script>
	const vm = new Vue({
		el: ".div01",
		data:{
			htmlstr: '<span style="font-color:red">Hello World!</span>'
		}
    })
</script>
```

## 计算属性：

```html
    <div id="app">
        {{this.msg}}
        {{msg2}}
        <button @click="say"></button>
    </div>

    <script>
        let app = new Vue({
            el:"#app",
            data(){
                return {
                    msg:"Hello"
                }
            },
            methods:{
                say(){
                    console.log(this.msg)
                }
            },
            computed:{
                msg2(){
                    return "World!"
                }
            }
        })     
    </script>
```

所谓计算属性就是computed当中定义的属性，其值为方法的返回值，访问用方法名访问。

但是通过这种方式只能够通过这个应用其计算的到的值，如果需要对该属性复制还需要定义它的set方法

```html
    <div id="app">
        First Name:<input type="text" v-model="firstName"/><br/>
        Last Name:<input type="text" v-model="lastName"/><br/>
        Full Name:<input type="test" v-model="fullName"/>
    </div>

    <script>
        let app = new Vue({
            el:"#app",
            data(){
                return {
                    msg:"Hello",
                    firstName:"",
                    lastName:""
                }
            },
            computed:{
                msg2(){
                    return "World!"
                },
                fullName:{
                    get(){
                        return this.firstName+this.lastName
                    },
                    set(value){
                        msg3 = value
                    }
                } 
            }
        })     
    </script>
```

## 监听：

监听和计算属性一样，是用方法来绑定属性，但是和计算属性不同的是，计算属性是一个方法即为一个属性。

而监听一个方法对应一个属性的监听方法，这个监听方法会在所监听的属性值发生改变的时候触发。

```html
<div id="app">
        <input type="text" v-model="firstName"/><br/>
        <input type="text" v-model="lastName"/><br/>
        <input type="text" v-model="fullName"/><br/>
    </div>
    
    <script>
        let app = new Vue({
            el:"#app",
            data(){
                return {
                    firstName:"",
                    lastName:""
                }
            },
            computed:{
                fullName:{
                    get(){
                        return this.firstName+" "+this.lastName
                    },
                    set(value){
                        fullName = value
                    }
                }
            },
            watch:{
                firstName(){
                    console.log("firstName 变量发生了变化...")
                },
                lastName(){
                    console.log("lastName 变量发生了变化...")
                },
                fullName(){
                    console.log("fullName 变量发生了变化...")
                }	
            }
        })
    </script>
```

## 条件渲染：

条件渲染是根据条件渲染某些元素，有两种方式：

1.v-if

v-if的原理是根据条件来创建或删除元素

2.v-show

v-show的原理是根据条件来选择显示元素或不显示元素，用display:"none"属性来实现。即如果条件不成立儿则给元素添加display:"none"属性

```html
    <div id="app">
        <input type="checkbox" v-model="isShow"/>
        <p v-if="isShow">
             v-if：true
        </p>
        <p v-show="isShow">
            v-show:true
        </p>
        <p v-show="!isShow">
            v-show:false
        </p>
    </div>
    <script>
        let app = new Vue({
            el:"#app",
            data(){
                return{
                    isShow:true
                }
            }
        })
    </script>
```

## 列表渲染

```html
<div id="app">
        <p v-for="user in userList">
            姓名:{{user.name}},年龄:{{user.age}}
        </p>

        <p v-for="(user,index) in userList">
            序号：{{index+1}},姓名:{{user.name}},年龄:{{user.age}}
        </p>
    </div>
    <script>
        let app = new Vue({
            el:"#app",
            data(){
                return{
                    userList:[
                        {name:"张三",age:16},
                        {name:"李四",age:17}
                    ]
                }
            }
        })
    </script>
```

