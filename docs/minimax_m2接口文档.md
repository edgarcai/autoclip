# 文本生成指南

> 支持通过 Anthropic 和 OpenAI 兼容接口进行文本生成调用。

## Anthropic 兼容接口 (推荐)

### 基础对话

<CodeGroup>
  ```python Python theme={null}
  import anthropic

  client = anthropic.Anthropic(
      base_url="https://api.minimaxi.com/anthropic",
      api_key=""  # 替换为你的 MiniMax API Key
  )

  message = client.messages.create(
      model="MiniMax-M2",
      max_tokens=1024,
      messages=[
          {
              "role": "user",
              "content": "请用简单的语言解释什么是机器学习"
          }
      ]
  )

  print(message.content[0].text)
  ```

  ```javascript Node.js theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic(
      base_url="https://api.minimaxi.com/anthropic",
      api_key=""  # 替换为你的 MiniMax API Key
  );

  const message = await client.messages.create({
    model: "MiniMax-M2",
    max_tokens: 1024,
    messages: [
      {
        role: "user",
        content: "请用简单的语言解释什么是机器学习",
      },
    ],
  });

  console.log(message.content[0].text);
  ```
</CodeGroup>

### 流式响应

<CodeGroup>
  ```python Python theme={null}
  import anthropic

  client = anthropic.Anthropic(
      base_url="https://api.minimaxi.com/anthropic",
      api_key=""  # 替换为你的 MiniMax API Key
  )

  with client.messages.stream(
      model="MiniMax-M2",
      max_tokens=1024,
      messages=[
          {
              "role": "user",
              "content": "写一首关于春天的诗"
          }
      ]
  ) as stream:
      for text in stream.text_stream:
          print(text, end="", flush=True)
  ```

  ```javascript Node.js theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic(
      base_url="https://api.minimaxi.com/anthropic",
      api_key=""  # 替换为你的 MiniMax API Key
  );

  const stream = await client.messages.stream({
    model: "MiniMax-M2",
    max_tokens: 1024,
    messages: [
      {
        role: "user",
        content: "写一首关于春天的诗",
      },
    ],
  });

  for await (const chunk of stream) {
    if (
      chunk.type === "content_block_delta" &&
      chunk.delta.type === "text_delta"
    ) {
      process.stdout.write(chunk.delta.text);
    }
  }
  ```
</CodeGroup>

### 工具调用（Function Calling）

更多详情参考 [MiniMax-M2 函数调用指南](/guides/text-m2-function-call)

<CodeGroup>
  ```python Python theme={null}
  import anthropic

  client = anthropic.Anthropic(
      base_url="https://api.minimaxi.com/anthropic",
      api_key=""  # 替换为你的 MiniMax API Key
  )

  tools = [
      {
          "name": "get_weather",
          "description": "获取指定城市的天气信息",
          "input_schema": {
              "type": "object",
              "properties": {
                  "city": {
                      "type": "string",
                      "description": "城市名称"
                  }
              },
              "required": ["city"]
          }
      }
  ]

  message = client.messages.create(
      model="MiniMax-M2",
      max_tokens=1024,
      tools=tools,
      messages=[
          {
              "role": "user",
              "content": "北京今天天气怎么样？"
          }
      ]
  )

  print(message.content)
  ```

  ```javascript Node.js theme={null}
  import Anthropic from "@anthropic-ai/sdk";

  const client = new Anthropic(
      base_url="https://api.minimaxi.com/anthropic",
      api_key=""  # 替换为你的 MiniMax API Key
  );

  const tools = [
    {
      name: "get_weather",
      description: "获取指定城市的天气信息",
      input_schema: {
        type: "object",
        properties: {
          city: {
            type: "string",
            description: "城市名称",
          },
        },
        required: ["city"],
      },
    },
  ];

  const message = await client.messages.create({
    model: "MiniMax-M2",
    max_tokens: 1024,
    tools: tools,
    messages: [
      {
        role: "user",
        content: "北京今天天气怎么样？",
      },
    ],
  });

  console.log(message.content);
  ```
</CodeGroup>

### 注意事项

<Warning>
  1. Anthropic API 兼容接口目前仅支持 `MiniMax-M2` 模型
  2. 使用时需要将 `ANTHROPIC_BASE_URL` 设置为 `https://api.minimaxi.com/anthropic`
  3. `ANTHROPIC_API_KEY` 应设置为您的 MiniMax API Key
  4. `temperature` 参数取值范围为 (0.0, 1.0]，推荐使用1.0，超出范围会返回错误
  5. 部分 Anthropic 参数（如 `thinking`、`top_k`、`stop_sequences`、`service_tier`、`mcp_servers`、`context_management`、`container`）会被忽略
  6. 当前不支持图像和文档类型的输入
</Warning>

## OpenAI 兼容接口

### 基础对话

<CodeGroup>
  ```python Python theme={null}
  from openai import OpenAI

  client = OpenAI(
      base_url="https://api.minimaxi.com/v1",
      api_key="", # 替换为你的 MiniMax API Key
  )

  response = client.chat.completions.create(
      model="MiniMax-M2",
      messages=[
          {
              "role": "user",
              "content": "请用简单的语言解释什么是机器学习"
          }
      ]
  )

  print(response.choices[0].message.content)
  ```

  ```javascript Node.js theme={null}
  import OpenAI from "openai";

  const client = new OpenAI(
      base_url="https://api.minimaxi.com/v1",
      api_key="", # 替换为你的 MiniMax API Key
  );

  const response = await client.chat.completions.create({
    model: "MiniMax-M2",
    messages: [
      {
        role: "user",
        content: "请用简单的语言解释什么是机器学习",
      },
    ],
  });

  console.log(response.choices[0].message.content);
  ```
</CodeGroup>

### 流式响应

<CodeGroup>
  ```python Python theme={null}
  from openai import OpenAI

  client = OpenAI(
      base_url="https://api.minimaxi.com/v1",
      api_key="", # 替换为你的 MiniMax API Key
  )

  stream = client.chat.completions.create(
      model="MiniMax-M2",
      messages=[
          {
              "role": "user",
              "content": "写一首关于春天的诗"
          }
      ],
      stream=True
  )

  for chunk in stream:
      if chunk.choices[0].delta.content:
          print(chunk.choices[0].delta.content, end="", flush=True)
  ```

  ```javascript Node.js theme={null}
  import OpenAI from "openai";

  const client = new OpenAI(
      base_url="https://api.minimaxi.com/v1",
      api_key="", # 替换为你的 MiniMax API Key
  );

  const stream = await client.chat.completions.create({
    model: "MiniMax-M2",
    messages: [
      {
        role: "user",
        content: "写一首关于春天的诗",
      },
    ],
    stream: true,
  });

  for await (const chunk of stream) {
    if (chunk.choices[0].delta.content) {
      process.stdout.write(chunk.choices[0].delta.content);
    }
  }
  ```
</CodeGroup>

### 工具调用（Function Calling）

更多详情参考 [MiniMax-M2 函数调用指南](/guides/text-m2-function-call)

<CodeGroup>
  ```python Python theme={null}
  from openai import OpenAI

  client = OpenAI(
      base_url="https://api.minimaxi.com/v1",
      api_key="", # 替换为你的 MiniMax API Key
  )

  tools = [
      {
          "type": "function",
          "function": {
              "name": "get_weather",
              "description": "获取指定城市的天气信息",
              "parameters": {
                  "type": "object",
                  "properties": {
                      "city": {
                          "type": "string",
                          "description": "城市名称"
                      }
                  },
                  "required": ["city"]
              }
          }
      }
  ]

  response = client.chat.completions.create(
      model="MiniMax-M2",
      messages=[
          {
              "role": "user",
              "content": "北京今天天气怎么样？"
          }
      ],
      tools=tools
  )

  print(response.choices[0].message)
  ```

  ```javascript Node.js theme={null}
  import OpenAI from "openai";

  const client = new OpenAI(
      base_url="https://api.minimaxi.com/v1",
      api_key="", # 替换为你的 MiniMax API Key
  );

  const tools = [
    {
      type: "function",
      function: {
        name: "get_weather",
        description: "获取指定城市的天气信息",
        parameters: {
          type: "object",
          properties: {
            city: {
              type: "string",
              description: "城市名称",
            },
          },
          required: ["city"],
        },
      },
    },
  ];

  const response = await client.chat.completions.create({
    model: "MiniMax-M2",
    messages: [
      {
        role: "user",
        content: "北京今天天气怎么样？",
      },
    ],
    tools: tools,
  });

  console.log(response.choices[0].message);
  ```
</CodeGroup>

### 注意事项

如果在使用MiniMax模型过程中遇到任何问题：

* 通过邮箱 [Model@minimax.io](mailto:Model@minimax.io) 等官方渠道联系我们的技术支持团队
* 在我们的 [Github](https://github.com/MiniMax-AI/MiniMax-M2/issues) 仓库提交Issue
* 通过我们的 [M2企业微信群](https://filecdn.minimax.chat/public/9c3bfa31-d802-4793-8282-f0e955309da7.jpg) 反馈

<Warning>
  1. 使用时需要将 `OPENAI_BASE_URL` 设置为 `https://api.minimaxi.com/v1`
  2. `OPENAI_API_KEY` 应设置为您的 MiniMax API Key
  3. `temperature` 参数取值范围为(0.0, 1.0]，推荐使用 1.0，超出范围会返回错误
  4. 部分 OpenAI 参数（如`presence_penalty`、`frequency_penalty`、`logit_bias` 等）会被忽略
  5. 当前不支持图像和音频类型的输入
  6. `n` 参数仅支持值为 1
  7. 旧版的`function_call` 已废弃，请使用 `tools` 参数
</Warning>

## 相关链接

* [Anthropic SDK 文档](https://docs.anthropic.com/en/api/client-sdks)
* [OpenAI SDK 文档](https://platform.openai.com/docs/libraries)

## 推荐阅读

<Columns cols={2}>
  <Card title="Anthropic API 兼容（推荐）" icon="book-open" href="/api-reference/text-anthropic-api" cta="点击查看">
    通过 Anthropic SDK 调用 MiniMax 模型
  </Card>

  <Card title="使用 MiniMax M2 构建 Agent 的最佳实践" icon="book-open" href="https://github.com/MiniMax-AI/Mini-Agent/blob/main/README_CN.md" cta="点击查看">
    本文档基于 mini-agent 项目的实战经验，提供构建生产级 Agent 系统的完整指南
  </Card>

  <Card title="MiniMax-M2 函数调用指南" icon="book-open" href="/guides/text-m2-function-call" cta="点击查看">
    函数调用让 AI 模型调用外部 API。
  </Card>

  <Card title="在 AI 编程工具里使用 MiniMax-M2" icon="book-open" href="/guides/text-ai-coding-tools" cta="点击查看">
    具备代码理解能力，适用于代码助手等场景。
  </Card>
</Columns>
