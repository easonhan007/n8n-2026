---
title: n8n+playwright强强联合，更高级的网络爬虫方案
date: 2026-01-20T14:29:28+08:00
lastmod: 2026-01-20T14:29:28+08:00
author: 乙醇
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /img/n8n_playwright_b.jpg
# covercaption: a description of the cover image
# images:
#   - /img/n8n_playwright_b.jpg
categories:
  - n8n
  - playwright
tags:
  - n8n
  - playwright
  - 自动化
# nolastmod: true
# math: true
draft: false
---

n8n如何直接调用playwright进行高级的爬虫操作。

<!--more-->

## 视频地址

- [B站](https://www.bilibili.com/video/BV1P9FQzME19/)
<iframe  width="560" height="315"  src="//player.bilibili.com/player.html?isOutside=true&aid=116045872373159&bvid=BV1P9FQzME19&cid=35962685230&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>

- [YouTube](https://www.youtube.com/watch?v=17rrkWWIzI0)
<iframe width="560" height="315" src="https://www.youtube.com/embed/17rrkWWIzI0?si=PvIjsc6hZIrX1sPQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 正文

先来看一下问题，我们用http请求节点去访问电影[千与千寻的豆瓣首页](https://movie.douban.com/subject/1291561/)，我们只能得到一串js代码，而不是页面的html。

这是因为电影的浏览页在打开之前，会有js的检验，保证只有真正的浏览器才能访问这个页面，可能是一种反爬虫的手段。

这时候我们可以用playwright来帮忙解决问题。

思路是这样的，先在本机用playwright获取千与千寻的主页，因为playwright是真实的浏览器，所以是可以重定向到真正的电影详情页的。

之后就是写一个http接口，把playwright拿到的html返回出来。

最后在n8n里，我们用http节点去调用这个接口，这样就可以拿到完整的HTML了。

代码我已经用ai生成好了，放在这里，大家可以看一下。

```python
# api.py

from fastapi import FastAPI, HTTPException, Query
from playwright.async_api import async_playwright
from pydantic import BaseModel, HttpUrl, validator
import re
from typing import Optional

app = FastAPI(title="URL HTML Fetcher")

class URLRequest(BaseModel):
    url: str
    until: Optional[str] = None

class HTMLResponse(BaseModel):
    url: str
    data: str
    status: str = "success"

@app.get("/")
async def root():
    return {
        "message": "FastAPI Playwright HTML Fetcher",
        "endpoints": {
            "/fetch-html": "GET - 获取指定 URL 的 HTML 内容"
        }
    }

@app.get("/fetch-html", response_model=HTMLResponse)
async def fetch_html(
    url: str = Query(..., description="要访问的 URL 地址", min_length=1, max_length=2048),
    until: Optional[str] = Query(None, description="CSS 选择器，等待该元素出现后再获取 HTML")
):
    """
    访问指定 URL 并返回页面的 HTML 代码

    参数:
    - url: 要访问的完整 URL（必须包含 http:// 或 https://）
    - until: 可选的 CSS 选择器，等待该元素出现后再获取 HTML

    返回:
    - url: 访问的 URL
    - data: 页面的 HTML 代码
    - status: 请求状态
    """

    # 验证 URL
    try:
        url_request = URLRequest(url=url)
    except ValueError as e:
        raise HTTPException(status_code=400, detail=str(e))

    # 使用 Playwright 访问页面
    try:
        async with async_playwright() as p:
            # 启动浏览器（无头模式）
            browser = await p.chromium.launch(headless=True)

            # 创建新页面
            page = await browser.new_page()

            # 设置超时时间（30秒）
            page.set_default_timeout(30000)

            # 访问 URL
            response = await page.goto(url_request.url, wait_until="domcontentloaded")

            # 检查响应状态
            if response.status >= 400:
                await browser.close()
                raise HTTPException(
                    status_code=400,
                    detail=f"页面返回错误状态码: {response.status}"
                )

            # 等待页面加载完成
            await page.wait_for_load_state("networkidle", timeout=50000)

            # 如果指定了 CSS 选择器，等待该元素出现
            if until:
                try:
                    await page.wait_for_selector(until, timeout=15000)
                except Exception as e:
                    await browser.close()
                    raise HTTPException(
                        status_code=400,
                        detail=f"等待选择器 '{until}' 超时或未找到: {str(e)}"
                    )

            # 获取 HTML 内容
            html_content = await page.content()
            await page.screenshot(path="s.png")

            # 关闭浏览器
            await browser.close()

            return HTMLResponse(
                url=url_request.url,
                data=html_content
            )

    except Exception as e:
        raise HTTPException(
            status_code=500,
            detail=f"访问页面时发生错误: {str(e)}"
        )

```

我们再看一下这个http接口，一共两个参数。

第一个参数是url，也就是我们要访问的页面。

第二个参数是until，其实是一个css选择器，表示只有在页面上出现了这个css选择器代表元素的时候才返回html。这是确保页面能完整加载的保底手段。

下面运行一下我们的接口，使用命令

```

uvicorn api:app --host 0.0.0.0 --reload

```

之后就是在n8n里调用`/fetch-html`接口。

url里面我们填入`https://movie.douban.com/subject/1291561/`，until里填入`.article`，确保这个元素在页面上显示出来之后我们再返回相应的html。

运行之后应该可以得到结果，符合预期。

然后用extract html content来抓取相应的信息就好了。

这里我们抓取3个信息，分别是

- 电影的名称。css选择器是`h1 > span`
- 电影的评分。`.rating_num`
- 电影的评论。`.short`，因为是有多条评论，所以要把`Return Array`这个开关打开

运行一下，没有问题。

到这里这个n8n的流程就结束了。

简单说，只要一个工具能对外提供 HTTP 服务，n8n 基本都能跟它接上。不管是 API 还是 Webhook，只要能联网、能调接口，n8n 就能把它拉进你的自动化工作流里一起干活

## 流程代码

直接复制粘贴到n8n就可以用了。

```json
{
  "name": "n8n+playwright",
  "nodes": [
    {
      "parameters": {
        "url": "=http://host.orb.internal:8000/fetch-html?url=https://movie.douban.com/subject/1291561/",
        "sendQuery": true,
        "queryParameters": {
          "parameters": [
            {
              "name": "url",
              "value": "https://movie.douban.com/subject/1291561/"
            },
            {
              "name": "until",
              "value": ".article"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.3,
      "position": [224, 0],
      "id": "a05d37c0-3e87-43be-b1a0-15c5580697d4",
      "name": "HTTP Request1"
    },
    {
      "parameters": {
        "operation": "extractHtmlContent",
        "extractionValues": {
          "values": [
            {
              "key": "name",
              "cssSelector": "h1 > span"
            },
            {
              "key": "score",
              "cssSelector": ".rating_num"
            },
            {
              "key": "comments",
              "cssSelector": ".short",
              "returnArray": true
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.html",
      "typeVersion": 1.2,
      "position": [432, 0],
      "id": "dd402594-01b9-452b-9b8d-712898b5b406",
      "name": "HTML"
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [0, 0],
      "id": "770dd1fa-952a-4cab-9d45-aa69c8a63c30",
      "name": "When clicking ‘Execute workflow’"
    }
  ],
  "pinData": {},
  "connections": {
    "HTTP Request1": {
      "main": [
        [
          {
            "node": "HTML",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "When clicking ‘Execute workflow’": {
      "main": [
        [
          {
            "node": "HTTP Request1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1",
    "availableInMCP": false
  },
  "versionId": "39325569-d548-4b1e-8de2-b9705cfe12d4",
  "meta": {
    "instanceId": "05918ff9c9ba7e7e6f5df0c8be162624879238b4c06e5881f0af695749866d92"
  },
  "id": "UW2MXJGdu3BzdnJUBJ3mE",
  "tags": []
}
```
