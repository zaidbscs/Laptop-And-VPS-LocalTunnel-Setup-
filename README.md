Here is your README formatted cleanly and professionally using Markdown. You can copy and paste this directly into a `README.md` file.

```markdown
# 🚀 Bot Control Panel Server Setup Guide

This guide provides the setup instructions for running your Bot Control Panel server in two different environments: **Local Development (Laptop)** and **Production (VPS)**.

---

## 💻 1. Laptop Setup (Local Development)
*Use this when running the server on your personal computer.*

### 📄 File: `server.js`
```javascript
const express = require('express');
const localtunnel = require('localtunnel');
const cors = require('cors');

const app = express();
const PORT = 3000;

app.use(cors());
app.use(express.json());

// Dummy Endpoint
app.get('/api/status', (req, res) => {
    res.json({ status: 'Laptop Server Online', bots: [] });
});

// Start Server & Auto-Create Tunnel
app.listen(PORT, async () => {
    console.log(`✅ Local server running on http://localhost:${PORT}`);
    
    try {
        // This creates the public URL automatically
        const tunnel = await localtunnel({ port: PORT });
        console.log(`🌍 Public URL for your frontend: ${tunnel.url}`);
        console.log(`👉 Paste this URL into your frontend's API_URL variable!`);
    } catch (err) {
        console.error('❌ Tunnel failed:', err.message);
    }
});
```

### ⚙️ How to run it on your Laptop:
1. Open your terminal and install the required dependencies:
   ```bash
   npm install express cors localtunnel
   ```
2. Run the server:
   ```bash
   node server.js
   ```
3. Copy the `https://xxx.loca.lt` URL printed in the console and paste it into your frontend HTML/JS file as the `API_URL`.

---

## 🖥️ 2. VPS Setup (LocalTunnel + PM2)
*Use this when deploying to a remote server. We use a **fixed subdomain** here so that every time PM2 restarts your app, your public URL remains exactly the same.*

### 📄 File: `server.js`
```javascript
const express = require('express');
const localtunnel = require('localtunnel');
const cors = require('cors');

const app = express();
const PORT = 3000;

app.use(cors());
app.use(express.json());

// Dummy Endpoint
app.get('/api/status', (req, res) => {
    res.json({ status: 'VPS Server Online via LocalTunnel', bots: [] });
});

app.listen(PORT, async () => {
    console.log(`✅ VPS server running locally on port ${PORT}`);
    
    try {
        // IMPORTANT: We use a fixed subdomain so the URL never changes when PM2 restarts the app
        const tunnel = await localtunnel({ 
            port: PORT, 
            subdomain: 'my-vps-bot-panel' // ⚠️ Change this to your preferred unique name
        });
        
        console.log(`🌍 Your permanent Public URL: ${tunnel.url}`);
        
        tunnel.on('close', () => {
            console.log('⚠️ Tunnel closed. PM2 will likely restart the process.');
        });
    } catch (err) {
        console.error('❌ LocalTunnel failed:', err.message);
    }
});
```

### 🚀 How to run it 24/7 on your VPS using PM2
Since it's a VPS, you want it to run in the background and survive server reboots.

**Step 1: Install dependencies**
```bash
npm install express cors localtunnel
npm install -g pm2
```

**Step 2: Start the app with PM2**
```bash
pm2 start server.js --name "bot-control-api"
```

**Step 3: Check the logs to get your URL**
```bash
pm2 logs bot-control-api
```
> 💡 *You will see your URL printed in the console (e.g., `https://my-vps-bot-panel.loca.lt`). Copy this URL!*

**Step 4: Make it survive VPS reboots**
```bash
pm2 save
pm2 startup
```
> ⚠️ **Important:** The `pm2 startup` command will output a specific command in your terminal. **Copy and paste that exact command** back into your terminal and press Enter to finish the setup.

---

## 🔗 3. Frontend Connection

Now, in your frontend HTML/JS file, you just use the LocalTunnel URL. You **do not** need to use your VPS IP address or open any firewall ports!

```javascript
// In your frontend HTML/JS
const API_URL = 'https://my-vps-bot-panel.loca.lt'; // Use the exact URL from your PM2 logs
```

---

## 💡 Pro Tip: VPS + LocalTunnel

> **Note:** LocalTunnel is designed primarily for local development. While it works perfectly fine on a VPS for personal use, the free LocalTunnel servers can occasionally be a little slow or drop connections if there is heavy traffic.
> 
> **Fix:** If you ever notice your VPS tunnel dropping or becoming unresponsive, just run:
> ```bash
> pm2 restart bot-control-api
> ```
> *It will instantly reconnect and generate a fresh tunnel!*
```

### Why this format is better:
1. **Clear Hierarchy**: Uses `#`, `##`, and `###` to make sections easy to scan.
2. **Syntax Highlighting**: Code blocks are explicitly tagged (`javascript`, `bash`) for proper color coding in GitHub/GitLab/VS Code.
3. **Visual Callouts**: Uses blockquotes (`>`) and emojis to highlight important warnings and tips, preventing critical steps (like the `pm2 startup` command) from being missed.
4. **Clean Spacing**: Proper line breaks make it highly readable on both desktop and mobile screens.
