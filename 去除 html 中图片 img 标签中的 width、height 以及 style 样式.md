---
title: 去除 html 中图片 img 标签中的 width、height 以及 style 样式
tags: html,样式,正则表达式
grammar_cjkRuby: true
date: 2018-11-23
---


``` javascript
function formatImg(html){
            var newContent= html.replace(/<img[^>]*>/ig,function(match,capture){
            var match = match.replace(/(style="(.*?)")|(width="(.*?)")|(height="(.*?)")/ig, '');
            return match;
        });
        return newContent;
}
```