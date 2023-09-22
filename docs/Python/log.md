---
date created: 2022-11-23 17:42:28 +08:00
date updated: 2023-09-13 14:21:21 +08:00
share: true
category: Python
---
# loguru
## 特性
- there is one and only one logger, pre-configured and outputs to stderr
- 

## 设置日志等级
```
from loguru import logger

config = {

}
logger.config(**config)
```

### 日志环境变量

在DataGrid中 `%env LOGURU_LEVEL=SUCCESS`
在Terminal中 `export LOGURU_LEVEL=SUCCESS`


## 日志等级

| TRACE    | 5   | logger.trace()    |
| -------- | --- | ----------------- |
| DEBUG    | 10  | logger.debug()    |
| INFO     | 20  | logger.info()     |
| SUCCESS  | 25  | logger.success()  |
| WARNING  | 30  | logger.warning()  |
| ERROR    | 40  | logger.error()    |
| CRITICAL | 50  | logger.critical() |
