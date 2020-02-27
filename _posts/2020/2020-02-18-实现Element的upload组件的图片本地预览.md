---
layout: post
title: 实现Element的upload组件的图片本地预览
date: 2020-02-18 00:00:00
categories: 
- Element-UI-前端UI框架
tags: 
- Vue-cli
- Element-UI
- Javascript
description: 实现Element的upload组件的图片本地预览。
---


@[TOC]()


upload组件的html代码如下：
```
<el-upload
 class="avatar-uploader"
  action=""
  :show-file-list="false"
  :before-upload="beforeAvatarUpload"
  v-model="ruleForm.coverFile"
>
  <img
    v-if="ruleForm.coverUrl"
    :src="ruleForm.coverUrl"
    class="avatar"
  />
  <i v-else class="el-icon-plus avatar-uploader-icon"></i>
</el-upload>
```

在vue组件中的js代码如下：
```
return {
  ruleForm: {
    coverUrl: "",
    coverFile: ""
  },
  methods: {
	  beforeAvatarUpload(file) {
	      const isJPG = file.type === "image/jpeg";
	      const isPNG = file.type === "image/png";
	      const isLt1M = file.size / 1024 / 1024 < 1;
	
	      if (!isJPG && !isPNG) {
	        this.$message.error("上传头像图片只能是 JPG 或 PNG 格式!");
	      } else if (!isLt1M) {
	        this.$message.error("上传头像图片大小不能超过 1MB!");
	      } else {
	        this.ruleForm.coverFile = file;
	        this.imagePreview(this.ruleForm.coverFile);
	      }
	
	      // 不使用upload自带的上传方式，而是使用axios，所以阻止upload自带的上传
	      return false;
	    },
    imagePreview: function(file) {
      var self = this;
      //定义一个文件阅读器
      var reader = new FileReader();
      //文件装载后将其显示在图片预览里
      reader.onload = function(e) {
        //将bade64位图片保存至数组里供上面图片显示
        self.ruleForm.coverUrl = e.target.result;
      };
      reader.readAsDataURL(file);
    }
  }
}
```
