---
title: 【Git學習筆記】Git Commit Log 最佳實踐
date: 2019-07-28 20:58:37
categories: [Git]
tags: [Git,Commitizen]
---
## 前言

在實作專案過程中時常會需要使用版本控制，而目前我都是使用Git來作為我的版控環境。

最近剛好在公司的一堂CI/CD課程下課後，與講師聊到了git flow的管理，以及一些使用Git的小技巧，

無意聽說了“最佳實踐”這個名詞，據說是從AngularJS的規範出來的，這部分我還沒有詳細研究。

而在Git的Commit Log也可以做“最佳實踐”，關於最佳實踐的觀念，有興趣的朋友可以[看這裡](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.uyo6cb12dt6w)

而講師介紹給了我若想要快速地做到Commit Log的最佳實踐，可以使用**Commitizen**這個套件比較方便快速上手。
<!--more-->
此套件的GitHub[連結在此](https://github.com/commitizen/cz-cli)，若想要在非NodeJS的專案裝這個套件來使用的話也別忘了先使用以下指令去建立出package.json檔案哦！

```bash
# npm init
```

## Vue.js專案安裝Commitizen練習

在還沒有時間深入研究整個最佳實踐的規範之前，我用Vue CLI建立了一個新專案，並且在其中安裝上Commitizen這個套件，稍微玩了一下，若你是已經習慣使用指令來進行git版本控制的人，這個套件將會非常適合你。

接下來就把我自己在Vue專案上亂玩的步驟記錄一下，讓對這個套件有興趣的人可以簡單的試用一下，看一下Commitizen可以達到得Commit Log規範成效。

### 初始化Vue.js專案
