---
title: 一个视频掌握 80% n8n 内容
date: 2026-01-15T17:51:11+08:00
lastmod: 2026-01-15T17:51:11+08:00
author: 乙醇
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /img/cover1.png
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - video
tags:
  - n8n
  - 视频
  - 初学者
# nolastmod: true
# math: true
---

视频教程中可能用到的资源。

<!--more-->

## 视频链接

- [YouTube](https://youtu.be/k3xdSqwlFic?si=Exd2JERTfb2ffoJc)
- [B 站](https://www.bilibili.com/video/BV1oDrZBZEb6/)

## 订阅源地址

- https://www.ifanr.com/feed(数码科技新闻)
- https://www.qbitai.com/feed（ai人工智能新闻）
- http://feed.smzdm.com/（每天更新网友爆料、编辑精选的优惠信息，包括各大B2C网站的特价商品和促销活动，是大家每天的网购指南。）
- https://www.gcores.com/rss(游戏相关的新闻)

## 合并 items 的提示词

```
已知n8n的code节点里，input的用法如下。
Method	Description	Available in Code node?
$binary	Shorthand for $input.item.binary. Incoming binary data from a node	❌
$input.item	The input item of the current node that's being processed. Refer to Item linking for more information on paired items and item linking.	✅
$input.all()	All input items in current node.	✅
$input.first()	First input item in current node.	✅
$input.last()	Last input item in current node.	✅
$input.params	Object containing the query settings of the previous node. This includes data such as the operation it ran, result limits, and so on.	✅
$json	Shorthand for $input.item.json. Incoming JSON data from a node. Refer to Data structure for information on item structure.	✅ (when running once for each item)
$input.context.noItemsLeft	Boolean. Only available when working with the Loop Over Items node. Provides information about what's happening in the node. Use this to determine whether the node is still processing items.
现在我想要把input里的title和content字段拿出来，把titile变成html的h1，content保持原来的格式不变，最后把title和content组合成1个新的html并返回。

```

## 合并 item 的代码

```javascript
/ 获取所有输入项
const items = $input.all();

// 映射每个 item，生成新的 html 字段
const newItems = items.map(item => {
  const title = item.json.title || '';
  const content = item.json.content || '';

  // 构造 HTML：title 转为 <h1>，content 保持原样
  const html = `<h1>${title}</h1>\n${content}`;

  // 返回新 item，保留原有数据并添加 html 字段
  return  html
});

// 返回结果
return {
  json: {
    html: newItems.join("<br />")
  }
}

```

## Open weather 的注册地址

https://home.openweathermap.org/users/sign_up

## 第 3 个流程说明的翻译

```
快来试试吧！

启动你的第一个 AI 智能体——一个能调用工具获取实时信息、发送邮件并自动执行任务的聊天机器人。

开始使用：
1. 连接 Gemini（参见下方红色便签说明）
2. 点击 🗨 打开聊天 按钮，尝试提问以下内容：
   * “巴黎现在的天气怎么样？”
   * “给我最新的科技新闻。”
   * “给我一些 n8n AI 智能体的创意点子。”

有问题或想提供反馈？
如需反馈、指导、定制工作流，或有任何疑问，请使用我的统一 AI 驱动联系表单。

➡️ 立即联系我
自动化愉快！—— Lucas Peyrin
```

## AI agent 的 system message 翻译

```
你是 n8n 演示 AI 智能体，一个友好且乐于助人的助手，旨在展示 AI 智能体在 n8n 自动化平台中的强大能力。你的性格积极鼓励、略带教育性，并对自动化充满热情。你的主要功能是通过使用可用工具来回答用户问题并满足其请求，从而展示自身能力。你的交流风格自然、对话式。

你的核心目标是作为基于 n8n 构建的 AI 智能体的实时演示。你将与用户互动，通过智能调用可用工具来回答问题，并解释 AI 智能体背后的概念，帮助用户理解其潜力。

### 我的工作原理
我是一个运行在简单 n8n 工作流中的 AI 模型。该工作流为我提供了两个关键要素：
1. **一组工具**：这些是我可以调用以获取信息或执行操作的功能。
2. **简易记忆**：我可以记住当前对话的近期历史，以便理解上下文。

### 我的用途
我的主要用途是作为一个展示范例。我演示了如何为各种功能（即我的工具）赋予聊天界面，而无需复杂的用户界面。这是一种通过简单对话让任何人都能轻松使用强大自动化功能的绝佳方式。

### 我的工具使用说明
如果用户的请求匹配某个工具的能力，你必须选择并调用该工具。你无法自行执行这些操作；必须通过调用工具来完成。

### 关于 n8n 中的 AI 智能体
- **可靠性**：虽然我可以一次有效使用一个工具，但更高级的智能体能够执行多步骤任务。然而，对于**复杂且关键的任务流程，通常更可靠的做法是在 n8n 中构建结构化的、逐步执行的工作流，而不是完全依赖智能体的推理能力**。智能体非常适合面向用户的交互，但结构化工作流在后端可靠性方面更具优势。
- **最佳实践**：建议保持智能体的工具集聚焦，通常控制在 10–15 个工具以内，以确保可靠性和避免混淆。

### 当前日期与时间
[DateTime: 2026-01-07T03:14:36.635-05:00]

- 以友好、对话式且乐于助人的语气进行回复。
- 当用户的请求需要调用工具时，请先选择合适的工具，然后以清晰易懂的方式向用户呈现工具执行的结果。
- 主动引导。如果用户不确定该做什么，可根据你可用的工具主动提供一些示例（例如，介绍你的工具以及你对自己能力的了解）。

```

## 第 1 和 第 2 个流程

```json
{
  "name": "rss to email",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "triggerAtHour": 7
            }
          ]
        }
      },
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.3,
      "position": [0, 0],
      "id": "14d3a373-e191-4f85-b59e-8e7e242eea4e",
      "name": "Schedule Trigger"
    },
    {
      "parameters": {
        "url": "https://www.ifanr.com/feed",
        "options": {}
      },
      "type": "n8n-nodes-base.rssFeedRead",
      "typeVersion": 1.2,
      "position": [224, 0],
      "id": "67f1275e-dd20-4d36-9fc5-d7acb25d2f8a",
      "name": "RSS Read"
    },
    {
      "parameters": {
        "maxItems": 3
      },
      "type": "n8n-nodes-base.limit",
      "typeVersion": 1,
      "position": [448, 0],
      "id": "c16e63f4-5331-4678-9dbc-f04eee028ea7",
      "name": "Limit"
    },
    {
      "parameters": {
        "fromEmail": "nbkhic@qq.com",
        "toEmail": "nbkhic@gmail.com",
        "subject": "每日新闻",
        "html": "={{ $json.html }}",
        "options": {
          "appendAttribution": false
        }
      },
      "type": "n8n-nodes-base.emailSend",
      "typeVersion": 2.1,
      "position": [1120, 96],
      "id": "8656c006-8cba-4634-90ec-8ef275347eec",
      "name": "Send email",
      "webhookId": "05bc4b98-8258-4fc6-a228-5dde239cff9b",
      "credentials": {
        "smtp": {
          "id": "jPkzRJNjSb1LRqn8",
          "name": "SMTP account"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "// 获取所有输入项\nconst items = $input.all();\n\n// 映射每个 item，生成新的 html 字段\nconst newItems = items.map(item => {\n  const title = item.json.title || '';\n  const content = item.json.content || '';\n\n  // 构造 HTML：title 转为 <h1>，content 保持原样\n  const html = `<h1>${title}</h1>\\n${content}`;\n\n  // 返回新 item，保留原有数据并添加 html 字段\n  return  html\n});\n\n// 返回结果\nreturn {\n  json: {\n    html: newItems.join(\"<br />\")\n  }\n}\n"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [896, 96],
      "id": "1a278d89-7aba-4a0f-bf01-34d0022885a5",
      "name": "Code in JavaScript"
    },
    {
      "parameters": {
        "url": "https://www.qbitai.com/feed",
        "options": {}
      },
      "type": "n8n-nodes-base.rssFeedRead",
      "typeVersion": 1.2,
      "position": [224, 192],
      "id": "a028a378-c744-479f-8e6e-2c6f5f24eaca",
      "name": "RSS Read1"
    },
    {
      "parameters": {
        "maxItems": 3
      },
      "type": "n8n-nodes-base.limit",
      "typeVersion": 1,
      "position": [448, 192],
      "id": "d1477579-9c30-4ab8-a96d-0f18acfec1c5",
      "name": "Limit1"
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.merge",
      "typeVersion": 3.2,
      "position": [672, 96],
      "id": "70ed1b11-98d7-4700-a0aa-41e6f4b580ab",
      "name": "Merge"
    }
  ],
  "pinData": {},
  "connections": {
    "Schedule Trigger": {
      "main": [
        [
          {
            "node": "RSS Read",
            "type": "main",
            "index": 0
          },
          {
            "node": "RSS Read1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "RSS Read": {
      "main": [
        [
          {
            "node": "Limit",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Limit": {
      "main": [
        [
          {
            "node": "Merge",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Code in JavaScript": {
      "main": [
        [
          {
            "node": "Send email",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "RSS Read1": {
      "main": [
        [
          {
            "node": "Limit1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Limit1": {
      "main": [
        [
          {
            "node": "Merge",
            "type": "main",
            "index": 1
          }
        ]
      ]
    },
    "Merge": {
      "main": [
        [
          {
            "node": "Code in JavaScript",
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
  "versionId": "61ea00cc-a985-4e6d-84e1-e3eb75e4e1f4",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "05918ff9c9ba7e7e6f5df0c8be162624879238b4c06e5881f0af695749866d92"
  },
  "id": "HnwpXLSuurTI9MQapaHHY",
  "tags": []
}
```

## 第 3 个流程

````json
{
  "name": "ai agent",
  "nodes": [
    {
      "parameters": {
        "content": "## [Video Tutorial](https://youtu.be/laHIzhsz12E)\n@[youtube](laHIzhsz12E)",
        "height": 352,
        "width": 512,
        "color": 7
      },
      "id": "877e1f08-86cb-4e91-be39-55a50bda70e1",
      "name": "Sticky Note2",
      "type": "n8n-nodes-base.stickyNote",
      "position": [1200, 0],
      "typeVersion": 1
    },
    {
      "parameters": {
        "content": "## Try It Out!\n\n**Launch your first AI agent—a chatbot that uses tools to fetch live info, send emails, and automate tasks.**\n\n### To get started:\n1.  **Connect Gemini** (see red sticky note below)\n2.  Click the **🗨 Open chat** button and try asking:\n    *   “What’s the weather in Paris?”\n    *   “Get me the latest tech news.”\n    *   “Give me ideas for n8n AI agents.”\n\n### Questions or Feedback?\nFor feedback, coaching, buit-for-you workflows or any questions, use my unified AI-powered contact form.\n\n➡️ **[Get in Touch Here](https://api.ia2s.app/form/templates/academy)**\n\n*Happy Automating! —Lucas Peyrin*",
        "height": 460,
        "width": 392
      },
      "id": "6bb2361e-976a-456f-bcae-ee7d27368401",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Introduction Note",
      "type": "n8n-nodes-base.stickyNote",
      "creator": "Lucas Peyrin",
      "position": [-144, 0],
      "typeVersion": 1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "content": "💡 Later, activate this workflow and share the public chat URL to let others talk to your AI Agent!",
        "height": 252,
        "width": 300,
        "color": 7
      },
      "id": "9bd15859-077c-420f-a26c-8e144ac88bdb",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Sticky Note12",
      "type": "n8n-nodes-base.stickyNote",
      "creator": "Lucas Peyrin",
      "position": [336, 96],
      "typeVersion": 1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "content": "Your AI agent can:\n1. **Receive** messages from the chat\n2. **Select** the right tools (e.g., weather, news, email)\n3. **Respond** with live, helpful answers\n\n\n**Open the AI agent node** and edit the **System Message** to adjust your agent’s thinking, behavior, and replies.\n\n\n\n\n\n\n\n\n\n\n",
        "height": 348,
        "width": 396,
        "color": 7
      },
      "id": "24d46684-27d3-4268-929c-f793624655a1",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Sticky Note13",
      "type": "n8n-nodes-base.stickyNote",
      "creator": "Lucas Peyrin",
      "position": [720, 0],
      "typeVersion": 1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "content": "\n\n\n\n\n\n\n\n\n\n\n\n\n\nThis node helps your agent remember the last few messages to stay on topic.",
        "height": 260,
        "width": 308,
        "color": 7
      },
      "id": "0c8a4191-5597-4f5d-808a-d031f9b2cd7f",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Sticky Note15",
      "type": "n8n-nodes-base.stickyNote",
      "creator": "Lucas Peyrin",
      "position": [720, 432],
      "typeVersion": 1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "content": "\n\n\n\n\n\n\n\n\n\n\n\n\nThese tools let your agent access real-world data or take actions. Add more to expand its abilities!\n\nClick the ➕ under the agent’s Tool input to add:\n- Google Calendar (Get Upcoming Events)\n- Gmail (Send an Email) (Gmail)",
        "height": 324,
        "width": 372,
        "color": 7
      },
      "id": "432ef663-96d1-4a80-b2ed-c1f2088a79f4",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Sticky Note16",
      "type": "n8n-nodes-base.stickyNote",
      "creator": "Lucas Peyrin",
      "position": [1120, 432],
      "typeVersion": 1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "toolDescription": "Gets the latest blog posts about any rss feed.",
        "url": "={{ /*n8n-auto-generated-fromAI-override*/ $fromAI('URL', `Use one of:\n- https://feeds.bbci.co.uk/news/world/rss.xml (BBC World – global headlines)\n- https://www.aljazeera.com/xml/rss/all.xml (Al Jazeera English – in‑depth global coverage)\n- http://rss.cnn.com/rss/edition_world.rss (CNN World – breaking news worldwide)\n- https://techcrunch.com/feed/ (TechCrunch – global tech & startup news)\n- http://news.ycombinator.com/rss (Hacker News – tech community headlines)\n- https://n8n.io/blog/rss (n8n Blog – updates & tutorials)\n- https://www.bonappetit.com/feed/recipes-rss-feed/rss (Bon Appétit – recent recipes list)\n- https://www.endsreport.com/rss/news-and-analysis (ENDS Report – environmental law & policy news)\n- https://medlineplus.gov/groupfeeds/new.xml (MedlinePlus – health topics & wellness updates)\n- https://www.ifanr.com/feed(数码科技新闻)\n- https://www.qbitai.com/feed（ai人工智能新闻）\n- http://feed.smzdm.com/（每天更新网友爆料、编辑精选的优惠信息，包括各大B2C网站的特价商品和促销活动，是大家每天的网购指南。）\n- https://www.gcores.com/rss(游戏相关的新闻)`, 'string') }}",
        "options": {}
      },
      "id": "c9271482-00a7-4eb4-8e16-7436960fefa2",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Get News",
      "type": "n8n-nodes-base.rssFeedReadTool",
      "creator": "Lucas Peyrin",
      "position": [1264, 480],
      "typeVersion": 1.2,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "public": true,
        "initialMessages": "Hi there! 👋",
        "options": {
          "inputPlaceholder": "Type your message here...",
          "showWelcomeScreen": false,
          "subtitle": "This is for demo purposes. Try me out !",
          "title": "Your first AI Agent 🚀",
          "responseMode": "lastNode"
        }
      },
      "id": "3246df4b-a50c-4a93-8aff-59a33f3668ed",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Example Chat",
      "type": "@n8n/n8n-nodes-langchain.chatTrigger",
      "creator": "Lucas Peyrin",
      "position": [432, 192],
      "webhookId": "e5616171-e3b5-4c39-81d4-67409f9fa60a",
      "typeVersion": 1.1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "contextWindowLength": 30
      },
      "id": "edaa0de9-9fe3-4255-b500-c5881b996756",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Conversation Memory",
      "type": "@n8n/n8n-nodes-langchain.memoryBufferWindow",
      "creator": "Lucas Peyrin",
      "position": [832, 480],
      "typeVersion": 1.3,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "content": "\n\n\n\n\n\n\n\n\n\n\n\n1. [In Google AI Studio](https://aistudio.google.com/app/apikey) click **“Create API key in new project”** and copy it.\n\n2. Open the ```Connect Gemini``` node:\n   * **Select Credential → Create New**\n   * Paste into **API Key** and **Save**\n",
        "height": 316,
        "width": 294,
        "color": 3
      },
      "id": "660affe1-34d1-4d34-a72e-7c005450f653",
      "cid": "Ikx1Y2FzIFBleXJpbiI",
      "name": "Sticky Note",
      "type": "n8n-nodes-base.stickyNote",
      "creator": "Lucas Peyrin",
      "position": [336, 432],
      "typeVersion": 1,
      "notes": "© 2025 Lucas Peyrin"
    },
    {
      "parameters": {
        "model": {
          "__rl": true,
          "mode": "list",
          "value": "gpt-4.1-mini"
        },
        "builtInTools": {},
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOpenAi",
      "typeVersion": 1.3,
      "position": [432, 480],
      "id": "cd0bb302-01f2-4698-8502-79c95af8b848",
      "name": "OpenAI Chat Model",
      "credentials": {
        "openAiApi": {
          "id": "8OxplPol0qLPCNMQ",
          "name": "OpenAi account"
        }
      }
    },
    {
      "parameters": {
        "options": {
          "systemMessage": "=<role>\nYou are the n8n Demo AI Agent, a friendly and helpful assistant designed to showcase the power of AI agents within the n8n automation platform. Your personality is encouraging, slightly educational, and enthusiastic about automation. Your primary function is to demonstrate your capabilities by using your available tools to answer user questions and fulfill their requests. You are conversational.\n</role>\n\n<instructions>\n<goal>\nYour primary goal is to act as a live demonstration of an AI Agent built with n8n. You will interact with users, answer their questions by intelligently using your available tools, and explain the concepts behind AI agents to help them understand their potential.\n</goal>\n\n<context>\n### How I Work\nI am an AI model operating within a simple n8n workflow. This workflow gives me two key things:\n1.  **A set of tools:** These are functions I can call to get information or perform actions.\n2.  **Simple Memory:** I can remember the immediate past of our current conversation to understand context.\n\n### My Purpose\nMy main purpose is to be a showcase. I demonstrate how you can give a chat interface to various functions (my tools) without needing complex UIs. This is a great way to make powerful automations accessible to anyone through simple conversation.\n\n### My Tools Instructions\nYou must choose one of your available tools if the user's request matches its capability. You cannot perform these actions yourself; you must call the tool.\n\n### About AI Agents in n8n\n- **Reliability:** While I can use one tool at a time effectively, more advanced agents can perform multi-step tasks. However, for `complex, mission-critical processes, it's often more reliable to build structured, step-by-step workflows in n8n rather than relying solely on an agent's reasoning. Agents are fantastic for user-facing interactions, but structured workflows are king for backend reliability.\n- **Best Practices:** A good practice is to keep an agent's toolset focused, typically under 10-15 tools, to ensure reliability and prevent confusion.\n\n### Current Date & Time\n{{ $now }}\n</context>\n\n<output_format>\n- Respond in a friendly, conversational, and helpful tone.\n- When a user's request requires a tool, first select the appropriate tool. Then, present the result of the tool's execution to the user in a clear and understandable way.\n- Be proactive. If the user is unsure what to do, suggest some examples of what they can ask you based on your available tools (e.g., Talk about your tools and what you know about yourself).\n</output_format>\n</instructions>"
        }
      },
      "type": "@n8n/n8n-nodes-langchain.agent",
      "typeVersion": 3.1,
      "position": [800, 192],
      "id": "dd9f7030-d6bd-4a69-aa8b-885a1aefa4e7",
      "name": "AI Agent"
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.openWeatherMapTool",
      "typeVersion": 1,
      "position": [1152, 480],
      "id": "aef03c21-348a-49b6-a070-5b7dd2a5a861",
      "name": "OpenWeatherMap",
      "credentials": {
        "openWeatherMapApi": {
          "id": "qmAdU6JBZaqqlj1w",
          "name": "OpenWeatherMap account"
        }
      }
    }
  ],
  "pinData": {},
  "connections": {
    "Get News": {
      "ai_tool": [
        [
          {
            "node": "AI Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "Example Chat": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Conversation Memory": {
      "ai_memory": [
        [
          {
            "node": "AI Agent",
            "type": "ai_memory",
            "index": 0
          }
        ]
      ]
    },
    "OpenAI Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "OpenWeatherMap": {
      "ai_tool": [
        [
          {
            "node": "AI Agent",
            "type": "ai_tool",
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
  "versionId": "c5397e17-bddd-4aa0-9697-0a08a90c0eac",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "05918ff9c9ba7e7e6f5df0c8be162624879238b4c06e5881f0af695749866d92"
  },
  "id": "m8Igt_WoaYsYvYY28n9X7",
  "tags": []
}
````
