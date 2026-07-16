# Troubleshooting FAQ

**Common issues and solutions across all modules**

---

## Installation & Setup

### Node.js or npm not installed

**Error**: `command not found: node` or `npm`

**Solution**:
1. Install from https://nodejs.org/ (choose LTS version)
2. Verify installation:
   ```bash
   node -v
   npm -v
   ```
3. Both should show version numbers (e.g., v18.16.0)

---

### Dependency installation fails

**Error**: `npm install` fails with error messages

**Solution**:
```bash
# Clear npm cache
npm cache clean --force

# Try install again
npm install

# If still fails, try
npm install --legacy-peer-deps
```

---

## Module 3: GitHub MCP Server

### Port 3000 already in use

**Error**: `EADDRINUSE :::3000`

**Solution**:
```bash
# Option 1: Use different port
PORT=3001 npm start
# Then connect Claude to http://localhost:3001

# Option 2: Kill the process using port 3000
lsof -i :3000              # Find process ID
kill -9 <PID>              # Kill it
npm start                  # Start fresh

# Option 3: On Windows
netstat -ano | findstr :3000    # Find process
taskkill /PID <PID> /F          # Kill it
```

---

### GitHub token invalid or missing

**Error**: `401 Unauthorized` or `Error: GITHUB_TOKEN not found`

**Solution**:
1. Verify `.env` file exists:
   ```bash
   ls -la .env
   ```

2. Check token format:
   ```bash
   cat .env
   # Should show: GITHUB_TOKEN=ghp_xxxxxxxxxxxx
   ```

3. If token is missing or invalid:
   - Go to https://github.com/settings/tokens
   - Generate a new token with `repo` and `public_repo` scopes
   - Copy the new token (you won't see it again)
   - Update `.env` with new token
   - Restart server: `Ctrl+C`, then `npm start`

---

### Claude can't connect to MCP server

**Error**: "Cannot connect to MCP server" or "Connection timeout"

**Solution**:
1. Verify server is running:
   ```bash
   curl http://localhost:3000/health
   # Should return: {"status": "ok"}
   ```

2. Check URL in Claude settings:
   - Should be: `http://localhost:3000` (not https://)
   - Should be: `localhost` (not `127.0.0.1`, though that works too)

3. Firewall check:
   - Temporarily disable firewall or allow port 3000
   - Try again

4. Restart both:
   - Stop server: `Ctrl+C`
   - Stop Claude completely (close app)
   - Start server: `npm start`
   - Open Claude fresh

---

### Claude doesn't see my tools

**Error**: Claude says "No tools available" or doesn't see list_github_issues

**Solution**:
1. Verify server is running and healthy:
   ```bash
   curl http://localhost:3000/health
   ```

2. Check server output for errors:
   - Look at terminal running `npm start`
   - Should show: "✅ Tools loaded" or "Tool count: 3"
   - If errors appear, fix them

3. Reconnect Claude:
   - In Claude settings, disconnect the MCP server
   - Wait 5 seconds
   - Reconnect to `http://localhost:3000`

4. If still not working:
   - Restart server: `Ctrl+C`, then `npm start`
   - Restart Claude completely

---

### "Rate limit exceeded" error

**Error**: `429 Too Many Requests` or "Rate limit exceeded"

**Solution**:
- GitHub API has rate limits (~60 requests/hour for unauthenticated, 5000/hour for authenticated)
- Wait 10-60 minutes and try again
- The token helps. Verify it's set correctly (see above)

**To check current rate limit**:
```bash
curl -H "Authorization: token $GITHUB_TOKEN" https://api.github.com/rate_limit | jq '.rate_limit'
```

---

### Server crashes when calling a tool

**Error**: Server crashes or disconnects when Claude calls a tool

**Solution**:
1. Check server logs for error message
2. Common causes:
   - **Invalid parameters** → Claude passed wrong data type. Add validation.
   - **API error** → GitHub API returned error. Add error handling.
   - **Missing .env variable** → Token or other config missing. Check `.env`

3. Add error handling to your tool:
   ```javascript
   try {
     const result = await githubClient.listIssues(owner, repo);
     return result;
   } catch (error) {
     return {
       isError: true,
       content: [{type: "text", text: `Error: ${error.message}`}]
     };
   }
   ```

---

## Module 4: Build from Scratch (Replit)

### Replit fork fails

**Error**: "Cannot fork repository" or similar

**Solution**:
1. Verify you're logged into Replit
2. Try forking manually:
   - Go to https://replit.com/new
   - Paste repo URL
   - Click "Import"
3. If still failing, create a new Replit project and copy code manually

---

### Server runs on Replit but Claude can't connect

**Error**: "Cannot connect to Replit server"

**Solution**:
1. Check Replit URL:
   - Should be public (not private)
   - Format: `https://projectname-username.replit.dev`
   - In Claude: paste this full URL

2. Verify server is running in Replit:
   - Click "Run" button
   - Should show "Server running on..."
   - Check terminal for errors

3. If Replit server goes to sleep:
   - Replit free tier suspends idle servers
   - Click "Run" again to wake it up
   - Or upgrade to Replit Pro to keep running

---

## General Issues

### "Cannot find module" error

**Error**: `Error: Cannot find module 'xyz'`

**Solution**:
```bash
# Install missing dependency
npm install xyz

# Or reinstall all dependencies
npm install
```

---

### Code syntax errors

**Error**: `SyntaxError: Unexpected token` or similar

**Solution**:
1. Check error message for line number
2. Open that line in your editor
3. Common mistakes:
   - Missing closing brace `}`
   - Missing semicolon `;`
   - Typo in function name
   - Quote mismatch `"` vs `'`

4. Use a linter to catch errors:
   ```bash
   npm run lint
   ```

---

### Environment variable not accessible

**Error**: `process.env.GITHUB_TOKEN is undefined`

**Solution**:
1. Verify `.env` file exists:
   ```bash
   ls -la .env
   ```

2. Verify file has correct content:
   ```bash
   cat .env
   # Should show: GITHUB_TOKEN=ghp_xxxx...
   ```

3. Restart server (server reads `.env` on startup):
   ```bash
   Ctrl+C
   npm start
   ```

4. Verify variable is used correctly:
   ```javascript
   // Correct:
   const token = process.env.GITHUB_TOKEN;
   
   // Wrong:
   const token = process.env.github_token;  // Wrong case!
   ```

---

## Getting More Help

### Check server logs
The terminal running `npm start` often shows the real error:
```bash
# Example error:
Error: Invalid GitHub token
  at authenticate (github-client.mjs:15)
  at listIssues (github-client.mjs:42)
```

### Test with curl
Bypass Claude and test server directly:
```bash
# Check if server is up
curl http://localhost:3000/health

# Call a tool directly (if your server supports it)
curl -X POST http://localhost:3000/tools/list_github_issues \
  -H "Content-Type: application/json" \
  -d '{"owner": "adarsh22pm", "repo": "mcp-workshop"}'
```

### Enable debug logging
Add to your server code:
```javascript
console.log("DEBUG: Received request:", JSON.stringify(request, null, 2));
```

### Ask for help
If stuck:
1. Check this FAQ first
2. Search the course repo issues: https://github.com/adarsh22pm/mcp-workshop/issues
3. Open a new issue with:
   - What you were trying to do
   - The exact error message
   - Steps to reproduce

---

## Common Misconceptions

### ❌ "MCP is the same as an API"
No. MCP is a **protocol** for agents to discover and use APIs. You build an MCP **server** that wraps an API (or custom service).

### ❌ "I need to modify Claude's code to use my MCP"
No. Claude discovers your MCP automatically. You just connect the MCP server URL.

### ❌ "MCP servers need to be on the internet"
No. During development, localhost works fine. You can deploy later.

### ❌ "I have to use Zuplo to build MCP servers"
No. Zuplo is one approach. You can write an MCP server from scratch in any language (Node.js, Python, Go, Rust, etc.).

---

## Still Stuck?

1. **Re-read the module lesson** — Often the answer is there
2. **Search this FAQ** — Your issue might be listed above
3. **Check server logs** — The error message is usually the clue
4. **Test with curl** — Isolate whether it's Claude or server
5. **Restart everything** — Kill server, restart terminal, open Claude fresh

**Last resort**: Open an issue on GitHub with full details and error logs.

---

**Last updated**: July 16, 2024
