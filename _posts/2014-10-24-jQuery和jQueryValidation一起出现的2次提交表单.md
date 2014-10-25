---
layout: default
title: jQuery和jQueryValidation一起导致的2次提交表单
---
<link ref="stylesheet" href="/css/pygments.css">
环境：jQuery Validation Engine 2.6.2
      jQuery v1.8.3
在Chrome 38.0.2125.104 m(最新的64位)和IE11上出现一次点击一次按钮，出发了两次submit，但是在firefox 32.0.3上是可以正常提交的。

解决方法就是将jQuery的$("id").submit()的提交改为document.id.submit()；  或者去掉手动触发的校验引擎这部分 $("frm").validatingEngine("validate");  
下面是vm的这段代码。

{% highlight html %}
<form id="frm" name="frm" method="post" action="/user/do">
    <input id="postForm" value="Submit" class="validate[required,ajax[check]] txt""/>
</form>
<script language="javascript">
functio submitMyForm(function(){
   $("frm").validatingEngine("validate");  
   //本地做一些校验
   $.ajax({
       type: 'POST',
       url: getContextPath() + '/ajax/check.json',
       async: false,
       success:function(data){
           var obj = eval(data);
           var result = obj.result;
           if(result == 'true'){
               $("#frm")submit();
           }else{
               //
           }
       },
       error: function(){
           return;
       }
   });
});

$().ready(function(){
    $("#postForm").click(function(){
        submitMyForm();
    });
});
<scirpt/>
{% endhighlight %}

作为一个比较呆的程序员，果断采用散弹枪式编程法，找到两个解决方法：
1.删掉async: false同步这一段，可以正常提交。但是这个问题在原来也没有提出过，这个可能有一定的设计理由，怕修改了这里对其他地方有影响，所以放弃了这个方法；
2.将$("#frm").submit()换成原生的document.frm.submit()就可以提交了，但是表单在提交之前不会触发js的校验框架,input框中的值不会被校验，就需要单独添加代码来对这里进行处理。(毕竟不能太影响用户体验了嘛)

猜测可能是 jQuery在有ajax+jQuery校验框的某种情况下出现的一场，可能是jQuery submit处理的封装的问题。 或者是手动出发了校验引擎 $("frm").validatingEngine("validate"); 引起的。 

问题排查：
在调试的时候发现jquery.validationEngine.js中有这么个方法_ajax
{% highlight javascript %}
if (options.eventTrigger == "submit")
    field.closest("form").submit();
{% endhighlight %}
在这个地方对表单进行了提交。
然后手动出发点校验引擎在这儿，有这么一段注释
{% highlight javascript %}
/**
* Validates either a form or a list of fields, shows prompts accordingly.
* Note: There is no ajax form validation with this method, only field ajax validation are evaluated
*
* @return true if the form validates, false if it fails
*/
validate: function() {
{% endhighlight %}
开始猜测错误的源头还是都jQuery 校验引擎的使用问题。因为这里正好在input的校验里面有ajax的校验,或者这里说的在submit之前的这个ajax校验。结合找到的两个解决问题的方法，得出结论

错误产生主要还是对ajax校验引擎的错误使用，这里async: false设置的原因 应该是$.ajax和 $("frm").validatingEngine("validate");两个ajax会导致某部分校验失效而设置的。

具体的原因还有待看下validation的源码。  

下面给一个场景比较简单的jQuery校验引擎的正确使用方法
{% highlight javascript %}
<form name="frm" id="frm"  method="POST" action="/usr/submit.htm">
    <input id="postForm" value="Submit" class="validate[required,ajax[check]] txt""/>
</form>
<script language="javascript">
$().ready(){
    $("#postForm").click(){
        $("frm").submit();//这里提交，如果会自动在提交时再在提交之前校验你的表单，如果失败就会被阻断
    }
}
</script>
{% endhighlight %}

下面给段在js里面看到的作者的一段很逗的注释
{% highlight javascript %}
if (element.hasClass('validating')) {
    // form is already validating.
    // Should abort old validation and start new one. I don't know how to implement it.
    return false;
} else {	
{% endhighlight %}

好的谢谢拜读。如我的文章有什么问题或者大家有什么问题，还请Contact me:jiangchuan1109@gmail.com
不断完善中。(做事都不上心啊！该鞭策下了)
