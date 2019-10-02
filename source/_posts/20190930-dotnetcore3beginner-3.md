---


title: 【ASP.Net Core 3.0應用程式開發-3】什麼是MVC？什麼是前後端分離？ ft.安裝EF Core 3.0、初始化資料模型對應
date: 2019-10-03 01:02:33
categories: [ASP.Net Core]
tags: [DotNetCore,ASP.Net Core 3.0,C#,Visual Studio,MVC,WebAPI,EF Core 3.0]
---

## 前言

接續上一篇我們建立完資料庫及資料表，這一篇會帶大家了解MVC程式架構的基本概念，以及什麼是前後端分離，最後在實作的部分會帶大家在上次建立的API專案中安裝Entity Framework Core 3.0（EF Core 3.0），並在專案中透過EF Core建立Model，該Model會對應到資料庫的資料表結構。

> 這一系列的文章比較偏向讓大家看完能夠有一些比較基本的觀念，並且會附加比較多的實作教學，因此可能解釋一些觀念的時候，不會用太精細的方式來與大家做探討，請大家多多見諒！主要想讓大家對觀念的理解可以剛好輔助實作就好，如果對觀念有更深的興趣歡迎尋找更多文章，或者看我文中提到的更深入的文章，也可以在下方回應與大家分享自己看到的資訊哦～！

<!--more-->

## 基本概念

### MVC概念

MVC是一種軟體的架構模式，主要是把軟體系統的程式分成三層：模型（Model）、視圖（View）和控制器（Controller）。主要是Model <-> Controller <-> View這樣的方式在做程式溝通。而我自己在開發過程中還會再多一層，這一層會放在Model以及Controller之間，可以把它稱作業務邏輯（Service），所以最後會變成Model <-> Service <-> Controller <-> View層這樣子，以下簡單以實作面讓大家理解一下每一層的作用：

- 視圖（View）：從Controller獲得資料，並且進行介面設計與渲染。
- 控制器（Controller）：做整體程式流程的控制，接收View的資料請求，並且呼叫Service來完成業務邏輯，最後將資料回傳到View進行顯示。
- 業務邏輯（Service）：負責業務邏輯的相關工作，會呼叫Model去撈取資料庫資料。
- 模型（Model）：與資料庫的資料表有對應關係，Service可以透過Model撈取資料庫中的資料。

![4](4.png)

簡單來說就是以上這樣，如果想要對MVC了解更多的可以先參考這篇網路上我覺得寫得不錯的討論文章，以及文章下面更多的資源：[MVC是一個巨大誤會](http://blog.turn.tw/?p=1539)

### 前後端分離

在上方的MVC架構中，我們有提到Controller在完成View的請求之後，會將資料傳到View上顯示，並且也有看到圖片中標示了「前端」與「後端」。而其實這邊的「View」指的就是前端的部分，在ASP.net 的MVC專案中，前端跟後端其實是無法「完全分開」的，如果你架設專案的伺服器壞了，是整個網站都一起會掛掉，簡單來說，前後端在這種MVC架構下其實是綁在一起的。

而前後端分離就如同他字面上的意思，將前端與後端完全分離，分成兩個部分，甚至是兩個專案來做。我們可以先看下面這張圖，當前端需要資料時，會發出請求給後端，後端去資料庫存取資料後，再把資料回傳到前端去。

![3](3.png)

而如果用更簡單的方式來畫圖，就會變成下面這副德性

![2](2.png)

而前後端分離就是在前端跟後端這個請求跟回傳的部分，不是透過在專案內連結的互傳，而是透過一個叫「API」的東西來進行交互。

而API是什麼東西呢？API的全稱叫做「應用程式介面 (Application Programming Interface)」，我會比較喜歡叫他“應用程式接口”，當兩個不同專案需要進行交互的時候，通常就會透過API來進行溝通，前端專案透過呼叫後端專案所開放出來的API，來存取後端專案的資源。

我用個飲料機的例子舉例，我今天想要喝一杯飲料，所以我（前端）透過去點擊飲料機上的按鈕（API）來叫飲料機給我飲料（後端）：

- 「我」想要一瓶「飲料」：「前端」發出「需求」
- 「飲料機上的按鈕」：「API」
- 「飲料機」給我「飲料」：「後端」回傳「資料」

就是像這個樣子，而所謂後端的API專案，就跟我們在第一堂課提到的建立WEB API專案的那個API是同一個意思，因此我們在未來的實作中將會透過.Net Core來做API與後端的邏輯，然後前端的專案則是用另外的專案來實作，之後會帶大家識做後端的API專案，並且再教大家如何透過軟體（Postman）或者前端去對後端API發出請求，大家就會很明瞭上面所說的這些東西了！

### 什麼是Entity Framework Core 3.0（EF Core 3.0）

![1](1.png)

Entity Framework Core是微軟新一代出的***Object-Relational Mapping（ORM）***框架，是以.Net Core實作出來的。主要適用於透過程式碼來處理Model以及資料庫結構對應的工作。

.Net Core是從.Net Framework發展而來，而Entity Framework Core 3.0（EF Core 3.0）則是從Entity Framework發展而來的，算是進化版。給使用者呼叫的函式介面類似於Entity Framework，但是底層幾乎是砍掉重練的。

> ***Object-Relational Mapping（ORM）***：物件關係映射，在MVC架構中「Model」就是以ORM的方式運作的，主要作用就是在關聯式資料庫與實體的物件之間做一個對應，這樣我們就可以透過程式去操作物件，效果等同於我們去下SQL指令來對資料庫做操作，可以讓開發者在程式中以更簡易的方式對資料庫進行操作。

這個套件大家就看一下概略的介紹，有興趣可以去查詢官方的介紹比較清楚，接下來我就帶大家來開始在專案中裝上這個套件啦！

## 開始實作

### 安裝EF Core 3.0.0

以下步驟將帶大家安裝兩個套件：「Microsoft.EntityFrameworkCore.SqlServer」及「Microsoft.EntityFrameworkCore.Tools」，以下步驟將以「Microsoft.EntityFrameworkCore.SqlServer」套件為例

1. 再次開啟在第一篇所建立的WebApi專案

![5](5.png)

2. 接下來選上面的「工具 -> NuGet套件管理員 -> 管理方案的NuGet套件」，開啟圖形化的NuGet套件安裝管理員（上方的「套件管理主控台」則是指令輸入畫面，可以使用指令安裝套件）

> NuGet：是Microsoft 支援的共用程式碼機制，它定義了如何建立、裝載和取用.NET中的「套件」。

![6](6.png)

3. 看到NuGet的圖形化管理介面之後，點擊「瀏覽」分頁，並且輸入「Microsoft.EntityFrameworkCore.SqlServer」，找到該套件之後點擊它，右邊會顯示此套件的資訊

![7](7.png)

4. 在右邊視窗勾選要安裝此套件的專案，並且點選「安裝」

![8](8.png)

5. 等待套件安裝完成

![9](9.png)

6. 會出現以下提示，勾選「不要再顯示這個訊息」，並點擊「確定」

![10](10.png)

7. 出現「接受授權」的提示，點擊「我接受」

![11](11.png)

8. 安裝完成後，套件管理的右邊視窗就會顯示現在安裝的版本，確認一下是不是EF Core 「3.0.0」版本

![12](12.png)

9. 而在右邊「方案總管」視窗也可以看到，在「相依性->套件」的資料夾中也有寫已安裝的套件有哪些

![13](13.png)

10. 以相同的步驟再安裝「Microsoft.EntityFrameworkCore.Tools」套件

![17](17.png)

### 透過EF Core 3建立模型對應

我們可以透過EF Core套件，幫我們自動在程式中建立資料模型，對應到我們第二堂課所建立的資料庫資料表，而接下來就要帶大家了解一下怎麼做到這件事。

1. 首先點擊上方「工具」，找到「NuGet套件管理員」並選擇「套件管理主控台」，準備用下指令的方式對應資料庫

![14](14.png)

2. 開啟後可以在下方的視窗看到如下畫面，底下「PM>」就是要輸入指令的位置

![15](15.png)

3. 下指令安裝成功後會看到以下畫面，點右上「ｘ」關閉並重新打開此套件管理主控台的命令視窗

![16](16.png)

4. 接下來輸入以下指令，建立實體的資料對應模型，對應到先前建立的資料庫與資料表：

```shell
Scaffold-DbContext "Data Source=localhost;user id=dotnetcore;password=dotnetcore; persist security info=True; Initial Catalog=dotnetcore;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Force
```

![18](18.png)

指令的解釋如下，不知道的人可以看，或者直接去[官方文件](https://docs.microsoft.com/zh-tw/ef/core/miscellaneous/cli/powershell)看一下

- Scaffold-DbContext：EFCore中產生資料實體模型的指令
- Data Source：資料庫的來源，這邊是輸入本機IP，把專案架上伺服器時會需要修改
- user id：資料庫登入角色的名稱
- password：資料庫登入角色的密碼
- persist security info：宣告為 True 時，代表當SQL連線已經建立，也會將密碼儲存在記憶體中，供後續程式引用
- Initial Catalog：就是要對應的資料庫
- Microsoft.EntityFrameworkCore.SqlServer：指定套件去進行動作
- -OutputDir：將檔案放入目錄。 路徑是相對於專案目錄，這邊要把建立出的實體資料模型放入「Models」資料夾中
- -Force：覆寫現有的檔案。

5. 完成後就會看到右邊的檔案目錄多了「Models」資料夾，並且裡面多出了「dotnetcoreContext.cs」以及「Member.cs」

![19](19.png)

6. 以下解釋一下剛剛自動對應產生的兩個檔案分別的功能：

- dotnetcoreContext.cs：此檔案其實是資料庫名稱+Context組成的檔名，裡面會放資料庫連線的資訊以及模組對應的資訊，該檔案是透過EFCore套件自動產生，因此初期不會修改到

  ![20](20.png)

- Member.cs：此檔案是資料庫中資料表的模型，裡面的資料欄位跟型態會對應到資料庫的資料型態。

  ![21](21.png)

看到以上畫面就代表你成功安裝了EF Core 3.0套件，並且也透過指令將資料庫與資料表對應到程式碼中，未來如果資料庫結構有更新時，只要打開「NuGet套件管理器主控台」，再次輸入以下指令即可重新對應並覆蓋程式碼囉：

```shell
Scaffold-DbContext "Data Source=localhost;user id=dotnetcore;password=dotnetcore; persist security info=True; Initial Catalog=dotnetcore;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Force
```

---

下一篇將帶大家建立MVC + Service的程式架構，並且寫一個簡單的會員子系統操作給大家當做示範。