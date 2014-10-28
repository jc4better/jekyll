---
layout: default
title: remove hide display在表示'页面隐藏'的区别
----
jQuery.min.js v1.8.3
测试过后发现这个测试貌似没什么意义。。。
remove:
直接移除这个标签 在页面上不可见了(真正的不可见),页面也将无法再对这个对象进行获取,通过父节点/子节点等操作都是无法再获取该节点的(前面保存了的也会无效的)，即使你F12源码也是没有的。
<% highlight javascript %>
remove: function(e, t) {
    var n, r = 0;
    for (;(n = this[r]) != null; r++) 
        if (!e || v.filter(e, [n]).length)!t && n.nodeType === 1 && (v.cleanData(n.getElementsByTagName("*")), v.cleanData([n])), n.parentNode && n.parentNode.removeChild(n);
            return this
},
<% endhighlight %>
可以对一些对操作比较敏感的地方这样使用。如:用户要充值，需要对用户的证书检查，安装有证书才显示给他密码框进行后续操作。防止用户F12将style="display:none"强行改成inline继续操作。

hide(),show():
通过在style="display:none"和style="display:inline"互相切换来显示隐藏标签。源码仍然保留在页面中的，后面也可以对该标签进行获取操作。

toggle:
同hide(),show()
对js进行解码，可以发现它其实就是通过show()和hide()来进行操作的。//v(this).show() : v(this).hide()

display none:
style中的属性，对该属性进行操作达到的现实隐藏功能.
另外display还有display:block，该元素将显示为块级元素，前后会带有换行符(w3cshool)
类似还有table inline-table
