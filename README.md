# Virtual-Dom
记录一下Virtual Dom介绍

# VD是什么
VD是一个简单的JS对象，最少包含tag,props和children三个属性。
```
{
    tag: "div",
    props: {},
    children: [
        "Hello World", 
        {
            tag: "ul",
            props: {},
            children: [{
                tag: "li",
                props: {
                    id: 1,
                    class: "li-1"
                },
                children: ["第", 1]
            }]
        }
    ]
}
```
上面的VD由以下的HTML生成
```
<div>
    Hello World
    <ul>
        <li id="1" class="li-1">
            第1
        </li>
    </ul>
</div>
```
# 为什么需要VD
将页面的状态抽象为JS对象的形式，配合不同的渲染工具，使跨平台渲染成为可能。

在页面进行更新的时候，借助VD，DOM元素的改变可以在内存中进行比较，再结合框架的事务机制将多次比较的结果合并后一次性更新到页面，从而有效地减少页面渲染的次数，提高渲染效率。
# 如何实现VD到真实DOM的映射
```
const ul = h('ul', {id: 'list', style: 'color: red'}, [
  h('li', {class: 'item'}, ['Item 1']),
  h('li', {class: 'item'}, ['Item 2']),
  h('li', {class: 'item'}, ['Item 3'])
]

const urlDom = ul.render() // 渲染 DOM 节点和它的子节点
class VNode{
constructor(tagName,props,children){
this.tagName=tagName;
this.props=props;
this.children=children;
}
render(){
const el=document.createElement(this.tagName)
Object.entries(this.props).forEach(([key,value])=>el.setAttribute(key,value))
//把对象的键值以数组的形式遍历出来，结果和for..in一致
var children=this.children||[]
children.forEach((child)=>{
var childNode=(child instanceof VNode)?children.render()：document.creatElement(child)
el.appendChild(childNode)
})
return el
}
}
```
