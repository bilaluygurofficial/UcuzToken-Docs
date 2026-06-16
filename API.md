# API Documentation

## Base URL
```
https://api.ucuztoken.com/v1
```

## Authentication

All API requests require authentication using an API key. Include your API key in the request headers.

### Claude API
```
X-API-Key: YOUR_API_KEY
```

### GPT API
```
Authorization: Bearer YOUR_API_KEY
```

### Gemini API
```
X-API-Key: YOUR_API_KEY
```

---

## Endpoints

### 1. Claude API

#### Messages Endpoint
**POST** `/messages`

Create a message with Claude models.

**Headers:**
```
Content-Type: application/json
X-API-Key: YOUR_API_KEY
anthropic-version: 2023-06-01
```

**Request Body:**
```json
{
  "model": "claude-3-5-sonnet-20241022",
  "max_tokens": 1024,
  "messages": [
    {
      "role": "user",
      "content": "Your message here"
    }
  ]
}
```

**Available Models:**
- `claude-3-5-sonnet-20241022`
- `claude-3-5-haiku-20241022`
- `claude-3-opus-20240229`
- `claude-3-sonnet-20240229`
- `claude-3-haiku-20240307`

**Response:**
```json
{
  "id": "msg_xxx",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "Response text here"
    }
  ],
  "model": "claude-3-5-sonnet-20241022",
  "usage": {
    "input_tokens": 10,
    "output_tokens": 20
  }
}
```

---

### 2. GPT API

#### Chat Completions Endpoint
**POST** `/chat/completions`

Create a chat completion with GPT models.

**Headers:**
```
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY
```

**Request Body:**
```json
{
  "model": "gpt-4",
  "messages": [
    {
      "role": "user",
      "content": "Your message here"
    }
  ],
  "temperature": 0.7,
  "max_tokens": 1000
}
```

**Available Models:**
- `gpt-4`
- `gpt-4-turbo`
- `gpt-4-turbo-preview`
- `gpt-3.5-turbo`
- `gpt-3.5-turbo-16k`

**Response:**
```json
{
  "id": "chatcmpl-xxx",
  "object": "chat.completion",
  "created": 1234567890,
  "model": "gpt-4",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Response text here"
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 10,
    "completion_tokens": 20,
    "total_tokens": 30
  }
}
```

---

### 3. Gemini API

#### Generate Content Endpoint
**POST** `/models/{model}:generateContent`

Generate content with Gemini models.

**Headers:**
```
Content-Type: application/json
X-API-Key: YOUR_API_KEY
```

**Request Body:**
```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "Your message here"
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.7,
    "maxOutputTokens": 1000
  }
}
```

**Available Models:**
- `gemini-pro`
- `gemini-pro-vision`
- `gemini-1.5-pro`
- `gemini-1.5-flash`

**Response:**
```json
{
  "candidates": [
    {
      "content": {
        "parts": [
          {
            "text": "Response text here"
          }
        ]
      },
      "finishReason": "STOP"
    }
  ],
  "usageMetadata": {
    "promptTokenCount": 10,
    "candidatesTokenCount": 20,
    "totalTokenCount": 30
  }
}
```

---

## Error Responses

All endpoints return standard HTTP status codes and error messages.

### Error Format
```json
{
  "error": {
    "message": "Error description",
    "type": "error_type",
    "code": "error_code"
  }
}
```

### Common Status Codes

| Code | Description |
|------|-------------|
| 200 | Success |
| 400 | Bad Request - Invalid parameters |
| 401 | Unauthorized - Invalid API key |
| 402 | Payment Required - Insufficient credits |
| 429 | Too Many Requests - Rate limit exceeded (should not happen with UcuzToken) |
| 500 | Internal Server Error |
| 503 | Service Unavailable |

---

## Rate Limits

**UcuzToken does NOT impose rate limits.** You can make as many requests as your credit allows.

---

## Credit Consumption

Credits are consumed based on token usage:
- **Input tokens**: Tokens in your request
- **Output tokens**: Tokens in the response

Different models have different pricing per token. Check your dashboard for real-time credit balance.

---

## Best Practices

1. **Store API Keys Securely**
   - Never commit API keys to version control
   - Use environment variables
   - Rotate keys periodically

2. **Handle Errors Gracefully**
   - Implement exponential backoff for retries
   - Check error messages for specific issues
   - Monitor credit balance regularly

3. **Optimize Token Usage**
   - Keep prompts concise
   - Use appropriate max_tokens limits
   - Choose the right model for your use case

4. **Monitor Usage**
   - Check your dashboard regularly
   - Set up alerts for low credit
   - Track which applications consume most credits

---

## Support

For API issues or questions:
- **WhatsApp**: 24/7 support
- **Website**: [ucuztoken.com](https://ucuztoken.com)
- **Email**: Available on website

---

## Changelog

### v1.0.0 (Current)
- Initial release
- Support for Claude, GPT, and Gemini APIs
- Unified authentication
- Credit-based billing
- No rate limits
