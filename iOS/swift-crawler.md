# 使用 Swift 寫爬蟲 (以 104 網站為例)

大綱 ：

1. 如何解析 html (使用 XPath) 
	- 環境建置 
	- XPath 查詢語法教學 
2. Swift 爬蟲套件使用方法  
3. 爬 104 公司頁面




## 如何解析 html (使用 XPath)

要寫爬蟲，要先理解**如何抓取網頁上特定的資訊**，本文會以一個 html 範例檔，搭配 XPath 語言，來一步一步介紹如何拿取畫面上的資訊。

首先要先了解 XPath 語言 是什麼，以下摘自[維基百科](https://zh.wikipedia.org/zh-tw/XPath)：

> XPath 即為 XML路徑語言（XML Path Language），它是一種用來確定XML文檔中某部分位置的語言。 XPath 基於 XML 的**樹狀結構**，**提供在資料結構樹中找尋節點的能力**。

簡單來說，可以將網頁的 html 架構比喻成一棵樹，使用 XPath 能幫助我們找到樹上的任何一個分支，也就是網頁的資訊。

為了要學習 XPath，要先介紹如何建置環境，以及 XPath 基本用法。

### 環境建置
要先將下方的 html 存成文字檔 `xpath.html`，並使用 chrome 開始。

```html
<!DOCTYPE html>
<html>
   <head>
   </head>
   <body>
      <div style="height: 100px;"></div>
      <h1 id="hi_id" class="h1_Class">XPath</h1>
      <a href="http://www.hosp.ncku.edu.tw/mis/48-netdisk/57-xml-xpath.html">補充資料 : XML XPath的選擇節點語法</a>
      <ul>
         <li class="a class">text 1</li>
         <li class="b class test">text 2</li>
         <li class="c class test">text 3</li>
         <li class="a class">text 4</li>
      </ul>
      <div>
         <h1>text</h1></div>
   </body>
</html>
```

下載 `xpath-helper` 的 [chrome plugin](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl?hl=zh-TW)

下載完安裝後，右上角就可以直接使用：
![](https://i.imgur.com/5B34GUW.png)

為了方便可以看到 html 原始碼，請在Chrome菜單中選擇 更多工具 > [開發者工具](https://developers.google.com/web/tools/chrome-devtools/?hl=zh-tw)

### XPath 查詢語法教學
#### 基本查詢
要把 html 結構想成一個階層式的架構，就像檔案路徑。

如果要取得 tag h1 時，就在左上角的 `QUERY` 輸入:  
`/html/body/h1`
![](https://i.imgur.com/ol0LV6I.png)

可以清楚地看到符合條件的結果會被 highlight 起來，因為 `xpath-helper` 會將找到的元素，新增 `class="xh-highlight"`，背景就變成黃色。

#### 全域搜尋
如果不確定要搜尋的 tag 在哪一層，就可以使用 `//` 來表示要搜尋在 html 裡面，所有 tag 為 `h1` 的：

`//h1`

![](https://i.imgur.com/08R2QRW.png)

#### 取第 n 個
如果相同的 tag 有很多個，想拿第一個可以在 tag 名稱後面加 `[1]`： 

`/html/body/ul/li[1]`

![](https://i.imgur.com/d6CBpXx.png)

拿最後一個可以加 tag 名稱加上 `[last()]`  

`/html/body/ul/li[last()]`

![](https://i.imgur.com/jJGcDpn.png)
#### 取得屬性

想拿屬性名稱只要打 `@屬性名稱` 就可以了

`/html/body/a/@href`

![](https://i.imgur.com/N27szIJ.png)


#### 條件式搜尋

找尋 tag 名稱是 `li` 並且 屬性 `class` 為 `a class`：   

`//li[@class='a class']`  


![](https://i.imgur.com/Efwjchz.png)
找尋 tag 名稱是 `li` 並且 值 為 `text 1`： 

`//li[text()='text 1']`

![](https://i.imgur.com/ji8eyc5.png)

找尋 tag 名稱是 `li` 並且 屬性 `class` 包含 `test`：   

`//li[contains(@class,'test')]`

![](https://i.imgur.com/EPPHisQ.png)

找尋 tag 名稱是 `li` 並且 值 包含 `text 1`：   

`//li[contains(text(),'text 1')]`

![](https://i.imgur.com/0Xr5271.png)

#### 分支條件
使用 `|` 合併多個搜尋結果： 

`//li[1]|//h1`

![](https://i.imgur.com/pYIATbG.png)

## Swift 爬蟲套件使用方法

會使用 [Kanna](https://github.com/tid-kijyun/Kanna) 套件來實作，先以剛剛的 html 當範例

### 取節點或屬性


```swift
import Kanna

let html = """
<!DOCTYPE html>
<html>
<head>
</head>
<body>
<div style="height: 100px;"></div>
<h1 id="hi_id" class="h1_Class">XPath</h1>
<a href="http://www.hosp.ncku.edu.tw/mis/48-netdisk/57-xml-xpath.html">補充資料 : XML XPath的選擇節點語法</a>
<ul>
<li class="a class">text 1</li>
<li class="b class test">text 2</li>
<li class="c class test">text 3</li>
<li class="a class">text 4</li>
</ul>
<div>
<h1>text</h1></div>
</body>
</html>
"""

if let doc = HTML(html: html, encoding: .utf8) {
    let nodeA = doc.xpath("/html/body/a").first!
    print(nodeA.innerHTML) 
    // Optional("補充資料 : XML XPath的選擇節點語法")
    
    print(nodeA.text)
    // Optional("補充資料 : XML XPath的選擇節點語法")
    
    print(nodeA["href"])
    // Optional("http://www.hosp.ncku.edu.tw/mis/48-netdisk/57-xml-xpath.html")
    
    let nodeAHref = doc.xpath("/html/body/a/@href").first!
    print(nodeAHref.innerHTML)
    // Optional(" href=\"http://www.hosp.ncku.edu.tw/mis/48-netdisk/57-xml-xpath.html\"")
    
    print(nodeAHref.text)
    // Optional("http://www.hosp.ncku.edu.tw/mis/48-netdisk/57-xml-xpath.html")
    
    print(nodeAHref["href"])
    // nil
}
```

因為透過 `xpath()` 方法拿回來是 `XPathObject`，是一個集合，所以要取 `first!`，  
藉由這兩個例子能發現 `nodeA` 是拿一整個 tag a，所以可以拿到文字，取出後如果要取屬性也可以用 `["attrname"]`，以這例子是拿 `href`；  
而 `nodeAHref` 是只有拿屬性 `href`，就拿不到裡面的文字了。

### 取大量節點


```swift
if let doc = HTML(html: html, encoding: .utf8) {
    let liNodes = doc.xpath("/html/body/ul/li")
    for liNode in liNodes {
        print(liNode["class"], liNode.text)
    }
}
// Optional("a class") Optional("text 1")
// Optional("b class test") Optional("text 2")
// Optional("c class test") Optional("text 3")
// Optional("a class") Optional("text 4")
```

## 爬 104 公司頁面

先到任意的公司頁面，本文以 [QNAP](https://www.104.com.tw/jobbank/custjob/index.php?r=cust&j=4c4a44263c36402230323c1d1d1d1d5f2443a363189j99) 為例，因為該公司個職缺較多。

### 使用 chrome 直接複製 xpath
先對想要查詢的資訊按右鍵，選`檢查`

![](https://i.imgur.com/lJFV1bt.png)

在對 node 點右鍵，選 `Copy XPath`
![](https://i.imgur.com/fkwU3a4.png)

接著就可以打開 `XPath Helper`，來驗證剛剛的 XPath 語法是否能拿到我們要的。

### 取得公司名稱
剛剛拿到的 XPath 是

`//*[@id="comp_header"]/ul/li[2]/h1`

我通常會把雙引號變成單引號，因為這樣丟到 swift 當 String 時，不用再 escape：

`//*[@id='comp_header']/ul/li[2]/h1`

然後會把`第幾個`改成`條件式搜尋`，避免以後元素的位置換了就抓錯的問題：

`//*[@id='comp_header']/ul/li[@class='comp_name']/h1`

這時就可以直接貼到程式裡面使用了：

```swift
let html = "要先透過 URLSession 拿到此網頁的 html，就不再贅述"
if let doc = HTML(html: html, encoding: .utf8) {
	let comp_name = doc.xpath("///*[@id='comp_header']/ul/li[@class='comp_name']/h1").first!.text
	print(comp_name)
	// Optional("QNAP_威聯通科技股份有限公司  ")
}
```

### 取得工作機會列表

如果只想拿`職務名稱`，就可以一樣看點右鍵，選 檢查然後點 Copy XPath，  
但如果想拿所有資料，包含`日期`、`職務名稱` 等等，就要觀察 html 的結構：

![](https://i.imgur.com/MsIQuAq.png)

看得出來有分兩種 `div class`：  
1. `joblist_cont focus` 是重點職務，亮紅燈的。  
2. `joblist_cont`

一樣針對這一層點右鍵，取得 `XPath`，會是：

`//*[@id="intro"]/form/div[4]`

可以修改成：

`//*[@id="intro"]/form/div[@class='joblist_cont focus']`

因為有兩種 class，所以可以用 `|` 來串連兩個搜尋結果


`//*[@id="intro"]/form/div[@class='joblist_cont focus']|//*[@id="intro"]/form/div[@class='joblist_cont']`

接著就可以開始寫程式了：

```swift

let html = "要先透過 URLSession 拿到此網頁的 html，就不再贅述"
if let doc = HTML(html: html, encoding: .utf8) {
  let jobNodes = doc.xpath("//*[@id="intro"]/form/div[@class='joblist_cont focus']|//*[@id="intro"]/form/div[@class='joblist_cont']")
  for jobNode in jobNodes {
      let jobnameNode = jobNode.xpath("ul/li/div[@class='jobname']/a").first!
      let name = jobnameNode.text!
      var date = jobNode.xpath("ul/li/div[@class='date']").first!.innerHTML!
      if date.contains("重點職務") {
          date = "重點職務"
      }
    
      print(name, date)
  }       
}     
```

透過這樣的方式就能取得`日期`、`職務名稱`等等，其中要注意到的，日期有兩種類型，
如果是重點職務就取不到日期，所以要使用 `innerHTML` 而不是 `text`。




