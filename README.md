💻 1. LAPTOP SETUP (Local Development)
Use this when running the server on your personal computer.
File: server.js
```
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
How to run it on your Laptop:
1-Open terminal and install dependencies: npm install express cors localtunnel
2-Run the server: node server.js
3-Copy the https://xxx.loca.lt URL printed in the console and paste it into your frontend HTML/JS file.
