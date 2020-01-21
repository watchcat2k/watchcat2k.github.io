---
layout: post
title: element-ui中的中国省市区级联选择器
date: 2020-01-21 00:00:00
categories: 
- Element-UI-前端UI框架
tags: 
- Vue-cli
- Element-UI
description: element-ui中的中国省市区级联选择器。
---



# 安装
以下教程参考自官方的教程：[https://www.npmjs.com/package/element-china-area-data](https://www.npmjs.com/package/element-china-area-data)

执行以下语句安装城市数据：
```
npm install element-china-area-data -S
```
# 导入
以vue-cli创建的项目为例，在vue文件中的`scrit`标签内导入数据：
```
import { regionData, CodeToText } from "element-china-area-data";
```

# 使用
如下代码所示：
```
<template>
  <div id="app">
    <div>
      <el-cascader
        size="large"
        :options="options"
        v-model="selectedOptions"
        @change="handleChange"
      >
      </el-cascader>
    </div>
  </div>
</template>

<script>
import { regionData, CodeToText } from "element-china-area-data";

export default {
  name: "app",
  data() {
    return {
      options: regionData,
      selectedOptions: []
    };
  },

  methods: {
    handleChange() {
      var loc = "";
      for (let i = 0; i < this.selectedOptions.length; i++) {
        loc += CodeToText[this.selectedOptions[i]];
      }
      alert(loc);
    }
  }
};
</script>
```

# 效果
如下图所示：

![在这里插入图片描述](https://gitee.com/watchcat2k/pictures_base/raw/master/2020/1/21-1.png)
