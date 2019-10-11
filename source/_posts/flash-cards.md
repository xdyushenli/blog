---
title: 抽记卡网站搭建
date: 2019-09-22 11:51:37
tags:
---
# uwsgi的app变量名必须为application
否则会出现如下错误
```python
from myproject import app as application
 
if __name__ == "__main__":
    application.run()
```
```
unable to load app 0 (mountpoint='') (callable not found or import error)

*** no app loaded. going in full dynamic mode ***

 

--- no python application found, check your startup logs for errors ---
```