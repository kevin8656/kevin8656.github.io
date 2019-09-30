---
title: 【ASP.Net Core 3.0應用程式開發-3】什麼是MVC？什麼是前後端分離？ ft.安裝EF Core 3.0、初始化資料模型對應
date: 2019-09-30 20:32:25
categories: [ASP.Net Core]
tags: [DotNetCore,ASP.Net Core 3.0,C#,VisualStudio,MVC,WebAPI,EF Core 3.0]
---

## 前言

接續上一篇API的專案建立教學，這一篇會帶大家了解MVC程式架構的基本概念，以及什麼是前後端分離，最後在實作的部分會帶大家使用SQL Server建立資料庫與資料表，並且安裝Entity Framework Core 3.0（EF Core 3.0），在上次建立的API專案中建立Model對應到資料庫的資料表結構。

先說一下，這一系列的文章比較偏向讓大家看完能夠有一些比較基本的觀念，並且會附加比較多的實作教學，因此可能解釋一些觀念的時候，不會用太精細的方式來與大家做探討，請大家多多見諒！主要想讓大家對觀念的理解可以剛好輔助實作就好，如果對觀念有更深的興趣歡迎尋找更多文章，或這看我文中提到的更深入的文章，也可以在下方回應與大家分享自己看到的資訊哦～！

<!--more-->

## 基本概念

### 什麼是Entity Framework Core 3.0（EF Core 3.0）

![1](1.png)

Entity Framework Core是微軟新一代出的***Object-Relational Mapping（ORM）***框架，是以.Net Core實作出來的。主要適用於透過程式碼來處理Model以及資料庫結構對應的工作。

.Net Core是從.Net Framework發展而來，而Entity Framework Core 3.0（EF Core 3.0）則是從Entity Framework發展而來的，算是進化版。給使用者呼叫的函式介面類似於Entity Framework，但是底層幾乎是砍掉重練的。

> ***Object-Relational Mapping（ORM）***：物件關係映射，主要作用就是在關聯是資料庫與實體的物件之間做一個對應，這樣我們就可以透過程式去操作物件，效果等同於我們去下SQL指令來對資料庫做操作，可以讓開發者在程式中以更簡易的方式對資料庫進行操作。

### MVC概念

MVC是一種軟體的架構模式，主要是把軟體系統的程式分成三層：模型（Model）、視圖（View）和控制器（Controller）。主要是Model <-> Controller <-> View這樣的方式在做程式溝通。而我自己在開發過程中還會再多一層，這一層會放在Model以及Controller之間，可以把它稱作業務邏輯（Service），所以最後會變成Model <-> Service <-> Controller <-> View層這樣子，以下簡單以實作面讓大家理解一下每一層的作用：

- 視圖（View）：從Controller獲得資料，並且進行介面設計與渲染。
- 控制器（Controller）：做整體程式流程的控制，接收View的資料請求，並且呼叫Service來完成業務邏輯。
- 業務邏輯（Service）：負責業務邏輯的相關工作，會呼叫Model去撈取資料庫資料。
- 模型（Model）：與資料庫的資料表有對應關係，Service可以透過Model撈取資料庫中的資料。

簡單來說就是以上這樣，如果想要對MVC了解更多的可以先參考這篇網路上我覺得寫得不錯的討論文章，以及文章下面更多的資源：[MVC是一個巨大誤會](http://blog.turn.tw/?p=1539)

### 前後端分離