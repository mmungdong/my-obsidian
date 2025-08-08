---
title: Home
layout: default
---
# 🏠 知识库主页

## 📋 快速导航
- [[目录]] | [[索引]] | [[待办事项]]
- [Hexo博客管理](hexo/) | [项目文档](projects/)

## 🔍 最近更新的Hexo文件LIST 
FROM "hexo"
SORT file.mtime DESC
LIMIT 10
## 📑 内容大纲style: nestedList
minLevel: 1
maxLevel: 3
## 🗂️ 分类浏览

### 笔记类型
- 📝 [[日常笔记]]
- 🔬 [[研究笔记]]
- 📚 [[阅读笔记]]
- 💡 [[灵感记录]]

### 项目分类
- 🌐 [[Hexo博客]]
- 🔧 [[编程开发]]
- 📊 [[数据分析]]
- 📝 [[写作创作]]

## 🏷️ 热门标签TAG CLOUD
FROM ""
WHERE file.tags
LIMIT 20
## 🔖 标签快速查找
- #hexo | #blog | #writing
- #programming | #javascript | #python
- #note-taking | #productivity | #react
- #research | #idea

## 📊 笔记统计TABLE 
length(rows) as "笔记数量"
FROM ""
GROUP BY folder
SORT length(rows) DESC
## 📅 最近编辑LIST 
FROM ""
WHERE file.mtime > date(today) - dur(7 days)
SORT file.mtime DESC