# notes_of_vue.js_components
vue.js components的学习笔记，以及一些demo<br/>
## 1.1、什么是组件
组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 is 特性拓展．
## 1.2、使用组件
### 1.2.1、注册
要注册一个全局组件，你可以使用 Vue.component(tagName, options)。<br/>
注意：对于自定义标签名，Vue.js 不强制要求遵循 W3C规则 （小写，并且包含一个短杠），尽管遵循这个规则比较好。<br/>
组件在注册之后，便可以在父实例的模块中以自定义元素 <my-component></my-component> 的形式使用。要确保在初始化根实例 之前 注册了组件：<br/>**示例1-2-1.html**
### 1.2.2、局部注册
不必在全局注册每个组件。通过使用组件实例选项注册，可以使组件仅在另一个实例/组件的作用域中可用。**示例1-2-2.html**
### 1.2.3、DOM 模版解析说明
当使用 DOM 作为模版时（例如，将 el 选项挂载到一个已存在的元素上）, 你会受到 HTML 的一些限制，因为 Vue 只有在浏览器解析和标准化 HTML 后才能获取模版内容。尤其像这些元素 ```<ul> ， <ol>， <table> ， <select> ```限制了能被它包裹的元素， ```<option>``` 只能出现在其它元素内部。**示例1-2-3.html**<br/>
> 应当注意，如果您使用来自以下来源之一的字符串模板，这些限制将不适用：
>- ```<script type="text/x-template">```
>- ```JavaScript内联模版字符串```
>- ```.vue 组件```<br/>

因此，有必要的话请使用字符串模版。
### 1.2.4、data 必须是函数
使用组件时，大多数可以传入到 Vue 构造器中的选项可以在注册组件时使用，有一个例外： data 必须是函数。 实际上，如果你这么做：
```
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'hello'
  }
})
```
那么 Vue 会在控制台发出警告，告诉你在组件中 data 必须是一个函数。最好理解这种规则的存在意义。<br/>
对比：**示例1-2-4.html**和**示例1-2-4(1).html**
### 1.2.5、构成组件
组件意味着协同工作，通常父子组件会是这样的关系：组件 A 在它的模版中使用了组件 B 。它们之间必然需要相互通信：父组件要给子组件传递数据，子组件需要将它内部发生的事情告知给父组件。然而，在一个良好定义的接口中尽可能将父子组件解耦是很重要的。这保证了每个组件可以在相对隔离的环境中书写和理解，也大幅提高了组件的可维护性和可重用性。<br/>
在 Vue.js 中，父子组件的关系可以总结为 props down, events up 。父组件通过 props 向下传递数据给子组件，子组件通过 events 给父组件发送消息。看看它们是怎么工作的。
![image](https://github.com/25paul/notes_of_vue.js_components/blob/master/images/props-events.png)
## 1.3、Prop
### 1.3.1、使用 Prop 传递数据
组件实例的作用域是孤立的。这意味着不能并且不应该在子组件的模板内直接引用父组件的数据。可以使用 props 把数据传给子组件。<br/>
prop 是父组件用来传递数据的一个自定义属性。子组件需要显式地用 props 选项声明 “prop”：**示例1-3-1.html**和**示例1-3-1(1).html**、**示例1-3-1(2).html**
### 1.3.2、camelCase vs. kebab-case
HTML 特性不区分大小写。当使用非字符串模版时，prop的名字形式会从 camelCase 转为 kebab-case（短横线隔开）：**示例1-3-1(2).html**
### 1.3.3、动态prop
类似于用 v-bind 绑定 HTML 特性到一个表达式，也可以用 v-bind 动态绑定 props 的值到父组件的数据中。每当父组件的数据变化时，该变化也会传导给子组件：
**示例1-3-3.html**
### 1.3.4、字面量语法 vs 动态语法
常犯的一个错误是使用字面量语法传递数值：例如**示例1-3-1(1).html**传入数值是错误的。因为它是一个字面 prop ，它的值以字符串 "1" 而不是以实际的数字传下去。如果想传递一个实际的 JavaScript 数字，需要使用 v-bind ，从而让它的值被当作 JavaScript 表达式计算。
### 1.3.5、单向数据流
prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。这是为了防止子组件无意修改了父组件的状态——这会让应用的数据流难以理解。<bbr/>
另外，每次父组件更新时，子组件的所有 prop 都会更新为最新值。这意味着你不应该在子组件内部改变 prop 。如果你这么做了，Vue 会在控制台给出警告。<br/>
通常有两种改变 prop 的情况：<br/>
1.prop 作为初始值传入，子组件之后只是将它的初始值作为本地数据的初始值使用；<br/>
2.prop 作为需要被转变的原始值传入。<br/>
更确切的说这两种情况是：<br/>
1.定义一个局部 data 属性，并将 prop 的初始值作为局部数据的初始值。<br/>
```
props: ['initialCounter'],
data: function () {
  return { counter: this.initialCounter }
}
```
2.定义一个 computed 属性，此属性从 prop 的值计算得出。<br/>
```
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```
**注意**在 JavaScript 中对象和数组是引用类型，指向同一个内存空间，如果 prop 是一个对象或数组，在子组件内部改变它会影响父组件的状态。
### 1.3.6、Prop 验证
组件可以为 props 指定验证要求。如果未指定验证要求，Vue 会发出警告。当组件给其他人使用时这很有用。
prop 是一个对象而不是字符串数组时，它包含验证要求：
```
Vue.component('example', {
  props: {
    // 基础类型检测 （`null` 意思是任何类型都可以）
    propA: Number,
    // 多种类型
    propB: [String, Number],
    // 必传且是字符串
    propC: {
      type: String,
      required: true
    },
    // 数字，有默认值
    propD: {
      type: Number,
      default: 100
    },
    // 数组／对象的默认值应当由一个工厂函数返回
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```
type 可以是下面原生构造器：String,Number,Boolean,Function,Object,Array<br/>
type 也可以是一个自定义构造器，使用 instanceof 检测。<br/>
当 prop 验证失败了，如果使用的是开发版本会抛出一条警告。
## 1.4、自定义事件
我们知道，父组件是使用 props 传递数据给子组件，但如果子组件要把数据传递回去，应该怎样做？那就是自定义事件！
### 1.4.1、使用 v-on 绑定自定义事件
每个 Vue 实例都实现了事件接口(Events interface)，即：使用 $on(eventName) 监听事件；使用 $emit(eventName) 触发事件<br/>
注意：Vue的事件系统分离自浏览器的EventTarget API。尽管它们的运行类似，但是$on 和 $emit 不是addEventListener 和 dispatchEvent 的别名。<br/>
另外，父组件可以在使用子组件的地方直接用 v-on 来监听子组件触发的事件。**实例1-4-1.html**<br/>
有时候，你可能想在某个组件的根元素上监听一个原生事件。可以使用 .native 修饰 v-on 。例如：**示例1-4-1(1).html**。<br/>
```
<my-component v-on:click.native="doTheThing"></my-component>
```
### 1.4.2、使用自定义事件的表单输入组件
自定义事件也可以用来创建自定义的表单输入组件，使用 v-model 来进行数据双向绑定。牢记：<br/>
```
<input v-model="something">
```
仅仅是一个语法糖：<br/>
```
<input v-bind:value="something" v-on:input="something = $event.target.value">
```
所以在组件中使用时，它相当于下面的简写：<br/>
```
<custom-input v-bind:value="something" v-on:input="something = arguments[0]"></custom-input>
```
所以要让组件的 v-model 生效，它必须：接受一个 value 属性；在有新的 value 时触发 input 事件<br/>
**示例1-4-1.html**和**示例1-4-1(1).html**
### 1-4-3、非父子组件通信
有时候非父子关系的组件也需要通信。在简单的场景下，使用一个空的 Vue 实例作为中央事件总线：<br/>
```
var bus = new Vue()
// 触发组件 A 中的事件
bus.$emit('id-selected', 1)
// 在组件 B 创建的钩子中监听事件
bus.$on('id-selected', function (id) {
  // ...
})
```
在更多复杂的情况下，你应该考虑使用专门的 [状态管理模式](https://cn.vuejs.org/v2/guide/state-management.html).
## 1.5、使用 Slot 分发内容
为了让组件可以组合，我们需要一种方式来混合父组件的内容与子组件自己的模板。这个过程被称为 内容分发 (或 “transclusion” 如果你熟悉 Angular)。Vue.js 实现了一个内容分发 API ，参照了当前 Web 组件规范草案，使用特殊的 <slot> 元素作为原始内容的插槽。
### 1.5.1、编译作用域
在深入内容分发 API 之前，我们先明确内容的编译作用域。<br/>
组件作用域简单地说是：父组件模板的内容在父组件作用域内编译；子组件模板的内容在子组件作用域内编译。<br/>
一个常见错误是试图在父组件模板内将一个指令绑定到子组件的属性/方法。示例1-5-1.html<br/>
### 1.5.2、单个 Slot
除非子组件模板包含至少一个 <slot> 插口，否则父组件的内容将会被丢弃。当子组件模板只有一个没有属性的 slot 时，父组件整个内容片段将插入到 slot 所在的 DOM 位置，并替换掉 slot 标签本身。示例1-5-1.html和示例1-5-1(1).html。
### 1.5.3、具名 Slot
```<slot>``` 元素可以用一个特殊的属性 name 来配置如何分发内容。多个 slot 可以有不同的名字。具名 slot 将匹配内容片段中有对应 slot 特性的元素。
仍然可以有一个匿名 slot ，它是默认 slot ，作为找不到匹配的内容片段的备用插槽。如果没有默认的 slot ，这些找不到匹配的内容片段将被抛弃。**示例1-5-3.html**。
### 1.5.4、作用域插槽
作用域插槽是一种特殊类型的插槽，用作使用一个（能够传递数据到）可重用模板替换已渲染元素。<br/>
在子组件中，只需将数据传递到插槽，就像你将 prop 传递给组件一样。<br/>
在父级中，具有特殊属性 scope 的 <template> 元素，表示它是作用域插槽的模板。scope 的值对应一个临时变量名，此变量接收从子组件中传递的 prop 对象。**示例1-5-4.html**。<br/>
作用域插槽更具代表性的用例是列表组件，允许组件自定义应该如何渲染列表每一项。**示例1-5-4(1).html**。
## 1.6、动态组件
多个组件可以使用同一个挂载点，然后动态地在它们之间切换。使用保留的 <component> 元素，动态地绑定到它的 is 特性。
### 1.6.1、keep-alive
如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令参数。
## 1.7、杂项
### 1.7.1、编写可复用组件
Vue 组件的 API 来自三部分 - props, events 和 slots
### 1.7.2、子组件索引
尽管有 props 和 events ，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 ref 为子组件指定一个索引 ID 。<br/>
 ```
<div id="parent">
  <user-profile ref="profile"></user-profile>
</div>
var parent = new Vue({ el: '#parent' })
// 访问子组件
var child = parent.$refs.profile
```
### 1.7.3、异步组件
在大型应用中，我们可能需要将应用拆分为多个小模块，按需从服务器下载。为了让事情更简单， Vue.js 允许将组件定义为一个工厂函数，动态地解析组件的定义。Vue.js 只在组件需要渲染时触发工厂函数，并且把结果缓存起来，用于后面的再次渲染。
###1.7.4、组件命名约定
当注册组件（或者 props）时，可以使用 kebab-case ，camelCase ，或 TitleCase 。Vue 不关心这个。<br/>
在 HTML 模版中，请使用 kebab-case 形式。<br/>
### 1.7.5、递归组件
### 1.7.6、Circular References Between 
### 1.7.7、内联模版
### 1.7.8、X-Templates
### 1.7.9、使用 v-once 的低级静态组件
详细看官网。
