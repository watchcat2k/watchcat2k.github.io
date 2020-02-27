---
layout: post
title: Vue项目中使用axios和element ui实现文件上传时的进度显示
date: 2020-02-20 00:00:00
categories: 
- Vue-前端框架
tags: 
- Vue
- Vue-cli
- Axios
- Element-UI
description: Vue项目中使用axios和element ui实现文件上传时的进度显示。
---


html代码如下：
```
<div v-if="progressSeen">
  <el-progress
    :text-inside="true"
    :stroke-width="15"
    :percentage="progress"
    status="success"
  ></el-progress>
</div>
```
js代码如下：
```
export default {
  data() {
    return {
      progress: 0,
      progressSeen: false,
    }
  },
  methods: {
    submitForm(formName) {
      //显示进度
      this.progressSeen = true;

      var params = new FormData();
      params.append(xxx, xxx);

      var config = {
        headers: {
          "Content-Type": "multipart/form-data"
        },
        onUploadProgress: progressEvent => {
          var complete =
            ((progressEvent.loaded / progressEvent.total) * 100) | 0;
          this.progress = complete;
        }
      };

      var url = this.$store.state.baseUrl + "/video/upload/";

      //缓存this指针
      var self = this;

      this.axios
        .post(url, params, config)
        .then(function(response) {
            //...
        })
        .catch(function(error) {
          self.$message.error(error);
        });
        }
    }
  }
}
```

