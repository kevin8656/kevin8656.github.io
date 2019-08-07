---
title: 在JavaScript中將base64/jpeg轉換成base64/png格式
date: 2019-08-07 11:53:25
categories: [JavaScript]
tags: [blob,Base64,JS圖片格式轉換]
---

## 前言

最近開始跟一間公司配合遠端工作，主要是做vue.js的專案維護跟開發工作，然後有個需求是要在系統上使用WebCam接上攝像頭，並且用攝像頭拍照之後，將照片上傳到使用者檔案裡。

原先系統中就有上傳圖片的功能，當初api設計時，參數是接收base64的png格式，並且會在後端轉檔成jpeg後儲存，但我裝了[vue-web-cam](https://github.com/VinceG/vue-web-cam)這套件之後，才發現他Capture WebCam之後拿到的base64格式是jpeg，若我將這圖片資料原封不動丟到以前的上傳圖片API，圖片在後端轉檔後就會損毀，如下圖

<!--more-->

![](02.jpg)

因此我只有兩個選項

1. 等待後端將API改為可以接收png & jpeg兩種格式的資料
2. 直接在前端取得base64圖片檔時，從jpeg格式轉為png

因為後端工程師剛好在忙，所以我就直接自己嘗試轉格式看看了

## 轉檔過程

```javascript
    async uploadCapture() {
      const { customerId } = this.$store.getters;
      const payload = {};
      payload.id = customerId;
      const base64png = await this.jpeg2png(this.img);
      payload.canvas = this.img;
      this.$emit("dialogWebCamUploadFiles", payload);
      this.handleClose();
    },
    jpeg2png(base64jpeg) {
      return new Promise(resolve => {
        const img = new Image();
        img.setAttribute("src", base64jpeg);
        img.setAttribute("crossOrigin", "anonymous");
        img.onload = () => {
          const canvas = document.createElement("canvas");
          canvas.width = img.width;
          canvas.height = img.height;
          const ctx = canvas.getContext("2d");
          ctx.drawImage(img, 0, 0);
          const dataURL = canvas.toDataURL("image/png");
          resolve(dataURL);
        };
      });
```

這邊我主要是在我上傳檔案（uploadCapture）這個function中先將this.img這個base64/jpeg格式的資料丟入jpeg2png的function裡面轉檔，然後再將轉好的base64png檔案傳至後端儲存。在過程中有使用到canvas這東西，有興趣多了解的人可以[點這裡過去看](https://developer.mozilla.org/zh-TW/docs/Web/API/Canvas_API)

而網路上也有找到一個別人寫的文章是專門在講JavaScript各種圖片格式轉檔語法的，雖然我原封不動抓過來用沒辦法用xDD但是我修修改改之後就可以用了，也算是有幫助，在這邊把裡面用到的各種轉檔方式也放進這篇文章，提供以後遇到此類問題的開發者可以更快找到這些轉來轉去的方法。感謝[image 各種型態轉換(blob, dataURL, canvas) in JavaScript]([https://chiayilai.com/image-%E5%90%84%E7%A8%AE%E5%9E%8B%E6%85%8B%E8%BD%89%E6%8F%9Bblob-dataurl-canvas-in-javascript/](https://chiayilai.com/image-各種型態轉換blob-dataurl-canvas-in-javascript/)) 的作者

---

### 以下紀錄來自[image 各種型態轉換(blob, dataURL, canvas) in JavaScript]([https://chiayilai.com/image-各種型態轉換blob-dataurl-canvas-in-javascript/](https://chiayilai.com/image-各種型態轉換blob-dataurl-canvas-in-javascript/)) 文章

### blob 轉成 dataURL

blob 和 file 都可以使用，從 blob 讀取資料的唯一方式就是使用 FileReader。

```javascript
function blobtoDataURL(blob, callback) {
    var fr = new FileReader();
    fr.onload = function(e) {
        callback(e.target.result);
    };
    fr.readAsDataURL(blob);
}

blobtoDataURL(blob, function (dataURL){
    console.log(dataURL);
});
```

### dataURL 繪製到 canvas

新增一個 image 和 canvas，image src 屬性帶入 dataURL，然後用 canvas drawImage 繪製剛才新增的 image。之後就可以用 canvas 來幫 image 做很多事，例如裁切、縮放、打字、畫線條等等。

```javascript
var img = new Image(),
    canvas = document.createElement('canvas'),
    ctx = canvas.getContext('2d');
img.onload = function(){
    ctx.drawImage(img);
};
img.src = dataURL;
```

### canvas 轉成 dataURL

這個最簡單。

```javascript
canvas.toDataURL(); // 預設會轉成 image/png
canvas.toDataURL('image/jpeg', 0.8); // 可指定 jpeg 品質
```

### canvas 轉成 blob

```javascript
canvas.toBlob(function(blob){
    console.log(blob);
}, "image/jpeg", 0.8);
```

### dataURL 轉成 blob

```javascript
function dataURItoBlob(dataURI) {
    // convert base64/URLEncoded data component to raw binary data held in a string
    var byteString;
    if (dataURI.split(',')[0].indexOf('base64') >= 0)
        byteString = atob(dataURI.split(',')[1]);
    else
        byteString = unescape(dataURI.split(',')[1]);

    // separate out the mime component
    var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];

    // write the bytes of the string to a typed array
    var ia = new Uint8Array(byteString.length);
    for (var i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
    }

    return new Blob([ia], {type:mimeString});
}
```

