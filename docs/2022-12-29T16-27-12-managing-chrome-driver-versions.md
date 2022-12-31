---
layout: default
date: 2022-12-29T16:27:12-05:00
title: Managing Chrome Driver Versions
tags: 
---

# Managing Chrome Driver Versions

When using `Chrome` + `Selenium` need to have compatible browser & driver versions.

- install web driver manager

```
    python3 -m pip install webdriver-manager
```

- import driver manager and configure webdriver with it

```
...
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
...

options = Options()
options.headless = False
driver = webdriver.Chrome(ChromeDriverManager().install(), chrome_options=options)
driver.maximize_window()
```

`NOTE`: `ChromeDriverManager().install()` will install the right web driver for whatever version of Chrome you have installed