---
title: 使用n8n进行接口测试
date: 2026-01-16T13:26:11+08:00
lastmod: 2026-01-16T13:26:11+08:00
author: 乙醇
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /img/n8n_api_test.jpg
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - n8n
  - 测试
  - 接口测试
tags:
  - 接口测试
  - 自动化
# nolastmod: true
# math: true
draft: false
---

如何使用 n8n 进行接口测试的资源。

<!--more-->

## 视频地址

- [Youtube](https://youtu.be/vugZDC38pA0?si=mAKUaxi7Ux2Kq_O_)
- [B站](https://www.bilibili.com/video/BV1barDBZEg7/)

## 测试接口的文档

[Dummy Json](https://dummyjson.com/docs)

## n8n code 节点模拟断言代码

```javascript
// 单接口
const resp = $input.first().json;

if (resp.status !== "ok") {
  throw new Error("Response status error");
}
if (resp.method !== "GET") {
  throw new Error("Response method error");
}

return {
  json: { message: "ok" },
};
```

```javascript
// 接口编排
const resp = $input.first().json;

if (resp.firstName !== "Emily") {
  throw new Error("First name is incorrect!");
}

return {
  json: { message: "ok" },
};
```

## 生成 python unittest 及 runner 代码的提示词

```
用python的unit test写下面接口的测试用例。
// Could be GET or POST/PUT/PATCH/DELETE
fetch('https://dummyjson.com/test')
  .then(res => res.json()) .then(console.log);

/* { status: 'ok', method: 'GET' } */

再写个http节点去运行测试用例，最好是返回的内容里能看到用例执行的情况。
```

## 目录结构

```
project/
├── test_api.py         # unittest 用例
├── runner.py      # HTTP 测试运行器

```

## 测试用例代码

```python
# test_api.py
import unittest
import requests

class TestDummyJsonAPI(unittest.TestCase):

    def test_get_test_endpoint(self):
        url = "https://dummyjson.com/test"
        response = requests.get(url)

        # 1. HTTP 状态码
        self.assertEqual(response.status_code, 200)

        data = response.json()

        # 2. 返回字段校验
        self.assertIn("status", data)
        self.assertIn("method", data)

        # 3. 值校验
        self.assertEqual(data["status"], "ok")
        self.assertEqual(data["method"], "GET")


if __name__ == "__main__":
    unittest.main()

```

## Fast api runner 代码

```python
# runner.py

from fastapi import FastAPI, Response
import unittest
import io
import sys

app = FastAPI()


@app.get("/run-tests")
def run_tests():
    # 捕获 unittest 输出
    stream = io.StringIO()
    runner = unittest.TextTestRunner(
        stream=stream,
        verbosity=2
    )

    loader = unittest.TestLoader()
    suite = loader.discover(".")

    result = runner.run(suite)

    output = stream.getvalue()

    # 组装返回内容
    response_body = {
        "testsRun": result.testsRun,
        "failures": len(result.failures),
        "errors": len(result.errors),
        "wasSuccessful": result.wasSuccessful(),
        "output": output
    }

    return response_body
```

## Fast api 安装依赖以及运行的命令

```
pip install fastapi uvicorn requests
uvicorn runner:app --reload --host 0.0.0.0
```

## 访问宿主机 ip 的各种方式

- Mac obrstack: host.orb.internal:8000/run-tests
- Linux: host.docker.internal:8000/http://172.17.0.1:8000
- Win/Mac docker desktop: host.docker.internal:8000
