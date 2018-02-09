---
title: Vue官网todoMVC示例
copyright: true
date: 2018-01-28 02:14:14
categories:
- vue
- demo
tags:
- vue
---


这个示例是模仿官网示例样式和功能用我自己的方式写的，基本上没有看官网的源码，只参考自定义指令。让我们一步步来探讨一下。[官网demo](https://cn.vuejs.org/v2/examples/todomvc.html)

# 要实现的功能
1. 单条添加todo
2. 单条删除todo
3. 双击编辑todo
4. 单条todo已完成相应样式状态改变
5. 全部todo是已完成相应样式状态改变
6. 清除全部已完成todos
7. 待办todos数量显示
8. 所有todos，已完成todos，未完成todos筛选

![todoMVC.gif](https://i.loli.net/2018/01/29/5a6dfccfd4ea2.gif)
<!--more-->
# 单条添加todo

~~~html
<input type="text" class="todos_add" placeholder="What needs to be done?" 
@keyup.enter="addTodo($event.target)" //绑定enter事件
ref="currentInput">//操作input元素使enter一下之后清空输入框内容
~~~

~~~javascript
data() {//一些初始化数据
	return {
		todolists: [],
		dataStatus: ["All", "Active", "Completed"],
		dataStatusIndex: 0,
		whichShow: true,
		defaultShow: true
	}
},
~~~

~~~JavaScript
addTodo(e) { //添加todo
	var val = e.value
	if (val === "") {return} //如果输入内容为空则立即返回
	this.todoLists = this.todoLists.concat({//使用concat这个api添加todo
		value: val, //输入内容
		isEditing: false, //是否在编辑状态
		isActive: false, //删除X图标是否激活
		isChecked: false //是否已完成
	})
	this.$refs.currentInput.value = "" //按下enter添加todo之后把输入框value清零
	window.localStorage.setItem("content",JSON.stringify(this.todoLists))//使用localStorage以JSON格式存储数据
},
~~~

把每条todo的对应状态都存在同一个对象当中，在操作改变todo状态的时候不会被统一处理，使得每条todo都有单独的状态。

# 单条删除todo

~~~html
 <li  class="content_todoList"
 v-for="(list,index) in todoLists" 
 @mouseover="list.isActive = true" //鼠标移入todo改变对应todo的isActive状态
 @mouseleave="list.isActive=false" //鼠标移出todo改变对应todo的isActive状态
	<span class="el-icon-close content_todoList_delete" 
	:class="{show: list.isActive}" //动态绑定class使鼠标移动到某一todo显示X图标
	@click="deleteTodo(index)"> //绑定删除单条todo事件
	</span>
</li>
~~~

~~~JavaScript
deleteTodo(index) { //删除单条todo
		this.todoLists.splice(index, 1)//使用splice的api
		window.localStorage.setItem("content",JSON.stringify(todoLists)) //以JSON格式存储数据//localStorage存储数据
},
~~~

在每个`li`元素上绑定了鼠标移入和移除的事件来动态的改变每个todo的isActive，然后再使用isActive动态显示class。

# 双击编辑todo&&单条todo已完成

~~~html
<input type="checkbox" class="checkBox" 
v-model="list.isChecked">//双向绑定isChecked

<div class="content_todoList_main" 
@dblclick="toEdit(list)" //双击事件
v-show="!list.isEditing" //切换元素
:class="{deleted:list.isChecked}"> //动态绑定class该表已完成todo样式
{{list.value}}
</div>

<input type="text" class="content_todoList_main main_input" 
v-model="list.value" //双向绑定可输入value
v-show="list.isEditing" //切换元素
v-todo-focus="list.value" //自定义指令，双击之后自动focus对焦
@blur="unEdit(list)"> //绑定blur失去焦点事件
~~~

~~~JavaScript		
methods: {
	toEdit(obj) { //使添加的todothing可编辑
		obj.isEditing = true
	},
	
	unEdit(obj) { //使添加的todothing不可编辑
		obj.isEditing = false
	},
}

directives: { //自定义focus指令,需要一个binding参数做判断
	"todo-focus": function (el, binding) {
		if (binding.value) {
			el.focus()
		}
	}
}
~~~

通过每个todo里的isEditing属性控制show和是否可编辑两个状态，双击div之后改变当前todo的isEditing为true，从而显示为input，input失去焦点之后再通过blur事件改为false。
通过todo的idChecked来控制是否已完成，从而动态改变样式。

# 全部todos已完成

~~~html
<span 
class="icon-down el-icon-arrow-down" //使用element库做向下箭头icon
v-show="todoLists.length>0" //通过todoLists控制是否显示向下箭头icon
@click="selectAllTodos"></span> //全部已完成事件
~~~

~~~JavaScript
selectAllTodos() { //设置所有todo为已完成,使用map的api通过todo的isChecked属性操作
	this.todoLists.map(todo => todo.isChecked = todo.isChecked ? false : true)
}
~~~

# 待办todos数量显示
~~~html
<div class="data_times" v-show="times === 0"> //times为0显示item，大于0显示items，细节注定成败
	<span>{{times}}</span>&nbsp item left
</div>
<div class="data_times" v-show="times > 0">
<span>{{times}}</span>&nbsp items left</div>
~~~

~~~JavaScript
computed: {
	times() { //使用计算属性计算待办todos的次数 
		let todoArr = this.todoLists
		let times = 0
		for (let i = 0; i < todoArr.length; i++) {
			if (todoArr[i].isChecked === false) {
				times++
			}
		}
		return times
	}
},
~~~

使用了计算属性对todoLists计算，用for循环刷选出idChecked为true的累加，最后返回times。
# 清除全部已完成todos

~~~html
<div class="data_clearTodos" 
@click="clearTodos" 
v-show="times < todoLists.length"> //如果待办事件次数小于总todoLists长度就显示“clear completed”
	<a href="#">clear completed</a>
</div>

<div class="data_clearTodos" 
@click="clearTodos" 
v-show="times === todoLists.length"> //如果待办事件次数等于总todoLists长度就显示“clear completed”
	<a href="#"></a>
</div>
~~~


~~~JavaScript
clearTodos() { //清空已完成的todoLists，使用filter的api进行筛选
	this.todoLists = this.todoLists.filter(todo => todo.isChecked === false)
	window.localStorage.setItem("content",JSON.stringify(this.todoLists)) //以json格式存储数据
},
~~~

如果待办todos的times小于todoLists长度，就证明有已完成的todo，就可以显示“clear completed”，如果相等就说明没有已完成的todo。

# 三种状态筛选
所有todos，已完成todos，未完成todos筛选

~~~html
<li class="content_todoList" 
v-show="defaultShow || (whichShow?list.isChecked:!list.isChecked)">

<div class="data_status">
	<a href="#" 
	:class="{active: index === dataStatusIndex}" //动态class实现tab切换
	v-for="(item ,index) in dataStatus" v-for循环
	@click="switchStatus(index)" //切换不同tab显示内容
	:key="index">
		{{item}}
	</a>
</div>
~~~

~~~JavaScript
switchStatus(index) { //通过if条件判断操作
	this.dataStatusIndex = index
	if (this.dataStatus[index] === "Active") {
		this.defaultShow = false
		this.whichShow = false
	} else if (this.dataStatus[index] === "Completed") {
		this.defaultShow = false
		this.whichShow = true
	} else if (this.dataStatus[index] === "All") {
		this.defaultShow = true
	}
},
~~~

我这里是同时if条件句判断操作，有点麻烦，目前还没有想出来其他方案。在`li`元素使用三元运算符和或运算符进行操作显示不同状态的todos。

# 完整代码

~~~css
<style>
	* {
		padding: 0;
		margin: 0;
		box-sizing: border-box;
	}

	input {
		outline: none;
	}

	ul,
	li,
	ol {
		list-style: none;
	}

	#app {
		width: 800px;
		height: 900px;
		margin: 0 auto;
		text-align: center;
		background-color: rgb(245, 245, 245);
		padding: 24px 0;
	}

	#app .header {
		font-size: 48px;
	}

	.content {
		width: 72%;
		margin: 0 auto;
		box-shadow: 0 3px 3px 2px rgba(0, 0, 0, 0.25);
		position: relative;
	}

	.icon-down {
		position: absolute;
		font-size: 24px;
		top: 16px;
		left: 16px;
		cursor: pointer;
	}

	#app .content .todos_add {
		width: 100%;
		height: 56px;
		padding: 16px 56px;
		font-size: 24px;
		border: 1px solid transparent;
	}

	.content_todoLists {
		position: relative;
		z-index: 3;
	}

	.content_todoList {
		display: flex;
		flex-direction: row;
		border-top: 1px solid #ccc;
		font-size: 24px;
		padding: 8px;
		background-color: white;
		align-items: center;
	}

	.checkBox {
		width: 20px;
		height: 20px;
		margin-left: 10px;
	}

	.content_todoList_main {
		flex: 1;
		text-align: left;
		margin-left: 16px;
		font-size: 20px;
		padding: 6px 0;
	}

	.main_input {
		position: relative;
		z-index: 1;
	}

	.content_todoList_delete {
		position: absolute;
		right: 16px;
		color: rgb(252, 55, 55);
		font-weight: 500;
		display: none;
		cursor: pointer;
	}

	.show {
		display: block;
	}

	.deleted {
		text-decoration-line: line-through;
		color: #bbb;
	}

	.show:hover {
		color: rgb(255, 0, 0);
		font-weight: 700;
	}

	::-moz-placeholder {
		color: rgb(221, 218, 218);
	}

	::-webkit-input-placeholder {
		color: rgb(221, 218, 218);
	}

	:-ms-input-placeholder {
		color: rgb(221, 218, 218);
	}

	.data {
		display: flex;
		justify-content: space-between;
		padding: 8px;
		font-size: 14px;
		font-weight: 300;
		color: rgb(145, 145, 145);
	}

	a {
		text-decoration: none;
		color: rgb(145, 145, 145);
	}

	.data_times {
		width: 73px;
	}

	.data_clearTodos {
		width: 142px;
	}

	.data_status a {
		display: inline-block;
		border: 1px solid transparent;
		border-radius: 2px;
		padding: 1px 4px;
		margin: 0 2px;
	}

	.data_status a:hover {
		border-color: #bbb;
	}

	.data_clearTodos a:hover {
		text-decoration-line: underline;
	}

	.active {
		box-shadow: 0 0 1px black;
	}
</style>
~~~

~~~html
<body>
	<div id="app">
		<header class="header">todos</header>
		<div class="content">
			<span class="icon-down el-icon-arrow-down" 
			v-show="todoLists.length>0" 
			@click="selectAllTodos">
			</span>
			<input type="text" class="todos_add" placeholder="What needs to be done?" 
			@keyup.enter="addTodo($event.target)" 
			ref="currentInput">
			<ul class="content_todoLists">
				<li v-for="(list,index) in todoLists" class="content_todoList" 
				@mouseover="list.isActive = true" 
				@mouseleave="list.isActive=false"
				v-show="defaultShow || (whichShow?list.isChecked:!list.isChecked)">
					<input type="checkbox" class="checkBox" v-model="list.isChecked">
					<div class="content_todoList_main" @dblclick="toEdit(list)" v-show="!list.isEditing" :class="{deleted:list.isChecked}">
						{{list.value}}
					</div>
					<input type="text" class="content_todoList_main main_input" 
					v-model="list.value" 
					v-show="list.isEditing" 
					v-todo-focus="list.value"
					@blur="unEdit(list)">
					<span class="el-icon-close content_todoList_delete" :class="{show: list.isActive}" @click="deleteTodo(index)"></span>
				</li>
			</ul>
			<div class="data" v-show="todoLists.length>0">
				<div class="data_times" v-show="times === 0">
					<span>{{times}}</span>&nbspitem left
				</div>
				<div class="data_times" v-show="times > 0">
					<span>{{times}}</span>&nbspitems left
				</div>
				<div class="data_status">
					<a href="#" :class="{active:index === dataStatusIndex}" v-for="(item,index) in dataStatus" @click="switchStatus(index)" :key="index">
						{{item}}
					</a>
				</div>
				<div class="data_clearTodos" @click="clearTodos" v-show="times < todoLists.length">
					<a href="#">clear completed</a>
				</div>
				<div class="data_clearTodos" @click="clearTodos" v-show="times === todoLists.length">
					<a href="#"></a>
				</div>
			</div>
		</div>
	</div>
</body>
~~~

~~~javascript
<script>
	let vm = new Vue({
		el: "#app",
		data() {
			return {
				todoLists: [],
				dataStatus: ["All", "Active", "Completed"],
				dataStatusIndex: 0,
				whichShow: true,
				defaultShow: true
			}
		},
		computed: {
			times() { //使用计算属性计算待办todos的次数 
				let todoArr = this.todoLists
				let times = 0
				for (let i = 0; i < todoArr.length; i++) {
					if (todoArr[i].isChecked === false) {
						times++
					}
				}
				return times
			}
		},
		methods: {
			toEdit(obj) { //使添加的todo可编辑
				obj.isEditing = true
			},
			unEdit(obj) { //使添加的todo不可编辑
				obj.isEditing = false
			},
			addTodo(e) { //添加todo
				var val = e.value
				if (val === "") {
					return
				} //如果输入内容为空则立即返回
				this.todoLists = this.todoLists.concat({ //使用concat这个api添加todo
					value: val, //输入内容
					isEditing: false, //是否在编辑状态
					isActive: false, //删除X图标是否激活
					isChecked: false //是否已完成
				})
				this.$refs.currentInput.value = "" //按下enter添加todo之后把输入框value清零
				window.localStorage.setItem("content", JSON.stringify(this.todoLists)) //使用localStorage以JSON格式存储数据
			},
			deleteTodo(index) { //删除todo
				this.todoLists.splice(index, 1)
				window.localStorage.setItem("content", JSON.stringify(this.todoLists)) //以json格式存储数据
			},
			switchStatus(index) { //试下下方三个状态切换，略麻烦
				this.dataStatusIndex = index
				if (this.dataStatus[index] === "Active") {
					this.defaultShow = false
					this.whichShow = false
				} else if (this.dataStatus[index] === "Completed") {
					this.defaultShow = false
					this.whichShow = true
				} else if (this.dataStatus[index] === "All") {
					this.defaultShow = true
				}
			},
			clearTodos() { //清空已完成的todoLists
				this.todoLists = this.todoLists.filter(todo => todo.isChecked === false)
				window.localStorage.setItem("content", JSON.stringify(this.todoLists)) //以json格式存储数据
			},
			selectAllTodos() { //设置所有todo为已完成
				this.todoLists.map(todo => todo.isChecked = todo.isChecked ? false : true)
			}
		},
		directives: { //自定义focus指令
			"todo-focus": function (el, binding) {
				if (binding.value) {
					el.focus()
				}
			}
		},
		created() {
			let myStorage = window.localStorage.getItem('content')
			this.todoLists = JSON.parse(myStorage) || [] //因为todoLists初始值是null,使用或运算符，如果为null设为空数组
		}
	})
</script>
~~~
