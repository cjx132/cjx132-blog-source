---
title: "Vue入门"
date: 2020-02-10T17:54:03+08:00
categories:
  - Vue
tags:
  - Vue
---

[参考链接](https://blog.csdn.net/okForrest27/article/details/106849246)

[饿了么ui](https://element.eleme.cn/#/zh-CN)

[vue-router](https://router.vuejs.org/zh/)

[vue-axios](http://www.axios-js.com/zh-cn/docs/vue-axios.html)

# 绑定

-   数据绑定  
    { { 变量名 } }
    
-   html代码绑定  
    < p v-html=”变量名” > < / p >
    
-   标签属性绑定class,style,key等,可简写为:class等  
    < div v-bind:class=”color” > test < / div>
    
-   条件渲染，标签内容是否被看到  
    < p v-if=”变量名” >test < / p >变量名值的真假决定p标签内容是否被看到
    
-   v-on指令，事件绑定  
    v-on : click=” f ( ‘ abc ‘ $ event ) “ f ( str,e ) 为在Vue初始化时声明的函数（**函数必须写在vue的methods属性中**），v-on指令可绑定html中的所有事件
    
-   v-model,表单，复选框，单选框，多行文本框输入绑定，实现真正的双向绑定。
    
    表单的值和vue中data中的一个变量绑定，表单值的变化引起这个变量的变化，这个变量的变化也引起表单的值的变化（**表单的值就是变量的值，变量的值也是表单的值**）除此之外：**这个变量可以在视图的任意位置使用，还可以赋初始值**
    

## 指令

-   < p v-if=”变量名” >test < / p >变量名值的真假决定p标签内容是否被看到
    
-   v-bind指令
    
    1.  < div v-bind:class=”color” > test < / div>
        
    2.  < div v-bind:class=”{active : isActive, green : isGreen}” > test < / div>
        
        > isActive和isGreen在Vue初始化时声明并赋予其真值或假值，决定active和green是否被启用，可在style处编写active,green的字体，颜色等
        
    3.  < div v-bind:class=”\[isActive ? ‘active’ : ‘’, isGreen ? ‘green’ : ‘’ \] “> test < / div>
        
    4.  < div :style=” { color : color , frontSize : size , background : isRed : ‘red’ : ‘’ } “
        
        > 在Vue初始化时声明color,size,isRed的值
        
    5.  :title=”\`测试报告${item.key}\`“
        
        > **\`\`中间包裹的是字符串，添加${}使其变为变量**
        
    6.  :title=”\`测试报告\`+item.key”
        
        > **\`\`中间包裹的是字符串，+使双引号内整个变为字符串**
        
    7.  :status=”status\_text==\`采纳\`?\`success\`:\`error\`“
        
        > **\`\`中间包裹的是字符串**
        
    8.  :status=”status\_text | badgeStatus”**status\_text作为参数传入badgeStatus**
        
        ```plain
        filters: {//filters{}为vue返回的一个属性，和data(){},methods:{}并列
          badgeStatus: function(item) {
            if (item == "采纳") {
              return "success";
            } else {
              return "error";
            }
          }
        },
        ```
        
-   v-for指令，列表渲染
    
    1.  > < li v-for=” item , index in items “ : key=”index”> **items为在Vue初始化时声明的对象列表**
        > 
        > { { idnex } } { { item.message } }
        > 
        > **其中index为索引,item为数组的每一项,message为对象列表中每个对象的key（注意item放在第一个，index放在第二个）:key 是v-bind:key的缩写，给列表添加key属性**  
        > < /li >
        
    2.  > < li v-for=” value , key in object “ : key=”key”> **object为在Vue初始化时声明的对象**
        > 
        > { { key } } : { { value } }
        > 
        > **其中key为键,value为键值,（注意value放在第一个，key放在第二个）:key 是v-bind:key的缩写，给列表添加key属性**  
        > < /li >
        

# 快速开始

## 终端命令：

```plain
vue init webpack hello-vue
cd hello-vue\
npm install vue-router --save-dev
npm i element-ui -S
npm install
npm install axios --save
#安装SASS加载器
cnpm install sass-loader node-sass --save-dev
#启动测试
npm run dev
```
