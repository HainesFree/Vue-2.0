# Vue知识点总结
## 一、Vue模板语法
### 1、插值
>数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值：
```
<div id="app">{{message}}</div>
```
> Mustache 标签将会被替代为对应数据对象上 message属性的值。无论何时，绑定的数据对象上 message属性发生了改变，插值处的内容都会更新。
### 2、JavaScript表达式
```
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
```
> 注意，只能在模板中写入表达式，而不能使用js语句，所以下面的写法会报错:
```
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}
<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```
## 二、指令
> 指令 (Directives) 是带有 v- 前缀的特殊属性。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

### 1）文本渲染
#### 1、v-text
>在html中插入数据，跟{{}}功能一致：
```
<div id="app">
    <h3 v-text="msg"></h3>
    <!-- 效果一致 -->
    <h3>{{msg}}</h3>
</div>
<script>
    new Vue({
        el:'#app',
        data:{
            msg:'shanghai'
        }
    });
</script>

```
#### 2、v-once
>只渲染元素和组件一次。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。
```
<div id="app">
    <span v-once>{{msg}}</span>
    <h3>{{msg}}</h3>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            msg:'shanghai'
        }
    });
</script>
```
> 在控制台中修改data中msg的值会发现，使用v-once命令的元素不再发生变化。
#### 3、v-html
>把文本内容进行解析，生成html结构并渲染。
```
<div id="app">
    <span>{{msg}}</span>
    <h3 v-html="msg"></h3>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            msg:'<h1>Hello Vue!!!</h1>'
        }
    });
</script>
```
### 2）class与style绑定
#### 1、v-bind
>v-bind指令可以绑定元素的属性，动态给属性赋值。比如：v-bind:class、v-bind:style、v-bind:href形式。

>v-bind的简写形式：v-bind: 简化为 `: `，上边的形式可以改写成：
`:class`、`:style`、`:href`
```
<div id="app">
    <!-- 默认title属性 -->
    <p title="我是提示">鼠标悬停可以到结果</p>
    <!-- bind绑定title属性 -->
    <p v-bind:title="tip">你不知道的秘密</p>
    <a v-bind:href="url">百度一下,你就知道</a>
    <!-- 缩写 -->
    <p :title="tip">你不知道的秘密</p>
</div>
<script> var app = new Vue({
        el:'#app',
        data:{
            tip:'密码: 8888',
            url:'http://www.baidu.com'
        }
    });
</script>
```
#### 2、class绑定
```
<div v-bind:class="{ active: isActive }"> 我是一些文本</div>
```
>绑定对象中active是css样式class的名称，isActive控制是否添加样式，true添加false不添加
> 绑定DOM对象的class样式有以下几种形式：
* 绑定多个class
* 使用对象classObject绑定
* 使用数组classList绑定
#### 3、style绑定
> 绑定形式跟class一致：
* 使用内联对象Object
* 直接传入对象styleObject
* 使用数组对象styleList
> 内联对象形式：
```
<div id="app">
    <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">我是文本</div>
</div>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            activeColor: 'red',
            fontSize: 30
        }
    });
</script>
```
> 直接传入对象styleObject：
```
<div v-bind:style="styleObject"></div>

data: {
    styleObject: {
        color: 'red',
        fontSize: '13px'
    }
}
```
>直接传入数组array：
```
<div id="app">
    <p v-bind:style="[baseStyle, showStyle]"> 
    我是文本
    </p>
</div>
<script>
```
```
var app = new Vue({
        el: '#app',
        data: {
            baseStyle: {
                margin:0,
                padding:0
            },
            showStyle:{
                color:'green',
                fontSize:'24px'
            }
        }
    });
</script>
```
### 3）事件处理
#### 1、监听事件
> 记住：v-on就可以了：
```
<div id="app">
    <!-- v-on指定绑定事件 -->
    <button v-on:click="count++">增加+1</button>
    <!-- @简化方式 -->
    <button @click="count--">减少-1</button>
    <p>点击次数:{{count}}</p>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            count:0
        }
    });
</script>
```
#### 2、方法事件处理器
> 一般来说事件会绑定一个方法，在方法内部处理相关的事件逻辑。需要在methods属性中定义方法，然后v-on引用对应相关的方法名。
```
<div id="app">
    <!-- greet是在下面methods中定义 -->
    <button v-on:click="greet">greet</button>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            greetText:'Hello Vue!'
        },
        //定义组件方法
        methods:{
            greet: function () {
                //data中的数据都可以使用this来访问
                alert(this.greetText);
            }
        }
    });

    //方法也可以主动调用
    app.greet();
</script>
```
#### 3、$event对象
> 在事件处理函数中访问DOM原生事件event对象，可以使用特殊变量$event对象传入。
```
<div id="app">
    <!-- 传入$event -->
    <button v-on:click="greet($event)">greet</button>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            greetText:'Hello Vue!'
        },
        //定义组件方法
        methods:{
            //使用event对象
            greet: function (event) {
                console.log(event.target.tagName);//BUTTON
            }
        }
    });
</script>
```
#### 4、事件修饰符
> 在事件处理程序中调用 event.preventDefault() 或 
event.stopPropagation() 是非常常见的需求。为了解决这个问题，Vue.js 为 v-on 提供了事件修饰符。通过由点 (.) 表示的指令后缀来调用修饰符。
* stop ： 阻止event冒泡，等效于event.stopPropagation()
* prevent ： 阻止event默认事件，等效于event.preventDefault()
* capture ： 事件在捕获阶段触发
* self ： 自身元素触发，而不是子元素触发
* once ： 事件只触发一次
#### 5、键值修饰符
> 在监听键盘事件时，我们经常需要监测常见的键值。Vue 允许为 v-on 在监听键盘事件时添加关键修饰符：
```
<!-- 只有在 keyCode 是 13 时调用 submit() -->
<input v-on:keyup.13="submit">
```
> 记住所有的 keyCode 比较困难，所以 Vue 为最常用的按键提供了别名：
```
<!-- 同上 -->
<input v-on:keyup.enter="submit">
```
### 4）条件渲染
#### 1、v-if
> 根据if绑定值得true或者false进行渲染
```
<div id="app">
    <h3 v-if="seen">你能看见我!</h3>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            seen:true
        }
    });
</script>
```
#### 2、v-else
> 需要配合v-if使用，显示一个相反的情况。
```
<div id="app">
    <button @click="seen=!seen">切换</button>
    <p>
        <span v-if="seen">你能看见我!seen: {{seen}}</span>
        <span v-else>seen: {{seen}}</span>
    </p>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            seen:true
        }
    });
</script>
```
#### 3、v-else-if
```
<div id="app">
    <h3 v-if="age == 12">年龄:12</h3>
    <h3 v-else-if="age == 18">年龄:18</h3>
    <h3 v-else>不是12和18</h3>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            age: 18
        }
    });
</script>
```
#### 4、v-show
```
<div id="app">
    <h3 v-show="seen">你能看见我!</h3>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            seen:true
        }
    });
</script>
```
#### 5、v-if 和 v-show区别
> 两者的区别是v-if是“真正”的条件渲染，只有为true时才会被渲染。v-if也是“惰性”的，假如初始条件为false，那么条件变为true时才会发生第一次渲染。
v-show只是CSS的display属性的切换，不论初始条件是否成立，都会进行html渲染，然后改变display的值为none或者block。
一般来说v-if开销比较大，如果需要频繁切换显示不显示使用v-show，需要进行条件判断的但是改变很少的使用v-if。
### 5）列表渲染
#### 1、v-for
> 可以把一组值进行列表渲染，语法形式： item  in items，items 是源数据数组并且 item 是数组元素迭代的别名。
```
<div id="app">
    <ul>
        <li v-for="item in items">{{item.name}}</li>
    </ul>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            items:[
                {name:'西瓜'},
                {name:'苹果'},
                {name:'菠萝'}
            ]
        }
    });
</script>
```
> v-for也可以遍历对象，可以指定三个形参:
```
形式: v-for="(value, key, index) in object"
```
* value: 对象的值
* key: 对象的键 
* index: 遍历的索引
```
<div id="app">
    <ul>
        <li v-for="(value, key, index) in person">
            {{index}} == {{key}} == {{value}}
        </li>
    </ul>
</div>

<script>
    var app = new Vue({
        el:'#app',
        data:{
            person:{
                name:'Tim',
                age:12,
                love:'music'
            }
        }
    });
</script>
```
#### 2、key属性
> 用 v-for渲染列表时， 使用key属性给每个指定一个唯一的ID表示，那么可以在下次数据渲染时，提高渲染速度。它默认用“就地复用”策略。
```
<div id="app">
    <ul>
       <li v-for="item in items" :key="item.id">{{item.name}}</li>
    </ul>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            items:[
                {id:1,name:'西瓜'},
                {id:2,name:'苹果'},
                {id:3,name:'菠萝'}
            ]
        }
    });
</script>
```
#### 3、取值范围
```
<div id="app">
    <ul>
        <li v-for="i in 5">第 {{i}} 次</li>
    </ul>
</div>
```
### 6）表单输入绑定
> 用 v-model 指令在表单控件元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。
#### 1、文本 text
```
<div id="app">
    <input v-model="message" placeholder="输入..."/>
    <p>message: {{message}}</p>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            message:''
        }
    });
</script>
```
#### 2、复选框
> 单个使用时v-model是逻辑值：true和false，多个一起使用需要指定value值，选中结果绑定v-model的数组。
```
<div id="app">
    <input type="checkbox" v-model="checked">
    <p>是否选中: {{checked}}</p>
    <hr>
    <input type="checkbox" value="HTML5" v-model="lesson">
    <label>HTML5</label>
    <input type="checkbox" value="JavaScript" v-model="lesson">
    <label>JavaScript</label>
    <input type="checkbox" value="Vue" v-model="lesson">
    <label>Vue</label>
    <p>多选结果：{{lesson}}</p>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            checked:'',
            lesson:[]
        }
    });
</script>
```
#### 3、单选框
```
<div id="app">
    <input type="radio" value="yes" v-model="love">
    <label>喜欢</label>
    <input type="radio" value="no" v-model="love">
    <label>不喜欢</label>
    <p>是否喜欢: {{love}}</p>
</div>
<script>
    var app = new Vue({
        el:'#app',
        data:{
            love:''
        }
    });
</script>
```
#### 4、选择列表
```
<div id="app">
    <select v-model="selected">
        <option>西瓜</option>
        <option>苹果</option>
        <option>菠萝</option>
    </select>
    <h3>结果: {{selected}}</h3>
</div>
<script> var app = new Vue({
        el:'#app',
        data:{
            selected:''
        }
    });
</script>
```
> 使用v-for指令渲染列表，增加选项的value并指定默认值。
```
<div id="app">
    <select v-model="selected">
        <option v-for="op in list" v-bind:value="op.value">{{op.text}}</option>
    </select>
    <h3>结果: {{selected}}</h3>
</div>
<script> var app = new Vue({
        el:'#app',
        data:{
            selected:'2',
            list:[
                {text:'西瓜', value:'1'},
                {text:'苹果', value:'2'},
                {text:'菠萝', value:'3'}
            ]
        }
    });
</script>
```
#### 5、修饰符
* .lazy：绑定数据默认实时更新，lazy可以在onChange触发
* .number：返回数字类型的值，转换不成返回NaN
*  .trim：去除数据的前后空格
```
<div id="app">
    <p><input type="text" v-model.lazy="name"></p>
    <p><input type="text" v-model.number="age"></p>
    <p><input type="text" v-model.trim="cont"></p>
    <p><button @click="show();">显示值</button></p>
</div>
var app = new Vue({
    el: '#app',
    data: {
        name:'hello',
        age:12,
        cont:''//   shang  hai
    },
    methods:{
        show:function(){
            console.log(this.name, this.age, this.cont);
            console.log(typeof this.age);//number
        }
    }
});
```











