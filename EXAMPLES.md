# Usage Examples

Complete code examples for integrating UcuzToken API with different programming languages and frameworks.

---

## Table of Contents
- [Python Examples](#python-examples)
- [JavaScript/TypeScript Examples](#javascripttypescript-examples)
- [Go Examples](#go-examples)
- [Java Examples](#java-examples)
- [PHP Examples](#php-examples)
- [Ruby Examples](#ruby-examples)
- [C# Examples](#c-examples)

---

## Python Examples

### Claude with Python SDK

```python
import anthropic
import os

# Initialize client
client = anthropic.Anthropic(
    api_key=os.environ.get("UCUZTOKEN_API_KEY"),
    base_url="https://api.ucuztoken.com/v1"
)

# Simple message
message = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Hello, Claude!"}
    ]
)
print(message.content[0].text)

# Streaming response
with client.messages.stream(
    model="claude-3-5-sonnet-20241022",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Write a short story"}
    ]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

### GPT with OpenAI SDK

```python
from openai import OpenAI
import os

# Initialize client
client = OpenAI(
    api_key=os.environ.get("UCUZTOKEN_API_KEY"),
    base_url="https://api.ucuztoken.com/v1"
)

# Simple completion
completion = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello, GPT!"}
    ]
)
print(completion.choices[0].message.content)

# Streaming response
stream = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Write a short story"}],
    stream=True
)
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="", flush=True)
```

### Gemini with Google SDK

```python
import google.generativeai as genai
import os

# Configure client
genai.configure(
    api_key=os.environ.get("UCUZTOKEN_API_KEY"),
    transport="rest",
    client_options={"api_endpoint": "https://api.ucuztoken.com/v1"}
)

# Simple generation
model = genai.GenerativeModel('gemini-pro')
response = model.generate_content("Hello, Gemini!")
print(response.text)

# Streaming response
response = model.generate_content("Write a short story", stream=True)
for chunk in response:
    print(chunk.text, end="", flush=True)
```

---

## JavaScript/TypeScript Examples

### Claude with Anthropic SDK

```typescript
import Anthropic from '@anthropic-ai/sdk';

const client = new Anthropic({
  apiKey: process.env.UCUZTOKEN_API_KEY,
  baseURL: 'https://api.ucuztoken.com/v1'
});

// Simple message
async function chat() {
  const message = await client.messages.create({
    model: 'claude-3-5-sonnet-20241022',
    max_tokens: 1024,
    messages: [
      { role: 'user', content: 'Hello, Claude!' }
    ]
  });
  console.log(message.content[0].text);
}

// Streaming response
async function streamChat() {
  const stream = await client.messages.stream({
    model: 'claude-3-5-sonnet-20241022',
    max_tokens: 1024,
    messages: [
      { role: 'user', content: 'Write a short story' }
    ]
  });

  for await (const chunk of stream) {
    if (chunk.type === 'content_block_delta') {
      process.stdout.write(chunk.delta.text);
    }
  }
}

chat();
```

### GPT with OpenAI SDK

```typescript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: process.env.UCUZTOKEN_API_KEY,
  baseURL: 'https://api.ucuztoken.com/v1'
});

// Simple completion
async function chat() {
  const completion = await client.chat.completions.create({
    model: 'gpt-4',
    messages: [
      { role: 'system', content: 'You are a helpful assistant.' },
      { role: 'user', content: 'Hello, GPT!' }
    ]
  });
  console.log(completion.choices[0].message.content);
}

// Streaming response
async function streamChat() {
  const stream = await client.chat.completions.create({
    model: 'gpt-4',
    messages: [{ role: 'user', content: 'Write a short story' }],
    stream: true
  });

  for await (const chunk of stream) {
    process.stdout.write(chunk.choices[0]?.delta?.content || '');
  }
}

chat();
```

### Express.js API Server

```typescript
import express from 'express';
import Anthropic from '@anthropic-ai/sdk';

const app = express();
app.use(express.json());

const client = new Anthropic({
  apiKey: process.env.UCUZTOKEN_API_KEY,
  baseURL: 'https://api.ucuztoken.com/v1'
});

app.post('/api/chat', async (req, res) => {
  try {
    const { message } = req.body;
    
    const response = await client.messages.create({
      model: 'claude-3-5-sonnet-20241022',
      max_tokens: 1024,
      messages: [{ role: 'user', content: message }]
    });

    res.json({ 
      response: response.content[0].text,
      usage: response.usage 
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

## Go Examples

### Claude with Go

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "net/http"
    "os"
)

type ClaudeRequest struct {
    Model      string    `json:"model"`
    MaxTokens  int       `json:"max_tokens"`
    Messages   []Message `json:"messages"`
}

type Message struct {
    Role    string `json:"role"`
    Content string `json:"content"`
}

type ClaudeResponse struct {
    Content []Content `json:"content"`
}

type Content struct {
    Text string `json:"text"`
}

func main() {
    apiKey := os.Getenv("UCUZTOKEN_API_KEY")
    
    reqBody := ClaudeRequest{
        Model:     "claude-3-5-sonnet-20241022",
        MaxTokens: 1024,
        Messages: []Message{
            {Role: "user", Content: "Hello, Claude!"},
        },
    }

    jsonData, _ := json.Marshal(reqBody)
    
    req, _ := http.NewRequest("POST", 
        "https://api.ucuztoken.com/v1/messages", 
        bytes.NewBuffer(jsonData))
    
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("X-API-Key", apiKey)
    req.Header.Set("anthropic-version", "2023-06-01")

    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close()

    body, _ := io.ReadAll(resp.Body)
    
    var response ClaudeResponse
    json.Unmarshal(body, &response)
    
    fmt.Println(response.Content[0].Text)
}
```

### GPT with Go

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "net/http"
    "os"
)

type GPTRequest struct {
    Model    string       `json:"model"`
    Messages []GPTMessage `json:"messages"`
}

type GPTMessage struct {
    Role    string `json:"role"`
    Content string `json:"content"`
}

type GPTResponse struct {
    Choices []Choice `json:"choices"`
}

type Choice struct {
    Message GPTMessage `json:"message"`
}

func main() {
    apiKey := os.Getenv("UCUZTOKEN_API_KEY")
    
    reqBody := GPTRequest{
        Model: "gpt-4",
        Messages: []GPTMessage{
            {Role: "user", Content: "Hello, GPT!"},
        },
    }

    jsonData, _ := json.Marshal(reqBody)
    
    req, _ := http.NewRequest("POST",
        "https://api.ucuztoken.com/v1/chat/completions",
        bytes.NewBuffer(jsonData))
    
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("Authorization", "Bearer "+apiKey)

    client := &http.Client{}
    resp, _ := client.Do(req)
    defer resp.Body.Close()

    body, _ := io.ReadAll(resp.Body)
    
    var response GPTResponse
    json.Unmarshal(body, &response)
    
    fmt.Println(response.Choices[0].Message.Content)
}
```

---

## Java Examples

### Claude with Java

```java
import java.net.http.*;
import java.net.URI;
import org.json.*;

public class ClaudeExample {
    private static final String API_KEY = System.getenv("UCUZTOKEN_API_KEY");
    private static final String BASE_URL = "https://api.ucuztoken.com/v1";

    public static void main(String[] args) throws Exception {
        JSONObject request = new JSONObject()
            .put("model", "claude-3-5-sonnet-20241022")
            .put("max_tokens", 1024)
            .put("messages", new JSONArray()
                .put(new JSONObject()
                    .put("role", "user")
                    .put("content", "Hello, Claude!")));

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest httpRequest = HttpRequest.newBuilder()
            .uri(URI.create(BASE_URL + "/messages"))
            .header("Content-Type", "application/json")
            .header("X-API-Key", API_KEY)
            .header("anthropic-version", "2023-06-01")
            .POST(HttpRequest.BodyPublishers.ofString(request.toString()))
            .build();

        HttpResponse<String> response = client.send(
            httpRequest, 
            HttpResponse.BodyHandlers.ofString()
        );

        JSONObject jsonResponse = new JSONObject(response.body());
        String text = jsonResponse
            .getJSONArray("content")
            .getJSONObject(0)
            .getString("text");
        
        System.out.println(text);
    }
}
```

---

## PHP Examples

### Claude with PHP

```php
<?php

function callClaude($message) {
    $apiKey = getenv('UCUZTOKEN_API_KEY');
    $url = 'https://api.ucuztoken.com/v1/messages';
    
    $data = [
        'model' => 'claude-3-5-sonnet-20241022',
        'max_tokens' => 1024,
        'messages' => [
            ['role' => 'user', 'content' => $message]
        ]
    ];
    
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Content-Type: application/json',
        'X-API-Key: ' . $apiKey,
        'anthropic-version: 2023-06-01'
    ]);
    
    $response = curl_exec($ch);
    curl_close($ch);
    
    $decoded = json_decode($response, true);
    return $decoded['content'][0]['text'];
}

echo callClaude('Hello, Claude!');
?>
```

### GPT with PHP

```php
<?php

function callGPT($message) {
    $apiKey = getenv('UCUZTOKEN_API_KEY');
    $url = 'https://api.ucuztoken.com/v1/chat/completions';
    
    $data = [
        'model' => 'gpt-4',
        'messages' => [
            ['role' => 'user', 'content' => $message]
        ]
    ];
    
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Content-Type: application/json',
        'Authorization: Bearer ' . $apiKey
    ]);
    
    $response = curl_exec($ch);
    curl_close($ch);
    
    $decoded = json_decode($response, true);
    return $decoded['choices'][0]['message']['content'];
}

echo callGPT('Hello, GPT!');
?>
```

---

## Ruby Examples

### Claude with Ruby

```ruby
require 'net/http'
require 'json'

def call_claude(message)
  api_key = ENV['UCUZTOKEN_API_KEY']
  uri = URI('https://api.ucuztoken.com/v1/messages')
  
  request = Net::HTTP::Post.new(uri)
  request['Content-Type'] = 'application/json'
  request['X-API-Key'] = api_key
  request['anthropic-version'] = '2023-06-01'
  
  request.body = {
    model: 'claude-3-5-sonnet-20241022',
    max_tokens: 1024,
    messages: [
      { role: 'user', content: message }
    ]
  }.to_json
  
  response = Net::HTTP.start(uri.hostname, uri.port, use_ssl: true) do |http|
    http.request(request)
  end
  
  JSON.parse(response.body)['content'][0]['text']
end

puts call_claude('Hello, Claude!')
```

---

## C# Examples

### Claude with C#

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        var apiKey = Environment.GetEnvironmentVariable("UCUZTOKEN_API_KEY");
        var client = new HttpClient();
        
        var request = new
        {
            model = "claude-3-5-sonnet-20241022",
            max_tokens = 1024,
            messages = new[]
            {
                new { role = "user", content = "Hello, Claude!" }
            }
        };
        
        var content = new StringContent(
            JsonSerializer.Serialize(request),
            Encoding.UTF8,
            "application/json"
        );
        
        client.DefaultRequestHeaders.Add("X-API-Key", apiKey);
        client.DefaultRequestHeaders.Add("anthropic-version", "2023-06-01");
        
        var response = await client.PostAsync(
            "https://api.ucuztoken.com/v1/messages",
            content
        );
        
        var responseBody = await response.Content.ReadAsStringAsync();
        Console.WriteLine(responseBody);
    }
}
```

---

## Environment Variables

For all examples, set your API key as an environment variable:

### Linux/macOS
```bash
export UCUZTOKEN_API_KEY="your-api-key-here"
```

### Windows (PowerShell)
```powershell
$env:UCUZTOKEN_API_KEY="your-api-key-here"
```

### Windows (CMD)
```cmd
set UCUZTOKEN_API_KEY=your-api-key-here
```

### .env file
```
UCUZTOKEN_API_KEY=your-api-key-here
```

---

## Error Handling Best Practices

### Python
```python
try:
    message = client.messages.create(...)
except anthropic.APIError as e:
    print(f"API Error: {e}")
except anthropic.RateLimitError as e:
    print(f"Rate limit exceeded: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
```

### JavaScript
```typescript
try {
  const message = await client.messages.create(...);
} catch (error) {
  if (error instanceof Anthropic.APIError) {
    console.error('API Error:', error.message);
  } else {
    console.error('Unexpected error:', error);
  }
}
```

---

## More Examples

For more examples and integration guides, visit our [GitHub repository](https://github.com/ucuztoken/docs).
