[Selenium](http://www.selenium.org.cn/) 是一个浏览器自动化测试框架, 可以给爬虫提供js的渲染环境


#### 配置chrome启动参数
https://github.com/cobrateam/splinter/issues/540

```python
from selenium import webdriver
options = webdriver.ChromeOptions()
options.add_argument('--user-data-dir=/tmp/drive')
```

```
google-chrome  -h
  --user-data-dir=DIR
    Specifies the directory that user data (your "profile") is kept in.  Defaults to  $HOME/.config/google-chrome.  
    Separate instances  of  Google  Chrome  must use separate user data directories; repeated invocations of
    google-chrome will reuse an existing process for a given user data directory.
```
