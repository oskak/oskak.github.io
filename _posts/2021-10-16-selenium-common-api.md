---
title: Selenium 常用 API
tags: [Selenium]
---

## WebDriver 常用 API
| 方法 | 描述 |
|------|------|
| get(String url）| 访问目标 url 地址，打开网页 |
| getCurrentUrl() |	获取当前页面 url 地址 |
| getTitle() | 获取页面标题 |
| getPageSource() |	获取页面源代码 |
| close() |	关闭浏览器当前打开的窗口 |
| quit() | 关闭浏览器所有的窗口 |
| findElement(by) |	查找单个元素 |
| findElements(by) | 查到元素列表，返回一个集合 |
| getWindowHandle()	| 获取当前窗口句柄 |
| getWindowHandles() | 获取所有窗口的句柄 |

## WebElement 常用 API
| 方法 | 描述 |
|-----|------|
| click() |	对元素进行点击 |
| clear() |	清空内容（如文本框内容） |
| sendKeys(...)	| 写入内容与模拟按键操作 |
| isDisplayed() | 元素是否可见（true:可见，false：不可见） |
| isEnabled() |	元素是否启用 |
| isSelected() | 元素是否已选择 |
| getTagName() | 获取元素标签名 |
| getAttribute(attributeName) |	获取元素对应的属性值 |
| getText() | 获取元素文本值（元素可见状态下才能获取到） |
| submit() | 表单提交 |

## 元素等待机制
### 硬性等待
硬性等待就是不管你浏览器元素是否加载完成，都要进行等待设置好的时间，利用 Java 语言中的线程类 `Thread` 中的 `sleep` 方法，进行强制等待。这种方法在一定的程度上是可以解决元素加载过慢的情况，但是不建议使用该方法。

### 隐式等待
隐式等待的理解，就是我们通过代码设置一个等待时间，如果在这个等待时间内，网页加载完成后就执行下一步，否则一直等待到时间截止。这种方法相对于硬性等待显的会灵活一点，但是隐式等待也有个弊端，因为这个设置是全局的，程序需要等待整个页面加载完成，直到超时，有时候我需要找的那个元素早就加载完成了，只是页面上有个别其他元素加载比较慢，程序还是会一直等待下去。

### 显式等待
​显示等待是**等待指定元素**设置的等待时间，在设置时间内，默认每隔0.5s检测一次当前的页面这个元素是否存在，如果在规定的时间内找到了元素则执行相关操作，如果超过设置时间检测不到则抛出异常。默认抛出异常为：`NoSuchElementException`。推荐使用显式等待。

查找元素是否已经加载出来：
```
WebDriverWait wait = new WebDriverWait(driver, 5);
// 查找id为“kw"的元素是否加载出来了（已经在页面DOM中存在）
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("kw")));

// 在设定时间内找到后就返回，超时直接抛异常
```

### 页面加载超时设置
通过 `TimeOuts` 对象进行全局页面加载超时的设置，该设置必须放置 `get` 方法之前：
```
driver.manage().timeouts().pageLoadTimeout(5, TimeUnit.SECONDS);
driver.get("https://www.baidu.com");
```

## iframe 切换
`<iframe>` 是什么？`<iframe>` 标签是一个内联框架，即用来在当前 HTML 页面中嵌入另一个文档的（或网页嵌套网页），且所有主流浏览器都支持 `<iframe>` 标签。有时候我们定位元素的时候，发现怎么都定位不了，这时候需要查一查要定位的元素是否在 `<iframe>` 里面。

`main.html`:
```
<html>
<head>
  <title>FrameTest</title>
</head>
<body>
  <div id="id1">this is main page's div!</div>
  <input type="text" id="maininput" />
  <br/>
  <iframe id="frameA" frameborder="0" scrolling="no" style="left:0;position:absolute;" src="frame.html"></iframe>
</body>
</html>
```

`frame.html`:
```
<html>
<head>
  <title>this is a frame!</title>
</head>
<body>
  <div id="div1">this is iframes div，</div>
  <input id="iframeinput"></input>
</body>
</html>
```

使用 Selenium 操作浏览器时，如果需要操作 `<iframe>` 中的元素，首先需要切换到对应的内联框架中。见[7.2.iframe 切换](https://www.cnblogs.com/tester-ggf/p/12602211.html#3227301208)。

如果已经切换进入了其中的一个 `<iframe>` 中，再想对 `<iframe>` 外的元素进行操作，需要切换回到默认的页面中：
```
// 切换到默认内容页面
driver.switchTo().defaultContent();
```

## 浏览器窗口的切换
有时候在操作浏览器，可能打开了一个新的窗口，这个时候如果要对新窗口的元素进行操作，需要切换到新窗口中去，怎么去切换呢？在 Selenium 中有个叫**句柄**的概念。

​什么是句柄？简单理解就是浏览器窗口的一个标识，浏览器打开的每个窗口都有唯一的一个标识，也就是句柄，我们可以通过句柄来进行窗口之间的切换，从而来达到我们操作不同窗口的元素的目的。

多窗口之间的切换方法：
```
/**
* 切换窗口的方法
* 通过传入一个标题来找到我们需要的窗口。
* @param title 窗口的标题
*/
public void switchWindow(String title){
    Set<String> handles = driver.getWindowHandles();
    // 切换窗口的方式--循环遍历handles集合
    for (String handle : handles) {
        //判断是哪一个页面的句柄？？--根据什么来判断？？？title
        if(driver.getTitle().equals(title)){
        break;
    }else{
        //切换窗口--根据窗口标识来切换
        driver.switchTo().window(handle);
    }
}
```

## 浏览器窗口操作
WebDriver 给我们提供了一个 `Window` 对象，专门用于对窗口的设置。对象获取方法：
```
Window window = driver.manage().window();
```

`Window` 对象的方法有：

| 方法 | 描述 |
|-----|------|
| window.maximize() |	将浏览器窗口最大化。|
| window.getPosition() |	获取窗口的位置，返回 `Point` 对象，包含浏览器左上角的坐标位置。通过 `point.x` 和 `point.y` 来获取到。|
| window.setPosition(Point) |	指定浏览器窗口左上角的坐标位置，创建一个`Point` 对象，设置对象的 `x` 和 `y` 坐标即可。|
| window.getSize() | 获取窗口尺寸（宽和高），返回一个 `Dimension` 对象，通过该对象调用 `getHeight()` 和 `getWidth()` 来获取高度和宽度。|
| window.setSize(Dimension)	 | 设置窗口大小，创建一个 `Dimension` 对象，设置对象的高度和宽度。|

## 浏览器导航操作
WebDriver 提供了 `Navigation` 对象来对浏览器进行导航操作，如：前进、后退、刷新等。`Navigation` 对象获取：
```
Navigation navigate = driver.navigate();
```

`Navigation` 对象提供的方法：

| 方法 | 描述 |
|-----|------|
| navigate.to(url)	| 跳转到指定 url,和 webdriver 使用 `get` 方法是一样的。|
| navigate.refresh() |	刷新当前页面。|
| navigate.back()	| 浏览器回退操作。|
| navigate.forward() |	浏览器前进操作。|

## 模拟鼠标
`Actions` 类中鼠标操作常用方法：

| 方法	| 描述 |
|------|------|
| contextClick() | 鼠标右击 |
| clickAndHold(WebElement)	| 点击并控制（模拟悬停） |
| doubleClick(WebElement)	| 鼠标双击 |
| dragAndDrop(webElement1,webElement2)	| 鼠标拖动 |
| moveToElement(WebElement)	| 鼠标移动到某个元素上 |
| perform()	| 执行所有Actions中存储的行为 |
| click()	| 鼠标单击（左击） |

## 模拟键盘
在 Selenium 中有个 `Keys` 类，提供了几乎键盘上所有按键的方法，在使用的过程中，我们可以通过 `sendKeys()` 方法来模拟键盘的输入。见[9.2.模拟键盘](https://www.cnblogs.com/tester-ggf/p/12602211.html#1713804752)。

## References
- [selenium 使用教程详解-java版本](https://www.cnblogs.com/tester-ggf/p/12602211.html)
