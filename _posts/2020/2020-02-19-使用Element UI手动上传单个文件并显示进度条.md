---
layout: post
title: 使用Element UI手动上传单个文件并显示进度条
date: 2020-02-19 00:00:00
categories: 
- Element-UI-前端UI框架
tags: 
- Vue-cli
- Element-UI
description: 使用Element UI手动上传单个文件并显示进度条。
---




# 场景描述
使用Element UI的upload组件**手动**上传文件**单个**文件，如果选择了多个文件，会**覆盖**原来要上传的文件，以最新选择的文件为准。上传不使用upload组件自带的上传方式，而是**使用axios**上传。

# 实现过程
部分做法参考自：[https://www.cnblogs.com/lovemomo/p/11777608.html](https://www.cnblogs.com/lovemomo/p/11777608.html)

首先upload的html代码如下：
```
<el-upload
  class="upload-demo"
  ref="upload"
  action
  :before-upload="beforeUploadVideo"
  :on-change="handleChange"
  :file-list="fileList"
  :auto-upload="true"
>
  <el-button slot="trigger" size="small" type="primary"
    >选取文件</el-button
  >
</el-upload>
```
为了只能选择一个文件，我们需要利用`on-change`方法，始终令file-list保留最新的一项。并且，为了能用axios上传该文件，我们需要令`auto-upload=true`。虽然我们设置了upload组件自动上传，但其实这是为了该组件能执行`before-upload`方法，在该方法中，我们可以获得最新选择的文件的`file`对象，方便后面用axios上传，然后在该方法内始终阻止upload组件的自动上传。如果想要显示进度条，参考[https://blog.csdn.net/qq_36272282/article/details/104539860](https://blog.csdn.net/qq_36272282/article/details/104539860)。
代码如下所示：
```
export default {
  data() {
      fileList: [],
      ruleForm: {
        videoFile: ""
      }
    };
  },
  methods: {
    handleChange(file, fileList) {
      this.fileList = [fileList[fileList.length - 1]];
    },
    beforeUploadVideo(file) {
      //上传前回调
      var fileSize = file.size / 1024 / 1024 < 500;
      if (
        [
          "video/mp4",
          "video/ogg",
          "video/flv",
          "video/avi",
          "video/wmv",
          "video/rmvb",
          "video/mov"
        ].indexOf(file.type) == -1
      ) {
        this.$message.error("不支持此视频格式");
        this.fileList = [];
      } else if (!fileSize) {
        this.$message.error("视频大小不能超过500MB");
        this.fileList = [];
      } else {
        this.ruleForm.videoFile = file;
      }

      // 不使用upload自带的上传方式，而是使用axios，所以阻止upload自带的上传
      return false;
    },
    submitForm(formName) {
      // 这里把提交表单方法绑定到指定按钮中
      ...
      var params = new FormData();
      params.append("video_data", this.ruleForm.videoFile);
      ...
    }
  }
};
```

Element UI的upload组件自带了部分动画， 如果不想要这些动画，可以利用`>>>`关键符号修改Element UI底层代码：
```
<style lang="scss" scoped>

.upload-demo {
  display: flex;
}
>>> .el-list-enter-active,
>>> .el-list-leave-active {
  transition: none;
}

>>> .el-list-enter,
>>> .el-list-leave-active {
  opacity: 0;
}
>>> .el-upload-list {
  height: 40px;
}

</style>
```
