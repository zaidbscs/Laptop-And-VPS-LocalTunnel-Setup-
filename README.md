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



If you deploy this setup on your **laptop**, it will work perfectly for testing and development, but it comes with **3 major limitations** compared to a VPS. 

Here is exactly what happens and how to handle it:

---

### ⚠️ The 3 Big Catchers of Using a Laptop

1. **It dies when your laptop sleeps or shuts down**  
   If you close your laptop lid, it goes to sleep, loses Wi-Fi, or runs out of battery, the LocalTunnel connection drops **instantly**. Your frontend will show a "Server Unreachable" error until you wake the laptop up and restart the server.
2. **The URL might change**  
   If the tunnel drops and restarts, LocalTunnel might assign you a *new* random URL (e.g., changing from `https://abc.loca.lt` to `https://xyz.loca.lt`). You would then have to manually update your frontend code with the new URL.
3. **Security Risk**  
   You are creating a direct tunnel from the public internet into your personal computer. If there is a bug or vulnerability in your code, a hacker could theoretically access files on your laptop. (A VPS is safer because if it gets compromised, you just delete it and make a new one).

---

### ✅ How to make it *as close to 24/7 as possible* on your Laptop

If you still want to run it on your laptop (which is totally fine for testing!), do these 3 things:

#### 1. Prevent your laptop from sleeping
- **Windows:** Go to *Settings > System > Power & battery > Screen and sleep*. Set "When plugged in, put my device to sleep after" to **Never**.
- **Mac:** Go to *System Settings > Lock Screen* and set "Turn display off on power adapter" to **Never**. (Or use a free app like *Amphetamine* to keep it awake).
- *Keep your laptop plugged into the charger and, if possible, use an Ethernet cable instead of Wi-Fi for stability.*

#### 2. Use a Fixed Subdomain (Just like the VPS)
Update your laptop `server.js` to use a fixed subdomain. This way, even if it restarts, the URL stays the same.

```javascript
// LAPTOP server.js
const express = require('express');
const localtunnel = require('localtunnel');
const cors = require('cors');

const app = express();
const PORT = 3000;

app.use(cors());
app.use(express.json());

app.get('/api/status', (req, res) => {
    res.json({ status: 'Laptop Server Online', bots: [] });
});

app.listen(PORT, async () => {
    console.log(`✅ Local server running on http://localhost:${PORT}`);
    
    const startTunnel = async () => {
        try {
            // Added a fixed subdomain so the URL doesn't change on restart
            const tunnel = await localtunnel({ 
                port: PORT, 
                subdomain: 'my-laptop-bot-panel' 
            });
            
            console.log(`🌍 Public URL: ${tunnel.url}`);
            
            tunnel.on('close', () => {
                console.log('⚠️ Tunnel dropped! Restarting...');
                process.exit(1); // Force restart
            });
        } catch (err) {
            console.error('❌ Tunnel failed:', err.message);
            setTimeout(startTunnel, 5000); // Retry after 5 seconds
        }
    };

    startTunnel();
});
```

#### 3. Use PM2 on your laptop too!
You don't need a VPS to use PM2. It’s actually highly recommended on a laptop to automatically restart the server if it crashes.

1. Install PM2 globally on your laptop:  
   ```bash
   npm install -g pm2
   ```
2. Start your server with PM2 instead of `node`:  
   ```bash
   pm2 start server.js --name "laptop-bot-api"
   ```
3. To stop it when you are done testing:  
   ```bash
   pm2 stop laptop-bot-api
   ```

---

### 🎯 The Bottom Line

- **Laptop Deployment:** Great for **building, testing, and debugging**. It will stay live *only while your laptop is awake, plugged in, and connected to the internet*.
- **VPS Deployment:** Required for **actual 24/7 production use**. It doesn't sleep, doesn't lose Wi-Fi, and is isolated from your personal files.

If you are just testing your frontend right now, running it on your laptop with the updated code and PM2 is the perfect way to do it!
