# [lxQuery](https://github.com/lixiaotiancai/lxQuery) 文档
==================================================

## lxQuery简介
lxQuery是一种可以方便操作javascript的js库, 里面封装了大量的函数, 同时可以很方便的操作dom.<br>
author: 李骁<br>
***
### 基本语法
lxQuery可以通过lx(selector)来选中相应的HTML元素, 并对其进行操作：<br>
基础语法：lx(selector).action()<br>
实例：
```javascript
lx('div').css('color', 'red')
lx('#id').removeNode()
lx('div[attr=value]').on('click', function () { // do something })
```
于此同时, lxQuery也提供了丰富的内置函数<br>
实例：
```
lx.fetch(url, { // options }).then( // onFufilled).catch( // onRejected )
lx.defer((resolve, reject) => { // do something}).done( // onFufilled).fail( // onRejected)
lx.sleep(time)
lx.each({ // object or array}, callback)
```
不仅如此, lxQuery的大部分函数都支持链式调用<br>
实例:
```javascript
lx('div').eq(0).attr('id', 'id').end().removeItemByIndex(2).css({color: 'red', fontSize: '18px'}).end().on('click', callaback).off('click')
```
## 功能介绍
lxQuery主要分为8大模块:<br>
core模块: lxQuery主体<br>
dom模块: 可以方便操作dom<br>
style模块: 可以设置dom样式<br>
event模块: 控制dom事件<br>
callbacks模块: 方便调用回调函数<br>
defer模块: 控制异步流程<br>
ajax模块: fetch jsonp iframe<br>
extra模块: 内置的一些额外功能<br>
<br>
接下来将以模块为划分单位来依次介绍
***
### core 模块
lxQuery的主体模块, 提供的功能如下:<br>
lx(selector), 返回一个lxQuery对象, 目前只支持querySelectorAll元素选组器。可以通过lx(selector)来选择元素, 并通过lx(selector).action()来操作选中的元素。<br>
当selector为空时, 返回一个空的lxQuery对象。<br>
在core中, 提供了几个较为基础的函数:<br>
<br>
#### each 遍历<br>
支持:lx.each() 和 lx().each<br>
语法：lx.each(param, callback) lx(selector).each(callback)<br>
param为对象或数组, 暂不支持类数组<br>
callback(param1, param2)支持两个参数, 若遍历对象为array, 则param1为数组下标, param2为数组内容, 若遍历对象为object, 则param1为对象属性名, param2为对应属性值<br>
实例:
```javascript
// lx.each(arr, callback)
lx.each([1, 2, 3], function (i ,el) {
  console.log(i, el) // 0 1  1 2  2 3
})

// lx.each(obj, callback)
lx.each({name: 'lixiao', age: '24'}, function (name, value) {
  console.log(name, value) // name lixiao  age 24
})

//lx(selector).each()
lx('div').each(function (i, el) {
  el.style.color = 'red' // 所有的div的color都设成红色
})
```
#### merge 合并数组<br>
支持: lx.merge<br>
语法: lx.merge(arr1, arr2)<br>
这里的arr支持类数组<br>
实例:<br>
```javascript
lx.merge([1, 2, 3], [4, 5, 6]) // [1, 2, 3, 4, 5, 6]

var likeArray = {}
likeArray[0] = 1
likeArray[1] = 2
likeArray.length = 2

lx.merge(likeArray, [3, 4]) // {0:1, 1:2, 2:3, 3:4, length:4}
```
#### pushStack 进栈<br>
支持: lx().pushStack<br>
语法: lx('selector1').pushStack(lx('selector2'))<br>
将selector2元素push进栈, 后续的.action()操作都会针对栈顶的元素进行操作<br>
实例:<br>
```javascript
lx('#div1').pushStack('#div2').css('color', 'red') // #div2 color red
```
#### end 出栈<br>
支持：lx().end<br>
语法: lx(selector).end()<br>
将此元素进行出栈, 若栈为空时出栈, 则返回document<br>
实例:<br>
```javascript
lx('li').eq(0).css('color', 'red').end().css('fontSize', '18px') // 将选中的li元素中的第一li元素color red后将全体li元素 fontSize 18px
lx('div').end() // document
```
#### eq first last index选择器<br>
支持: lx.eq lx().eq lx.first lx().first lx.last lx().last<br>
语法： lx.eq(arr, index) lx.eq(index) lx.first(arr) lx().first() lx.last(arr) lx().last()<br>
元素index选择器, index支持不超出数组范围内的负数, -1代表数组的最后一个元素, 依次类推, 此外index的选择器实质是进行了一次入栈操作, 因此可对选择的dom元素做出栈操作<br>
实例:<br>
```javascript
lx.eq([1, 2, 3], 0) // [1]
lx.eq([1, 2, 3], -1) // [3]
lx.eq([1, 2, 3], 4) // []
lx.first([1, 2, 3]) // [1]
lx.last([1, 2, 3]) // [3]
lx('li').eq(0) // [first li]
lx('li').eq(-1) // [last li]
lx('li').first() // [first li]
lx('li').last() // [last li]
```
#### removeItemByIndex 移除某元素<br>
支持: lx.removeItemByIndex lx().removeItemByIndex<br>
语法: lx.removeItemByIndex(index, arr) lx(),removeItemByIndex(index)<br>
将某元素移除, index支持负数<br>
实例:<br>
```javascript
lx.removeItemByIndex([1, 2, 3], 1) // [1, 3]
lx.removeItemByIndex([1, 2, 3], -1)// [1, 2]
lx('li').removeItemByIndex(-1).css('color', 'red').end().css('fontSize', '20px') // 除最后一个li元素外color red后所有li元素fontSize 20px
```
#### serialize 序列化<br>
支持: lx.serialize lx().serialize<br>
语法: lx.serialize(object) lx('form').serialize()<br>
序列化, 当操作dom时只支持表单<br>
实例:<br>
```html
<form>
  <input name="name" value="lixiao" />
  <input name="age" value="24" />
</form>
```
```javascript
lx.serialize({name: 'lixiao', age: 24}) // name=lixiao&age=24
lx('form').serialize() // name=lixiao&age=24
```
### dom 模块
lxQuery中负责操作dom的模块<br>
主要负责增添删减dom元素以及操作dom元素的atrribute<br>
支持: lx().domAction() 该模块提供的api只支持操作lxQuery选中的dom对象<br>
<br>
#### removeNode 移除节点<br>
语法: lx(selector).removeNode()<br>
将选中的此节点从dom中移除<br>
实例:<br>
```javascript
lx('div').removeNode() // 移除所有的div标签
```
#### insertBefore/insertAfter 插入新节点<br>
语法: lx(selector).insertBefore(newNode) lx(selector).insertAfter(newNode)<br>
在被选中的节点前/后插入新的节点<br>
实例:<br>
```javascript
var node = document.createElement('div')
lx('#div1').insertBefore(node) // 在#div1前插入新node
lx('#div2').insertAfter(node) // 在#div2后插入新node
```
#### appendChild/prependChild 插入新子节点<br>
语法: lx(selector).apppendChild(newNode) lx(selector).preppendChild(newNode)<br>
在被选中的节点的尾部/开始插入新的子节点<br>
实例:<br>
```javascript
var node = document.createElement('div')
lx('#div1').appendChild(node) // 在#div1的尾部插入新子节点node
lx('#div2').prependChild(node) // 在#div2的开始插入新子节点node
```
#### attr 设置dom的attribute<br>
语法：lx(selector).attr(...)<br>
这里attr中的参数有4种形式<br>
1个参数 {String or Object}<br>
参数类型为String时, 该参数为attribute, 获取节点列表中所有节点的该attribute, 并push进数组中返回<br>
参数类型为Object时, 该参数为key为attribute value为值的对象, 按对象键值对设置节点列表的attribute<br>

2个参数 {String} [attribute] {String} [value] or {String} [attribute] {Function} {callback}<br>
第一个参数为attribute<br>
当第二个参数为String时, 该参数为value, 设置该节点的attribute<br>
当第二个参数为Function时, 该参数为回调, 根据此节点的该attribute进行操作<br>
实例:<br>
```javascript
lx('#div1').attr('id') // ['div1']
lx('#div1').attr({class: 'className', 'data-x': 1}) // #div1 新attribute class="className" data-x="1"
lx(#div1).attr('class', 'className') // #div1 新attribute class="className"
lx('div').attr('id', function (i, value) { return i + value + ''}) // 根据选中div的index和attribute id的value进行操作从而返回并赋予新attribute
```
#### removeAttr 移除dom的attribute<br>
语法: lx(selector).removeAttr('atrr1 atrr2 ...')<br>
移除相应的attribute, 这里接收的参数可以是多个attribute, 之间需用空格进行分隔<br>
实例:<br>
```javascript
lx('#div1').removeAttr('id class') // 移除了#div1的id及class
```
### style 模块
lxQuery中负责操作dom样式的模块<br>
支持: lx().domAction() 该模块提供的api只支持操作lxQuery选中的dom对象<br>
<br>
#### addClass 增加class<br>
语法: lx(selector).addClass('class1 class2 ...')<br>
增加相应的class类名, 这里接收的参数可以是多个class, 之间需用空格进行分隔<br>
实例:<br>
```javascript
lx('#div1').addClass('class1 class2') // #div1增加了新的样式class1 class2
```
#### removeClass 移除class<br>
语法: lx(selector).removeClass('class1 class2 ...')<br>
移除相应的class类名, 这里接收的参数可以是多个class, 之间需用空格进行分隔<br>
实例:<br>
```javascript
lx('#div1').removeClass('class1 class2') // #div1移除了样式class1 class2
```
#### toggleClass 切换class<br>
语法: lx(selector).toggleClass('class1 class2 ...')<br>
切换相应的class类名, 如果没有, 则添加, 如果有, 则移除, 这里接收的参数可以是多个class, 之间需用空格进行分隔<br>
实例:<br>
```javascript
// #div1没有class1 class2 class3
lx('#div1').toggleClass('class1 class2') // #div1增加了新样式class1 class2
lx('#div1').toggleClass('class1 class2 class3') // #div1移除了class1 class2 增加了class3
```
#### css 样式控制器 <br>
语法: lx(selector).css(...)<br>
这里的参数有4中形式<br>
1个参数 {String or Object}<br>
参数类型为String时, 该参数为css样式, 获取节点列表中所有节点的该css样式, 并push进数组中返回<br>
参数类型为Object时, 该参数为key为css value为值的对象, 按对象键值对设置节点列表的css样式<br>

2个参数 {String} [css] {String} [value] or {String} [css] {Function} {callback}<br>
第一个参数为css样式<br>
当第二个参数为String时, 该参数为value, 设置该节点的css<br>
当第二个参数为Function时, 该参数为回调, 根据此节点的该css样式进行操作<br>
实例:<br>
```javascript
lx('#div1').css('font-size') // ['20px']
lx('#div1').css({color: 'red', 'font-size: 20px'}) // #div1 新样式 color red font-size 20px
lx('#div1').css('color', 'red') // #div1 新样式 color red
lx('#div1').css('color', function (i, value) {return 'green'}) // 根据#div1的index和color的value值进行操作从而返回并设置新的color值
```
### event 模块<br>
lxQuery中负责操作dom绑定事件的模块<br>
支持: lx().domAction() 该模块提供的api只支持操作lxQuery选中的dom对象<br>
<br>
#### on 绑定事件<br>
语法: lx(selector).on('eventType1 evetType2 ...', callback)
按事件类型在选中的节点上绑定事件, eventType支持多个事件类型, 事件类型间用空格分隔<br>
实例:<br>
```javascript
lx('#div1').on('click mouseup', function () {}) // 在#div1上同时绑定了click mouseup两个事件
```
#### remove 解绑事件<br>
语法: lx().off(...)<br>
3种参数形式<br>
输入参数为type callback时, 卸载节点对应的type事件类型上的对应事件<br>
输入参数为type时, 卸载节点对应的type上的所有事件<br>
无参数时, 卸载节点上绑定的所有event<br>
eventType支持多个事件类型, 事件类型间用空格分隔<br>
实例:<br>
```javascript
lx('#div1').off('click mouseup', callback) // 解绑#div1上的click mouseup对应的callback事件
lx('#div1').off('click mouseup') // 解绑#div1上click mouseup上的所有事件
lx('#div1').off() // 解绑#div1上的所有事件
```
### callbacks 模块<br>
可以方便的操作回调函数的模块<br>
支持: lx.action() 不支持操作lx()选中的dom对象<br>
<br>
#### add 添加回调<br>
语法: lx.callbacks().add(newCallback)<br>
添加回调函数<br>
实例:<br>
```javascript
var c = lx.callbacks()
c.add(newCallback1)
c.add(newCallback2)
```
#### fire 调用回调<br>
语法: lx.callbacks().fire(param)<br>
调用回调函数<br>
实例:<br>
```javascript
var c = lx.callbacks()
c.add(newCallback1)
c.add(newCallback2)
c.fire(param) // newCallback1(param) newCallback2(param)
```
#### remove 移除回调<br>
语法: lx.callbacks().remove(callbackName)<br>
移除回调函数<br>
实例:<br>
```javascript
var c = lx.callbacks()
c.add(newCallback1)
c.add(newCallback2)
c.fire(param) // newCallback1(param) newCallback2(param)
c.remove(newCallback1)
c.fire(param) // newCallback2(param)
```
#### empty 清空回调<br>
语法: lx.callbacks().empty()<br>
清空回调函数<br>
实例:<br>
```javascript
var c = lx.callbacks()
c.add(newCallback1)
c.add(newCallback2)
c.fire(param) // newCallback1(param) newCallback2(param)
c.empty()
c.fire() // nothing happenned
```
### defer 模块<br>
可以方便操控异步流程的模块<br>
支持: lx.action() 不支持操作lx()选中的dom对象<br>
<br>
#### defer
这里的defer其实是参考了Promise, 不多说了, 直接上实例吧!
实例:<br>
```javascript
var name = ''
var flag = true
var d = lx.defer((resolve, reject) => {
   if (flag) {
    resolve(
      setTimeout(function () {
        name = 'lixiao'
        resolve(name)
      }, 1000)
    )
   } else {
      reject(new Error('error'))
   }
})

d.done(value => {
  console.log(value)
  return lx.defer((resolve, reject) => {
    resolve('666')
  })
}).done(value => console.log(value)).fail(err => console.log(err))
```
### ajax 模块<br>
封装了常用的 ajax jsonp iframe请求<br>
支持: lx.action() 不支持操作lx()选中的dom对象<br>
<br>
#### fetch 基于ajax+Promise<br>
语法: lx.fetch(url, options).then( // onFulfilled).catch( // onRejected)<br>
options {method:'GET'(default)/'POST', data: null(default)/serialize string}<br>
实例:<br>
```javascript
// get 请求
lx.fetch(url).then(res => console.log(res)).catch(err => console.log(err))

// post 请求
lx.fetch(url, {
  method: 'POST',
  data: 'name=lixiao&age=24'
}).then(res => console.log(res)).catch(err => console.log(err))
```
#### fetchJsonp 基于jsonp+Promise<br>
语法: lx.fetchJsonp(url, options).then( // onFulfilled).catch( // onRejected)<br>
options {callbackName:'callback'(default)/string, functionName:randomString(default/string), data:{}(default)/object}<br>
实例:<br>
```javascript
//jsonp请求
lx.fetchJsonp(url, {
data: {
  name: lixiao,
  age: 24
}
}).then(res => console.log(res)).catch(err => console.log(err))
```
#### iframe 基于iframe+window.name<br>
语法 lx.iframe(options, callback)<br>
options {callbackName: , funcName: , targetUrl: skipUrl: data: }<br>
注: lx.iframe只提供了本页面的请求函数, 目标页面和跳转页面需另行处理<br>
实例:<br>
```javascript
// iframe请求
lx.iframe({
  targetUrl: targetUrl,
  skipUrl: skipUrl,
  data: {
    name: lixiao
  }
}, res => console.log(res))
```
### extra 模块<br>
内置了一些常用的额外功能, 类似于插件<br>
<br>
#### cookie 操作cookie<br>
提供了setCookie getCookie removeCookie 三种方法<br>
支持: lx.cookieAction()<br>
语法: lx.setCookie(name, value) lx.getCookie(name) lx.removeCookie(name)<br>
实例:<br>
```javascript
lx.setCookie('name', 'lixiao') // document.cookie='name=lixiao'
lx.getCookie('name') // 'lixiao'
lx.removeCookie('name') // document.cookie=''
```
#### localStorage 操作localStorage<br>
提供了setLocalStorage getLocalStorage removeLocalStorage 三种方法<br>
支持: lx.localStorageAction()<br>
语法: lx.setLocalStorage(name, value) lx.getLocalStorage(name) lx.removeLocalStrorage(null/name)<br>
实例:<br>
```javascript
lx.setLocalStorage('name', 'lixiao')
lx.getLocalStorage('name') // 'lixiao'
lx.removeLocalStorage('name')
lx.removeLocalStorage() // 移除全部localStorage
```
#### sessionStorage 操作sessionStorage<br>
提供了setSessionStorage getSessionStorage removeSessionStorage 三种方法<br>
支持: lx.sessionStorageAction()<br>
语法: lx.setSessionStorage(name, value) lx.getSessionStorage(name) lx.removeSessionStrorage(null/name)<br>
实例:<br>
```javascript
lx.setSessionStorage('name', 'lixiao')
lx.getSessionStorage('name') // 'lixiao'
lx.removeSessionStorage('name')
lx.removeSessionStorage() // 移除全部SessionStorage
```
#### query 截取serialize string中的param对应的value<br>
支持: lx.query()<br>
语法: lx.query(name, queryString)<br>
实例:<br>
```javascript
lx.query('name', '?name=lixiao&age=24') // 'lixiao'
```
#### getAbsoluteUrl 获取当前页面的绝对路径<br>
支持: lx.getAbsoluteUrl()<br>
语法: lx.getAbsoluteUrl(urlString)<br>
实例:<br>
```javascript
lx.getAbsoluteUrl('page') // "https://github.com/lixiaotiancai/lxQueryDoc/edit/master/page"
```
#### sleep 间断执行<br>
支持: lx.sleep()<br>
语法: lx.sleep(time)<br>
注: 实质是返回了一个Promise对象, 因此要在Promise环境中调用该函数<br>
实例:<br>
```javascript
async function sleep () {
  console.log('start')
  await lx.sleep(2000)
  console.log('end') // log 'end' after 2000ms
}
```
#### debounce 防抖函数<br>
支持: lx.createDebounce()<br>
语法: lx.createDebounce(callback, time)
在time内, 不管触发了多少次callback, 都会只按触发一次处理<br>
实例:<br>
```javascript
// 防抖输入框
lx('input').on('keyup', lx.createDebounce(() => {
      console.log(lx('input')[0].value)
    }, 300))
```
#### throttle 截流函数<br>
支持: lx.createThrottle()<br>
语法: lx.createThrottle(callback, time)<br>
合并time内执行的callback并之触发一次<br>
实例:<br>
```javascript
// 截流scroll
lx('body').on('scroll', lx.createThrottle(() => {
     console.log(lx('body')[0].offsetHeight)
}, 100))
```
#### formatDate 格式化时间<br>
支持: lx.formateDate()<br>
语法: lx.createDate(patternString, number|new Date())<br>
将时间格式化<br>
实例:<br>
```javascript
formatDate('YYYY-MM-DD hh:mm:ss', new Date(2018, 8, 22, 8, 8, 8)) // '2018-09-22 08:08:08'
formatDate('Y-M-D h:m:s', new Date(2018, 8, 22, 8, 8, 8)) // '2018-9-22 8:8:8'
formatDate('YYYY-MM-DD hh:mm:ss', 1537545448184) // '2018-09-21 23:57:28'
```
#### delHtmlTag 删除富文本(html)标签<br>
支持: lx.delHtmlTag()<br>
语法: lx.delHtmlTag(string)<br>
删除合法的富文本标签<br>
实例:<br>
```javascript
delHtmlTag('<div data-index="index">text<img src="src"/></div>') // 'text'
delHtmlTag('<h1>这是h1的内容!<a href="a.com">详情可点击</a></><img src="a.jpg" />') // '这是h1的内容!详情可点击'
delHtmlTag('<啦啦啦 />123456<123></input aaa>') // '<啦啦啦 />123456<123>'
```
#### htmlEncode html关键字符转码<br>
支持: lx.htmlEncode()<br>
语法: lx.htmlEncode(string)<br>
对html关键字符进行转码<br>
实例:<br>
```javascript
htmlEncode('<script></script>') // '&lt;script&gt;&lt;/script&gt;'
```
