# Building an MCP Server from Scratch: Architecture & Design

**Read time**: ~25 minutes  
**Prerequisites**: Modules 1–2 (optional: Module 3)

---

## 1. The Three Components of Every MCP Server

Every MCP server, no matter how simple or complex, has three essential parts:

```
┌─────────────────────────────────────────┐
│         Your MCP Server                 │
├─────────────────────────────────────────┤
│ 1. Tool Definitions (metadata)          │
│    - name, description, parameters      │
├─────────────────────────────────────────┤
│ 2. Tool Implementations (the logic)      │
│    - What each tool actually does       │
├─────────────────────────────────────────┤
│ 3. MCP Protocol Handler (the bridge)    │
│    - Listens for Claude requests        │
│    - Routes to right tool               │
│    - Returns results                    │
└─────────────────────────────────────────┘
```

### Part 1: Tool Definitions

```javascript
// tools.mjs
export const TOOLS = [
  {
    name: "get_weather",
    description: "Get current weather for a city",
    inputSchema: {
      type: "object",
      properties: {
        city: {type: "string", description: "City name"}
      },
      required: ["city"]
    }
  }
];
```

**Purpose**: Tell Claude what tools exist, what they do, and what parameters they need.

### Part 2: Tool Implementations

```javascript
// weather-api.mjs
export const weatherService = {
  async getWeather(city) {
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${API_KEY}`
    );
    const data = await response.json();
    return {
      temperature: data.main.temp,
      description: data.weather[0].description
    };
  }
};
```

**Purpose**: Actually execute the tool logic. Call APIs, compute results, etc.

### Part 3: MCP Protocol Handler

```javascript
// server.mjs
const server = new Server({name: "weather-mcp"});

server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: TOOLS  // Part 1: Tool definitions
}));

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const {name, arguments} = request.params;
  
  if (name === "get_weather") {
    return await weatherService.getWeather(arguments.city);  // Part 2: Implementation
  }
});

server.connect(process.stdin, process.stdout);
```

**Purpose**: Listen for Claude's requests, call the right implementation, return results.

---

## 2. Why Building from Scratch is Different

### Module 3: Scaffolded from API
- Start with existing GitHub API
- Use Zuplo to auto-generate tool definitions
- You fill in implementation details
- Faster but less control

### Module 4: Building from Scratch
- Start with just an idea (weather, quotes, random ideas)
- You write tool definitions manually
- You write implementations from scratch
- More work but more learning

**Key insight**: When you build from scratch, you **design the tools first**, then implement them. This forces you to think like an agent designer.

---

## 3. Anatomy of a Tool Definition

Let's design a weather tool step-by-step.

### Step 1: What is this tool?
**"Get current weather for a city"**

### Step 2: Name it (Module 2 skills!)
```javascript
name: "get_weather"
```
✅ Verb (get) + Resource (weather)

### Step 3: Describe it
```javascript
description: "Get the current weather for a city including temperature, humidity, and conditions. Use this when you need real-time weather information."
```
✅ What it does + When to use

### Step 4: Define inputs
```javascript
inputSchema: {
  type: "object",
  properties: {
    city: {
      type: "string",
      description: "City name (e.g., 'New York', 'London')"
    },
    units: {
      type: "string",
      enum: ["celsius", "fahrenheit"],
      description: "Temperature units (default: celsius)"
    }
  },
  required: ["city"]
}
```
✅ What Claude needs to pass in

### Complete Tool Definition
```javascript
{
  name: "get_weather",
  description: "Get the current weather for a city including temperature, humidity, and conditions. Use this when you need real-time weather information.",
  inputSchema: {
    type: "object",
    properties: {
      city: {
        type: "string",
        description: "City name (e.g., 'New York', 'London')"
      },
      units: {
        type: "string",
        enum: ["celsius", "fahrenheit"],
        description: "Temperature units (default: celsius)"
      }
    },
    required: ["city"]
  }
}
```

**This tool definition is "UX for agents."** When Claude reads it, it understands exactly when and how to use this tool.

---

## 4. Implementing a Tool

Once you've defined the tool, implement it:

```javascript
export const weatherService = {
  async getWeather(city, units = "celsius") {
    // Validate input
    if (!city || typeof city !== "string") {
      throw new Error("City must be a non-empty string");
    }

    // Call external API
    const apiUrl = `https://api.openweathermap.org/data/2.5/weather`;
    const response = await fetch(
      `${apiUrl}?q=${encodeURIComponent(city)}&units=${units}&appid=${process.env.OPENWEATHER_API_KEY}`
    );

    // Handle errors
    if (!response.ok) {
      if (response.status === 404) {
        throw new Error(`City not found: ${city}`);
      }
      throw new Error(`Weather API error: ${response.statusText}`);
    }

    // Parse and return results
    const data = await response.json();
    return {
      city: data.name,
      temperature: data.main.temp,
      humidity: data.main.humidity,
      description: data.weather[0].description,
      feels_like: data.main.feels_like
    };
  }
};
```

**Key patterns:**
1. **Validate inputs** — Make sure data is correct type and format
2. **Call external service** — Fetch from API, database, etc.
3. **Handle errors gracefully** — Don't crash; return error message
4. **Return formatted results** — Clean JSON the agent can understand

---

## 5. The MCP Protocol Handler

The MCP server listens for Claude's requests and routes them:

```javascript
import { Server } from "@modelcontextprotocol/sdk/server/stdio.js";
import { ListToolsRequestSchema, CallToolRequestSchema } from "@modelcontextprotocol/sdk/types.js";

const server = new Server({
  name: "weather-mcp",
  version: "1.0.0"
});

// 1. Tell Claude what tools are available
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: WEATHER_TOOLS  // From tools.mjs
}));

// 2. Handle tool calls from Claude
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  try {
    // Route to the right implementation
    switch (name) {
      case "get_weather": {
        const result = await weatherService.getWeather(
          args.city,
          args.units
        );
        return {
          content: [{
            type: "text",
            text: `Weather in ${result.city}: ${result.temperature}°${args.units === "fahrenheit" ? "F" : "C"}, ${result.description}`
          }]
        };
      }
      
      case "get_forecast": {
        const result = await weatherService.getForecast(args.city, args.days);
        return {
          content: [{
            type: "text",
            text: `Forecast for ${result.city}:\n${result.forecast.join("\n")}`
          }]
        };
      }

      default:
        return {
          isError: true,
          content: [{
            type: "text",
            text: `Unknown tool: ${name}`
          }]
        };
    }
  } catch (error) {
    return {
      isError: true,
      content: [{
        type: "text",
        text: `Error: ${error.message}`
      }]
    };
  }
});

// 3. Start listening
server.connect(process.stdin, process.stdout);
console.log("✅ Weather MCP Server started");
```

**The flow:**
1. Claude connects and asks "what tools do you have?"
2. Server responds with tool definitions (ListToolsRequestSchema)
3. Claude reads descriptions and decides to call `get_weather`
4. Claude sends request with city="London"
5. Server routes to weatherService.getWeather()
6. Implementation returns result
7. Server formats and returns to Claude
8. Claude shows you: "Weather in London: 15°C, cloudy"

---

## 6. File Structure for a From-Scratch MCP

```
weather-mcp/
├── server.mjs           # MCP protocol handler (Part 3)
├── tools.mjs            # Tool definitions (Part 1)
├── weather-service.mjs  # Tool implementations (Part 2)
├── package.json
├── .env.example
└── README.md
```

**Separation of concerns:**
- `tools.mjs` = Tool metadata (what Claude sees)
- `weather-service.mjs` = Implementations (the logic)
- `server.mjs` = Glue code (connects metadata to logic)

This makes it easy to:
- Add new tools (add to tools.mjs + server.mjs)
- Update implementations (modify weather-service.mjs)
- Test each piece independently

---

## 7. Designing Tools Before Coding

Here's the workflow:

### 1. Identify Your Service
"I want to expose the OpenWeatherMap API as tools"

### 2. List Tool Ideas
- `get_weather` — Current weather
- `get_forecast` — Multi-day forecast
- `search_cities` — Find city by name

### 3. Design Each Tool (on paper or in a doc)
```
Tool: get_weather
- Name: get_weather ✅ (verb + resource)
- Description: Get current weather for a city. Use this when the user asks about weather.
- Inputs: 
  - city (required, string): City name
  - units (optional, enum: celsius/fahrenheit): Temperature units
- Output: temperature, description, humidity, etc.
```

### 4. Validate Design
- **Is the name clear?** ✅ Yes
- **Does description explain when to use it?** ✅ Yes
- **Are required fields marked?** ✅ Yes
- **Are optional fields optional?** ✅ Yes

### 5. Implement
Only after design is clear, start coding.

---

## 8. Testing Your Design

Before deploying, test:

### Test 1: Tool Discovery
```
Question: "What tools does this MCP server have?"
Agent should see: get_weather, get_forecast, search_cities
```

### Test 2: Tool Calling
```
Question: "What's the weather in London?"
Agent should call: get_weather(city="London")
Should return: Current temperature, description, etc.
```

### Test 3: Error Handling
```
Question: "What's the weather in xyzunknowncity?"
Agent should get error: "City not found: xyzunknowncity"
Should not crash.
```

---

## 9. Replit vs. Local Development

### Replit (Recommended for Module 4)
- ✅ No local setup needed
- ✅ Cloud-based (can share easily)
- ✅ Built-in terminal and file editor
- ✅ Can stay running (with Replit Pro)
- ❌ Free tier has limitations
- ❌ Slower than local

### Local (Alternative)
- ✅ Faster development
- ✅ Full control
- ✅ Can debug easily
- ❌ Requires Node.js setup
- ❌ Harder to share (need to deploy)

**For learning: Replit is easier. For production: Consider both.**

---

## 10. Common Mistakes When Building from Scratch

### ❌ Mistake 1: Tool Does Too Much
```javascript
// BAD: One tool that does everything
{
  name: "weather_tool",
  description: "Get weather, forecast, search cities..."
}

// GOOD: Separate tools for each job
{
  name: "get_weather",
  description: "Get current weather..."
}
```

### ❌ Mistake 2: No Input Validation
```javascript
// BAD: Assume input is valid
async getWeather(city) {
  const response = await fetch(`...?q=${city}`);  // What if city is null?
}

// GOOD: Validate first
async getWeather(city) {
  if (!city || typeof city !== "string") {
    throw new Error("City must be a non-empty string");
  }
  const response = await fetch(`...?q=${encodeURIComponent(city)}`);
}
```

### ❌ Mistake 3: Vague Error Messages
```javascript
// BAD:
throw new Error("Error");

// GOOD:
throw new Error("City not found: ${city}. Check spelling.");
```

### ❌ Mistake 4: Unformatted Output
```javascript
// BAD: Return raw API response
return response.json();

// GOOD: Format for humans
return {
  city: data.name,
  temperature: Math.round(data.main.temp),
  description: data.weather[0].description
};
```

---

## 11. Summary: Building from Scratch

1. **Design first** — Tool definitions before code
2. **Three components** — Definitions, implementations, protocol handler
3. **Separate concerns** — tools.mjs, service.mjs, server.mjs
4. **Validate inputs** — Don't trust Claude
5. **Handle errors** — Return error messages, don't crash
6. **Format output** — Clean results for humans
7. **Test thoroughly** — Discovery, calling, errors

---

## ✅ What You've Learned

- ✅ Every MCP has 3 parts (definitions, implementations, protocol handler)
- ✅ How to design a tool (name, description, inputs, outputs)
- ✅ How to implement tool logic (validate, call service, return)
- ✅ How to handle errors gracefully
- ✅ File structure that scales

---

## 🚀 Next: Build It

**[→ Go to building-guide.md](building-guide.md)**

You'll use this knowledge to build an actual weather MCP server on Replit. The building-guide walks you through every step.
