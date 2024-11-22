# API使用指南

## 基本调用步骤

1. 获取访问密钥。
2. 调用API获得模型回复。

## 获取访问密钥

1. 登录 https://www.tgkwai.com/ 获取 `access token`（形如 `sk-xxxx`），**Access Token 只展示一次，请妥善保存**。
2. 调用时在 HTTP header 或 WebSocket 参数中增加：
   - HTTP header: `X-Token: Bearer sk-xxxx`
   - WebSocket 参数: `?x-token=sk-xxxx`

## WebSocket 连接

### 创建 WebSocket 连接

**请求方法**: WebSocket connect
**请求路径**: `/api/v1/qamodel/session`
**请求头**: `X-Token: Bearer sk-xxxx` 或 `?x-token=sk-xxxx`

**请求参数**:

- `session_id`（string）: 会话号，用来记录上下文，可以不带（新会话）

**请求示例**:

```bash
# 如果没有 session_id
wscat -c "wss://api.tgkwai.com/api/v1/qamodel/session?x-token=sk-xxxx"

# 如果有 session_id
wscat -c "wss://api.tgkwai.com/api/v1/qamodel/session?x-token=sk-xxxx&session_id=a8d10ffa-60ba-4af1-916e-6a918c0096e3"
```

**响应参数**:

- `data`: 会话号 `session_id`

**响应示例**:

```json
{

  "code": 200,

  "msg": "OK",

  "data": "a8d10ffa-60ba-4af1-916e-6a918c0096e3"

}
```

### 发送消息

**请求方法**: WebSocket send

**请求参数**:

- `session_id`（string）: 会话号
- messages（list）: 会话历史记录
  - `role`（string）: "user" 或 "assistant" 或 "system"
  - `content`（string）: 会话内容
  - `type`（string）: "text" 
- `stream`（boolean）: 是否流式输出

**请求示例**:

```json
{

  "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

  "messages": [

    {

      "role": "user",

      "type": "text",

      "content": "水稻最经常遭受的病害有哪些？",
    },

    {

      "role": "assistant",

      "type": "text",

      "content": "水稻最经常遭受的病害包括稻瘟病、水稻纹枯病、水稻立枯病及水稻云形病等。其中，稻瘟病是世界范围内的主要水稻病害之一，对全球水稻生产构成重大威胁。水稻纹枯病则在亚洲各稻区分布广泛，是水稻生产上发生面积最大、为害最重的病害之一。水稻立枯病主要影响水稻苗期，特别是在北方寒地水稻中更为普遍。水稻云形病的发生与天气条件、稻田生态环境、肥水管理和品种抗病性紧密相关，影响水稻生长发育。这些病害对水稻产量和品质有着显著影响，是水稻种植中需要重点防治的对象。"

    },

    {

      "role": "user",

      "type": "text",

      "content": "水稻叶片上有水渍状斑点，可能是上述什么病害？"

    },

    {

      "role": "assistant",

      "type": "text",

      "content": "根据描述，水稻叶片上出现水渍状斑点，可能是稻瘟病的一种表现形式，即苗瘟。在苗瘟阶段，病斑初期出现在芽和芽鞘上，表现为水渍状斑点。随着病情的发展，病苗基部会变黑褐色，上部则呈黄褐色或淡红色，严重时会导致病苗枯死。在潮湿环境下，病部会生长出灰绿色霉层。这与描述中的稻瘟病苗瘟症状相匹配。"

    },

    {

      "role": "user",

      "type": "multimodal",

      "content": "如何对上述提到的病害进行防治呢？",
    }

  ],

  "stream": true

}
```

**响应参数**:

- `session_id`（string）: 会话号
- `content`（string）: 流式输出的消息
- `title`（string）: 可选，第一轮问答时会给一个 `title`
- `is_finished`（bool）: 是否已经回答结束

**响应示例**:

```json
{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "content": "你好！",

    "is_finished": false

  }

}

{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "content": "我是",

    "is_finished": false

  }

}

{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "content": "天工开悟",

    "is_finished": false

  }

}

{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "content": "你好！",

    "is_finished": false

  }

}

{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "content": "<BOS>[1] 唯农209 审定意见：<EOS>[2] 唯农209 审定意见：<EOS>[3] 唯农209 审定意见：<EOS>[4] 唯农209 审定意见：<EOS>[5] 唯农209 审定意见：",

    "is_finished": false

  }

}

{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "content": "[DONE]",

    "is_finished": true

  }

}

{

  "code": 200,

  "msg": "成功",

  "data": {

    "session_id": "a8d10ffa-60ba-4af1-916e-6a918c0096e3",

    "title": "问候",

    "is_finished": true

  }

}
```



### 错误响应

- 400 - `session_id` 不能为空
- 400 - `session_id` 不匹配
- 500 - 问答模型发送消息失败

## FAQs

**Q1: 在多轮对话时，模型会遗忘之前的上下文信息。**

**A1:** 这可能是由于您在发送请求时，`message` 中缺少了之前会话的消息。在进行多轮对话时，请将历史请求和模型之前的回复一并拼接到 `message` 中，以确保上下文信息的完整性。

**Q2: 使用 Python 的 websocket 库进行访问时，无法建立连接。**

**A2:** 在使用 websocket 库建立连接时，默认会发送一个不受信任的 `Origin` 报头，导致服务器拒绝该访问请求。您可以通过禁用 `Origin` 头来解决此问题，示例如下：

```python
ws = websocket.create_connection(f"wss://api.tgkwai.com/api/v1/qamodel/session?x-token={ACCESS_TOKEN}", suppress_origin=True)
```

