---
title: Yii_Dynamic_Content
date: 2013-10-04 11:27:10
tags: Translation
categories:
- 博文
---
# 译文
<h2 id="toc_0.1">动态内容</h2>

使用片段缓存或者页面缓存时，我们经常遇到这样的情境：除了在个别地方，整个输出的内容都是相对静态的。例如，帮助页面要显示静态的帮助信息，同时，页面顶部要显示当前登录用户的用户名。



解决这个问题，我们可以通过用户名来变换缓存的内容，但这样对，同时，页面顶部要显示当前登录用户的用户名。



解决这个问题，我们可以通过用户名来变换缓存的内容，但除了用户名，缓存中绝大部分内容都是相同的，这对我们珍贵的缓存空间是巨大的浪费。我们也可以将页面划分成几个片段，并分别缓存起来，但是这么做使得视图和代码都变得更加的复杂。更好的实现方式是使用CController提供的动态内容特性。



动态内容指的是输出当中不应该被缓存的片段，即使它被内嵌在片段缓存中。要使得这部分内容一直是动态的，那么每一次请求都要重新生成之，即使嵌套在它上面的这部分内容是从缓存中读取的。因此，我们需要这部分动态的内容有某个方法或者函数来生成。



调用CController::renderDynamic()方法在期望的地方插入动态内容。

```php
... 其它HTML内容 ...
<?php if($this->beginCache($id){ ?>
... 片段缓存中的内容 ...
	<?php $this->renderDynamic($callback); ?>
... 片段缓存中的内容 ...
<?php $this->endCache(); } ?>
... 其它HTML内容 ...
```


上面的代码中，$callback指的是有效的php回调函数。它可以是指向当前控制器类的方法名的字符串，或者全局的函数。也可以是指向对象方法的数组。renderDynamic()任何附加的参数都会被传递给回调函数。回调函数会将动态内容return回来而不是直接输出。

<h1>原文</h1>
<a href='http://www.yiiframework.com/doc/guide/1.1/en/caching.dynamic'>Yii Dynamic Content</a>

Dynamic Content



When using fragment caching or page caching, we often encounter the situation where the whole portion of the output is relatively static except at one or several places. For example, a help page may display static help information with the name of the user currently logged in displayed at the top.



To solve this issue, we can variate the cache content according to the username, but this would be a big waste of our precious cache space since most content are the same except the username. We can also divide the page into several fragments and cache them individually, but this complicates our view and makes our code very complex. A better approach is to use the dynamic content feature provided by CController.



A dynamic content means a fragment of output that should not be cached even if it is enclosed within a fragment cache. To make the content dynamic all the time, it has to be generated every time even when the enclosing content is being served from cache. For this reason, we require that dynamic content be generated by some method or function.



We call CController::renderDynamic() to insert dynamic content at the desired place.

```
...other HTML content...
<?php if(\(this->beginCache(\)id)) { ?>
...fragment content to be cached...

<blockquote>
<?php \(this->renderDynamic(\)callback); ?>
</blockquote>

...fragment content to be cached...
<?php $this->endCache(); } ?>
...other HTML content...
```



In the above, $callback refers to a valid PHP callback. It can be a string referring to the name of a method in the current controller class or a global function. It can also be an array referring to a class method. Any additional parameters to renderDynamic() will be passed to the callback. The callback should return the dynamic content instead of displaying it.
