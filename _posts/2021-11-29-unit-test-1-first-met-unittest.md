---
title: (单元测试1) 初识unittest
tags: [Test]
---

## 单元测试是什么
单元测试是用来对一个模块、一个函数或者一个类进行正确性检验的工作。你的代码可能在语法、词法和运行过程中没有问题了，但是并不能代表它就完全符合你的设计预期，很有可能你希望得到A，它给你的结果却是B。这就需要我们对程序进行单元测试。单元测试测的不是语法问题，而是业务逻辑是否正确的问题。单元测试是软件开发过程中非常重要的一个环节。

单元测试具有以下特点：
- 单元测试可以有效地测试某个程序模块的行为
- 单元测试的测试用例要覆盖常用的输入组合、边界条件和异常
- 单元测试本身代码要简单，如果测试代码太复杂，那么测试代码本身都可能有bug

unittest 一共包含4个概念：
- Test Fixture，就是 `Setup()` 和 `TearDown()`
- Test Case，一个 Test Case 就是一个测试用例，他们都是 `unittest.TestCase` 类的子类的方法
- Test Suite 是一个测试用例集合，基本上用不到。用 `unittest.main()` 或者其它发现机制来运行所有的测试用例。
- Test runner，这是单元测试结果的呈现接口，可以定制呈现方式，比如GUI界面

## 第一个 unittest 程序示例
```
import unittest
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

class etherscan_register_username_check(unittest.TestCase):

    def setUp(self):
        self.driver = webdriver.Firefox('.')

    def test_abcd(self):
        """
        input: abcd
        """
        driver = self.driver
        driver.get("https://etherscan.io/register")

        elem = driver.find_element_by_name("ctl00$ContentPlaceHolder1$txtUserName")
        elem.send_keys("abcd")
        elem.send_keys(Keys.RETURN)

        error_message = driver.find_element_by_xpath('//*[@id="ContentPlaceHolder1_txtUserName-error"]').text
        self.assertEqual(error_message,"Username is invalid.")

    def test_abcde(self):
        """
        input: abcde
        """
        driver = self.driver
        driver.get("https://etherscan.io/register")

        elem = driver.find_element_by_name("ctl00$ContentPlaceHolder1$txtUserName")
        elem.send_keys("abcde")
        elem.send_keys(Keys.RETURN)

        error_message = driver.find_element_by_xpath('//*[@id="ContentPlaceHolder1_txtUserName-error"]').text
        self.assertEqual(error_message,"")

    ## other test cases, e.g. abcde#

    def tearDown(self):
        self.driver.close()

if __name__ == "__main__":
    unittest.main()
```

## Debug
(1) Description:
```
    raise WebDriverException(
selenium.common.exceptions.WebDriverException: Message: 'geckodriver' executable needs to be in PATH.
```

Solution: 
```
export PATH=$PATH:/path/to/directory/of/executable
```

See [here](https://stackoverflow.com/questions/40208051/selenium-using-python-geckodriver-executable-needs-to-be-in-path) for more details.

## References
- [调试和测试](https://www.liujiangblog.com/course/python/50)
- [Python-Unittest](http://blog.wuwenxiang.net/Python-Unittest)
