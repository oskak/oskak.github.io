---
title: Selenium 第一个程序实例
tags: [Selenium]
---

## Selenium 历史
Selenium 是一系列基于 Web 的自动化工具，提供一套测试函数，用于支持 Web 自动化测试。函数非常灵活，能够完成界面元素定位、窗口跳转、结果比较。

早期的 Selenium1.x 使用的是 Javascript 注入技术与浏览器打交道，需要 Selenium RC 启动一个 Server，将操作 Web 元素的 API 调用转化为一段段 Javascript，在 Selenium 内核启动浏览器之后注入这段 Javascript。开发过 Web 应用的人都知道，Javascript 可以获取并调用页面的任何元素，自如的进行操作。由此才实现了 Selenium 的目的：自动化Web操作。这种 Javascript 注入技术的缺点是速度不理想，而且稳定性大大依赖于 Selenium 内核对 API 翻译成的 Javascript 质量高低。

相比于 selenium1.x，selenium2.x 版本整合了 webdriver 以及原版 selenium。当 Selenium2.x 提出了 WebDriver 的概念之后，它提供了完全另外的一种方式与浏览器交互。那就是利用浏览器原生的 API，封装成一套更加面向对象的 Selenium WebDriver API，直接操作浏览器页面里的元素，甚至操作浏览器本身（截屏，窗口大小，启动，关闭，安装插件，配置证书等）。由于使用的是浏览器原生的 API，速度大大提高，而且调用的稳定性交给了浏览器厂商本身，显然更加科学。然而带来的一些副作用就是，不同的浏览器厂商，对 Web 元素的操作和呈现多少会有一些差异，这就直接导致了 Selenium WebDriver 要分浏览器厂商不同，而提供不同的实现。

对于 Selenium3 来说最大的变动可能就是更加专注于手机和 web 的测试，尤其是手机的支持。Selenium 3 将删除最初的 Selenium 核心实现，官方不再推荐使用 RC API。

## WebDriver 原理
在 WebDriver 中有三个角色：
- 工程师写的自动化测试代码：自动化测试代码发送请求给浏览器的驱动（比如火狐驱动、谷歌驱动）
- 浏览器的驱动：它来解析这些自动化测试的代码，解析后把它们发送给浏览器
- 浏览器：执行浏览器驱动发来的指令，并最终完成工程师想要的操作。

## 第一个 Selenium 程序
[Get Firefox geckodriver](https://github.com/mozilla/geckodriver/releases/tag/v0.30.0)

`pom.xml`:
```
    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.seleniumhq.selenium/selenium-java -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>3.141.59</version>
        </dependency>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-api</artifactId>
            <version>3.141.59</version>
        </dependency>
    </dependencies>
```

`selenium_learn/src/main/java/app.java`:
```
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;

import java.util.concurrent.TimeUnit;


public class app {
    public static void main(String[] args) {
        System.setProperty("webdriver.gecko.driver", "geckodriver");
        WebDriver driver = new FirefoxDriver();

        driver.get("http://www.google.com/");
        driver.manage().timeouts().implicitlyWait(3, TimeUnit.SECONDS);
        // Click on the search text box and send value
        driver.findElement(By.xpath("/html/body/div[1]/div[3]/form/div[1]/div[1]/div[1]/div/div[2]/input")).sendKeys("javatpoint tutorials");

        // Click on the search button
        driver.findElement(By.xpath("/html/body/div[1]/div[3]/form/div[1]/div[1]/div[3]/center/input[1]")).click();
        
        driver.quit();
    }
}
```

## References
- [Selenium Webdriver原理终于搞清楚了！](https://blog.csdn.net/lovedingd/article/details/110929187)
- [selenium 使用教程详解-java版本](https://www.cnblogs.com/tester-ggf/p/12602211.html)
