# Module 4: Build from Scratch - Hands-On Guide

**Time**: 70 minutes  
**Goal**: Build a working weather MCP server on Replit from scratch

---

## Phase 1: Setup on Replit (10 minutes)

### Step 1.1: Create Replit Project

1. Go to https://replit.com
2. Click **+ Create** (or **New Replit**)
3. Select **Node.js**
4. Name it: `weather-mcp`
5. Click **Create**

Replit opens with a blank Node.js project.

### Step 1.2: Set Up Files

You'll need these 4 files:

**1. First, update `package.json`:**

Click `package.json` on the left, replace everything with:

```json
{
  "name": "weather-mcp",
  "version": "1.0.0",
  "type": "module",
  "main": "server.mjs",
  "scripts": {
    "start": "node server.mjs"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^0.5.0"
  }
}
```

**2. Create `tools.mjs`:**

Right-click in file explorer → New File → `tools.mjs`

```javascript
// Tool definitions for weather MCP
export const WEATHER_TOOLS = [
  {
    name: "get_weather",
    description:
      "Get current weather for a city including temperature, humidity, and conditions. Use this when you need real-time weather information for a specific location.",
    inputSchema: {
      type: "object",
      properties: {
        city: {
          type: "string",
          description: "City name (e.g., 'New York', 'London', 'Tokyo')",
        },
        units: {
          type: "string",
          enum: ["celsius", "fahrenheit"],
          description: "Temperature units (default: celsius)",
        },
      },
      required: ["city"],
    },
  },
  {
    name: "get_forecast",
    description:
      "Get weather forecast for the next few days. Use this when you need to plan ahead or know what weather is coming.",
    inputSchema: {
      type: "object",
      properties: {
        city: {
          type: "string",
          description: "City name",
        },
        days: {
          type: "number",
          description: "Number of forecast days (1-5, default: 3)",
        },
      },
      required: ["city"],
    },
  },
];
```

**3. Create `weather-service.mjs`:**

New File → `weather-service.mjs`

```javascript
// Weather service - implements tool logic
// Using a mock weather API for demo (no key needed)

export const weatherService = {
  async getWeather(city, units = "celsius") {
    // Validate input
    if (!city || typeof city !== "string") {
      throw new Error("City must be a non-empty string");
    }

    // For demo: return mock data
    // In production, you'd call openweathermap.org or similar
    const mockWeather = {
      "New York": { temp: 22, humidity: 65, description: "Partly cloudy" },
      "London": { temp: 15, humidity: 72, description: "Rainy" },
      "Tokyo": { temp: 18, humidity: 68, description: "Clear" },
      "Paris": { temp: 19, humidity: 60, description: "Sunny" },
      "Sydney": { temp: 25, humidity: 55, description: "Sunny" },
    };

    const cityKey = Object.keys(mockWeather).find(
      (k) => k.toLowerCase() === city.toLowerCase()
    );

    if (!cityKey) {
      throw new Error(
        `City not found: ${city}. Try: New York, London, Tokyo, Paris, Sydney`
      );
    }

    let temp = mockWeather[cityKey].temp;
    if (units === "fahrenheit") {
      temp = Math.round((temp * 9) / 5 + 32);
    }

    return {
      city: cityKey,
      temperature: temp,
      units: units,
      humidity: mockWeather[cityKey].humidity,
      description: mockWeather[cityKey].description,
      feels_like: Math.round(temp - 2),
    };
  },

  async getForecast(city, days = 3) {
    // Validate
    if (!city || typeof city !== "string") {
      throw new Error("City must be a non-empty string");
    }

    const daysNum = Math.min(Math.max(days || 3, 1), 5);

    // Mock forecast
    const conditions = [
      "Sunny",
      "Partly cloudy",
      "Rainy",
      "Cloudy",
      "Clear",
    ];
    const forecast = [];

    for (let i = 1; i <= daysNum; i++) {
      const date = new Date();
      date.setDate(date.getDate() + i);
      forecast.push(
        `Day ${i} (${date.toLocaleDateString()}): ${
          conditions[Math.floor(Math.random() * conditions.length)]
        }, High 23°C`
      );
    }

    return {
      city: city,
      forecast: forecast,
    };
  },
};
```

**4. Create `server.mjs`:**

New File → `server.mjs`

```javascript
#!/usr/bin/env node

import { Server } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";
import { weatherService } from "./weather-service.mjs";
import { WEATHER_TOOLS } from "./tools.mjs";

// Create MCP server
const server = new Server({
  name: "weather-mcp",
  version: "1.0.0",
});

// List available tools
server.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: WEATHER_TOOLS,
}));

// Handle tool calls from Claude
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  try {
    switch (name) {
      case "get_weather": {
        const city = args.city;
        const units = args.units || "celsius";

        const result = await weatherService.getWeather(city, units);

        return {
          content: [
            {
              type: "text",
              text: `Weather in ${result.city}: ${result.temperature}°${
                units === "fahrenheit" ? "F" : "C"
              }, ${result.description} (Humidity: ${result.humidity}%)`,
            },
          ],
        };
      }

      case "get_forecast": {
        const city = args.city;
        const days = args.days || 3;

        const result = await weatherService.getForecast(city, days);

        return {
          content: [
            {
              type: "text",
              text: `Forecast for ${result.city}:\n${result.forecast.join(
                "\n"
              )}`,
            },
          ],
        };
      }

      default:
        return {
          isError: true,
          content: [
            {
              type: "text",
              text: `Unknown tool: ${name}`,
            },
          ],
        };
    }
  } catch (error) {
    return {
      isError: true,
      content: [
        {
          type: "text",
          text: `Error: ${error.message}`,
        },
      ],
    };
  }
});

// Start server
server.connect(process.stdin, process.stdout);

console.log("🚀 Weather MCP Server started");
console.log("✅ Tools loaded:");
WEATHER_TOOLS.forEach((tool) => {
  console.log(`  - ${tool.name}`);
});
console.log("Ready for connections from Claude");
```

### Step 1.3: Install Dependencies

In Replit terminal (bottom):

```bash
npm install
```

Wait for it to finish (should take ~30 seconds).

### Step 1.4: Test Locally

```bash
npm start
```

You should see:
```
🚀 Weather MCP Server started
✅ Tools loaded:
  - get_weather
  - get_forecast
Ready for connections from Claude
```

✅ **Server is running locally on Replit!**

---

## Phase 2: Connect Claude to Your Replit Server (5 minutes)

### Step 2.1: Get Replit URL

In the Replit interface:
1. Look for **Output** panel (usually on right)
2. Copy the public URL (should look like `https://weather-mcp-xxxx.replit.dev`)

**Note**: The URL format might change. Look for where Replit shows "Live URL" or similar.

### Step 2.2: Connect Claude

In Claude (web or desktop):

1. **Settings** → **MCP Servers**
2. Click **Add MCP Server**:
   - **Name**: `weather-mcp`
   - **URL**: Paste your Replit URL from Step 2.1
   - **Type**: `local` or just leave blank
3. Click **Save/Connect**

You should see: ✅ **Connected to weather-mcp**

---

## Phase 3: Test Your MCP with Claude (20 minutes)

### Test 3.1: Tool Discovery

Ask Claude:
```
What tools does my weather MCP server have?
```

Claude should respond:
```
I can see 2 tools from your weather MCP server:
1. get_weather - Get current weather for a city
2. get_forecast - Get weather forecast for next days
```

✅ **Tool discovery works!**

### Test 3.2: Get Weather

Ask Claude:
```
What's the weather in London?
```

Claude will:
1. See the `get_weather` tool
2. Call it with city="London"
3. Get results from your MCP server
4. Show you: "Weather in London: 15°C, Rainy (Humidity: 72%)"

✅ **Tool calling works!**

### Test 3.3: Get Forecast

Ask Claude:
```
What's the weather forecast for Tokyo for the next 5 days?
```

Claude will call `get_forecast` and show:
```
Forecast for Tokyo:
Day 1 (7/20/2024): Sunny, High 23°C
Day 2 (7/21/2024): Cloudy, High 23°C
Day 3 (7/22/2024): Rainy, High 23°C
Day 4 (7/23/2024): Clear, High 23°C
Day 5 (7/24/2024): Partly cloudy, High 23°C
```

✅ **Multiple tools work!**

### Test 3.4: Error Handling

Ask Claude:
```
What's the weather in Narnia?
```

Claude will get error: "City not found: Narnia. Try: New York, London, Tokyo, Paris, Sydney"

Claude should respond gracefully:
```
I couldn't find weather for Narnia. It's not in the available cities. 
You can ask about: New York, London, Tokyo, Paris, or Sydney.
```

✅ **Error handling works!**

---

## Phase 4: Customize (15 minutes)

Now that you have a working template, customize it for your own idea.

### Option A: Add More Cities

Edit `weather-service.mjs`, add to `mockWeather`:

```javascript
const mockWeather = {
  "New York": { temp: 22, humidity: 65, description: "Partly cloudy" },
  "London": { temp: 15, humidity: 72, description: "Rainy" },
  // ADD MORE:
  "Barcelona": { temp: 26, humidity: 50, description: "Sunny" },
  "Berlin": { temp: 20, humidity: 65, description: "Cloudy" },
};
```

### Option B: Use Real Weather API

Replace `weather-service.mjs` to call a real API:

```javascript
export const weatherService = {
  async getWeather(city, units = "celsius") {
    // Call real API
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${process.env.OPENWEATHER_API_KEY}&units=${units}`
    );
    
    if (!response.ok) {
      throw new Error("City not found or API error");
    }
    
    const data = await response.json();
    return {
      city: data.name,
      temperature: Math.round(data.main.temp),
      humidity: data.main.humidity,
      description: data.weather[0].main,
    };
  }
};
```

Then add `OPENWEATHER_API_KEY` to Replit Secrets.

### Option C: Build Something Completely Different

Use this template for your own service:
- **Quotes API** — get random quotes
- **Jokes API** — get jokes
- **Currency Exchange** — get exchange rates
- **Sports Stats** — get game scores
- **Stock Prices** — get stock data

The pattern is the same:
1. Add tool definition to `tools.mjs`
2. Implement logic in service `.mjs`
3. Route in `server.mjs`

---

## Phase 5: Save & Deploy (10 minutes)

### Step 5.1: Save Your Work

Replit auto-saves, but manually save:
- **Ctrl+S** (or **Cmd+S**)

### Step 5.2: Optional: Push to GitHub

To keep your code:

```bash
# In Replit terminal
git init
git add .
git commit -m "Initial weather MCP server"
git remote add origin https://github.com/YOUR_USERNAME/weather-mcp
git push -u origin main
```

### Step 5.3: Share Your MCP

Your Replit URL is shareable. Others can use your MCP by:
1. Creating a Claude MCP connection
2. Pointing to your Replit URL
3. Calling your tools

---

## 📋 Verification Checklist

Before saying "done":

- [ ] `npm install` completed without errors
- [ ] `npm start` shows all tools loaded
- [ ] Claude can see your tools (tool discovery)
- [ ] Claude can call `get_weather` and get results
- [ ] Claude can call `get_forecast` and get results
- [ ] Error handling works (tested with invalid city)
- [ ] Customized at least one thing (added cities, changed tool, etc.)
- [ ] Code is on GitHub or backed up

---

## 🎯 What You've Built

✅ **Complete MCP server from scratch** (no scaffolding)  
✅ **Three-component architecture** (definitions, implementations, handler)  
✅ **Error handling** (invalid inputs, graceful failures)  
✅ **Multiple tools** (weather + forecast)  
✅ **Integration with Claude** (real tool calling)  
✅ **Customization** (added your own changes)  

**You can now build MCP servers for any idea.**

---

## 🚀 Next Steps

**[→ Continue to Capstone Project](../capstone/README.md)**

You've now completed Modules 3 and 4:
- ✅ Built an MCP from an existing API (GitHub)
- ✅ Built an MCP from scratch (Weather)

The capstone is your chance to build a **third MCP** for a product or service you care about.

---

## 💡 Tips for Your Capstone

1. **Pick something you use daily** — Makes it more useful
2. **Start with 1-2 tools** — Don't overcomplicate
3. **Design first** — Write tool definitions before code
4. **Test thoroughly** — Try edge cases
5. **Document well** — README explains what it does

---

**Stuck?** Check [resources/troubleshooting-faq.md](../../resources/troubleshooting-faq.md)

**Questions?** See [Module 4 Lesson](lesson.md) for architecture details.
