---
layout: post
title: 解决js函数闭包中this指针指向变化而导致this.$router.push不起作用的问题
date: 2020-01-15 00:00:00
categories: 
- Vue-前端框架
tags: 
- Vue
- Vue-cli
- Javascript
description: 解决js函数闭包中this指针指向变化而导致this.$router.push不起作用的问题。
---




譬如，在使用axios进行数据请求时，如下所示：
```
this.axios
.post(url, params, config)
.then(function(response) {
  // 成功
 if (response.data.error_code == 0) {
    this.$router.push({ path: "/" });
  }
})
.catch(function(error) {
  this.$message.error(error);
});
```
在`.then()`中包含了一个匿名函数，此时`this`指针指向已经改变，`this.$router.push()`是不起作用的，解决办法是在进行axios请求前缓存this指针，如下所示：
```
var self = this;

this.axios
 .post(url, params, config)
 .then(function(response) {
   // 成功
   if (response.data.error_code == 0) {
     self.$router.push({ path: "/" });
   }
 })
 .catch(function(error) {
   self.$message.error(error);
 });
```
其它类似的this指针指向改变的问题也可参照上述解决方法。
