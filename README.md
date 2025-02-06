# busy-llm-worker
一个轻量级的 Cloudflare Worker，用于模拟繁忙的 LLM 服务器。它以 OpenAI 兼容的格式返回"服务器繁忙，请稍后再试"的响应，非常适合测试客户端的错误处理。

## 功能特点

- 支持流式输出和非流式输出
- 完全兼容 OpenAI API 格式
- 支持 CORS
- 可自定义模型名称（默认为 'deepseek-chat'）

## API 说明

### 请求格式

- 方法：POST
- 请求体格式：JSON
- 支持参数：
  - `model`：模型名称（可选，默认为 'deepseek-chat'）
  - `stream`：是否使用流式输出（可选，布尔值）

### 响应格式

#### 非流式响应

返回标准的 JSON 格式：

```json
{
  "id": "chatcmpl-[timestamp]",
  "object": "chat.completion",
  "created": [timestamp],
  "model": "[model-name]",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "服务器繁忙，请稍后再试。"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 0,
    "completion_tokens": 0,
    "total_tokens": 0
  }
}
```

#### 流式响应

以 Server-Sent Events (SSE) 格式返回，每个字符单独发送，模拟真实的流式输出效果。

## CORS 支持

支持跨域请求，设置了以下 CORS 头：
- Access-Control-Allow-Origin: *
- Access-Control-Allow-Methods: POST, OPTIONS
- Access-Control-Allow-Headers: Content-Type, Authorization

## 示例

### 测试流式响应

```bash
curl -X POST http://deepseek.closeai.moe/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-token" \
  -d '{
    "model": "deepseek-chat",
    "messages": [{"role": "user", "content": "Hello"}],
    "temperature": 0.7,
    "top_p": 1,
    "n": 1,
    "stream": true,
    "stop": null,
    "max_tokens": 2048,
    "presence_penalty": 0,
    "frequency_penalty": 0,
    "user": "user-123"
  }'
``` 

### 测试非流式响应    

```bash
curl -X POST http://deepseek.closeai.moe/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-token" \
  -d '{
    "model": "deepseek-chat",
    "messages": [{"role": "user", "content": "Hello"}],
    "temperature": 0.7,
    "top_p": 1,
    "n": 1,
    "stream": false,
    "stop": null,
    "max_tokens": 2048,
    "presence_penalty": 0,
    "frequency_penalty": 0,
    "user": "user-123"
  }'
```