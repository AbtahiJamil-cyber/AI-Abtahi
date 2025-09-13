{
  "name": "abtahi-ai",
  "version": "1.0.0",
  "description": "Abtahi — AI assistant (OpenAI + Google search). Push to GitHub and deploy to Render/Heroku/Vercel.",
  "main": "server.js",
  "type": "commonjs",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "axios": "^1.4.0",
    "cors": "^2.8.5",
    "dotenv": "^16.0.3",
    "express": "^4.18.2",
    "helmet": "^7.0.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.22"
  }
}
# Copy to .env and fill your values (DO NOT COMMIT real keys to git)
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
GOOGLE_API_KEY=AIza...
GOOGLE_CX=0123456789:abcdefghijkl   # Programmable Search Engine ID
PORT=3000
# Optional enhancements:
# REDIS_URL=redis://:password@hostname:6379/0
// server.js
require('dotenv').config();
const express = require('express');
const axios = require('axios');
const path = require('path');
const cors = require('cors');
const helmet = require('helmet');

const app = express();
app.use(helmet());
app.use(cors());
app.use(express.json());
app.use(express.static(path.join(__dirname, 'public')));

const OPENAI_KEY = process.env.OPENAI_API_KEY;
const GOOGLE_KEY = process.env.GOOGLE_API_KEY;
const GOOGLE_CX = process.env.GOOGLE_CX;
const PORT = process.env.PORT || 3000;

if (!OPENAI_KEY) {
  console.error('ERROR: OPENAI_API_KEY not set in environment.');
  // don't exit so repo remains viewable; but endpoints will error politely
}

// Simple in-memory memory store per userId (for demo). Replace with DB for prod.
const memoryStore = {}; // { userId: [{role, content}, ...] }

const SYSTEM_PROMPT = `
You are Abtahi, an AI assistant just like ChatGPT.
- Speak in user's language when possible.
- You can generate runnable code snippets in many programming languages.
- If provided Google results, incorporate them and cite links.
- If asked for something illegal or unsafe, refuse and offer safe alternatives.
- Keep answers clear and include code in triple-backtick fenced blocks.
`;

/** Helper: call OpenAI (Chat Completions / Chat API) */
async function callOpenAI(messages, options = {}) {
  if (!OPENAI_KEY) throw new Error('OpenAI API key not configured');

  const body = {
    model: options.model || 'gpt-4o-mini', // choose suitable model available to you
    messages,
    temperature: typeof options.temperature === 'number' ? options.temperature : 0.2,
    max_tokens: options.max_tokens || 1200
  };

  const resp = await axios.post('https://api.openai.com/v1/chat/completions', body, {
    headers: {
      Authorization: `Bearer ${OPENAI_KEY}`,
      'Content-Type': 'application/json'
    },
    timeout: 120000
  });

  // Basic validation
  if (!resp.data || !resp.data.choices || !resp.data.choices[0]) {
    throw new Error('Invalid response from OpenAI');
  }

  return resp.data.choices[0].message; // { role, content }
}

/** Helper: Google Custom Search (Programmable Search) */
async function googleSearch(query) {
  if (!GOOGLE_KEY || !GOOGLE_CX) return { error: 'Google not configured' };
  const url = 'https://www.googleapis.com/customsearch/v1';
  const resp = await axios.get(url, {
    params: { q: query, key: GOOGLE_KEY, cx: GOOGLE_CX, num: 5 }
  });
  const items = resp.data.items || [];
  // Map to simplified results
  return items.map(it => ({ title: it.title, snippet: it.snippet, link: it.link }));
}

/** Ensure memory for user */
function ensureMemory(userId) {
  if (!memoryStore[userId]) {
    memoryStore[userId] = [{ role: 'system', content: SYSTEM_PROMPT }];
  }
}

/** API: health */
app.get('/api/health', (req, res) => res.send({ ok: true }));

/** API: search (returns structured google search results) */
app.get('/api/search', async (req, res) => {
  const q = req.query.q;
  if (!q) return res.status(400).send({ error: 'q query param required' });
  try {
    const results = await googleSearch(q);
    res.send({ results });
  } catch (err) {
    console.error(err);
    res.status(500).send({ error: 'Search failed', details: err.message });
  }
});

/**
 * API: /api/chat
 * body: { userId: string (optional), message: string, useGoogle: boolean (optional) }
 */
app.post('/api/chat', async (req, res) => {
  try {
    const { userId = 'guest', message, useGoogle = false } = req.body;
    if (!message) return res.status(400).send({ error: 'message is required' });

    ensureMemory(userId);
    // push user message
    memoryStore[userId].push({ role: 'user', content: message });

    // Optionally fetch google results and append as system info
    if (useGoogle && GOOGLE_KEY && GOOGLE_CX) {
      try {
        const g = await googleSearch(message);
        if (Array.isArray(g) && g.length > 0) {
          const aggregated = g.map((r, i) => `${i+1}. ${r.title}\n${r.snippet}\n${r.link}`).join('\n\n');
          memoryStore[userId].push({ role: 'system', content: `Google search results for: "${message}"\n\n${aggregated}` });
        } else {
          memoryStore[userId].push({ role: 'system', content: `Google search found no relevant results for: "${message}"` });
        }
      } catch (err) {
        console.warn('Google search error:', err.message);
        memoryStore[userId].push({ role: 'system', content: `Google search failed: ${err.message}` });
      }
    }

    // Call OpenAI
    const assistantMsg = await callOpenAI(memoryStore[userId], { temperature: 0.2, max_tokens: 1200 });

    // Save assistant reply to memory
    memoryStore[userId].push(assistantMsg);

    // Return the assistant message
    res.send({ reply: assistantMsg });

  } catch (err) {
    console.error('Chat error:', err.message || err);
    res.status(500).send({ error: err.message || 'Server error' });
  }
});

/** Optional: clear memory for a user (for testing) */
app.post('/api/clear', (req, res) => {
  const userId = req.body.userId || 'guest';
  memoryStore[userId] = [{ role: 'system', content: SYSTEM_PROMPT }];
  res.send({ ok: true });
});

/** Fallback to serve index.html */
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.listen(PORT, () => {
  console.log(`🚀 Abtahi running on http://localhost:${PORT} (PORT=${PORT})`);
});
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Abtahi — AI Assistant</title>
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <style>
    :root { --accent:#0b5fff; --muted:#666; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, Arial; }
    body { margin:0; height:100vh; display:flex; align-items:center; justify-content:center; background:#f4f6fb; }
    .app { width:1000px; max-width:96%; height:86vh; background:white; border-radius:12px; box-shadow:0 6px 30px rgba(20,20,50,0.08); display:flex; overflow:hidden; }
    .left { flex:1 1 420px; display:flex; flex-direction:column; }
    .header { padding:16px 20px; background:linear-gradient(90deg,var(--accent),#2d7cff); color:white; font-weight:700; }
    .messages { flex:1; padding:18px; overflow:auto; background:linear-gradient(0deg, rgba(11,95,255,0.03), transparent); }
    .msg { margin-bottom:14px; }
    .msg .who { font-weight:600; margin-bottom:6px; }
    .msg .content { white-space:pre-wrap; background:#fff; padding:10px; border-radius:8px; border:1px solid #eee; }
    .right { width:360px; border-left:1px solid #f0f0f0; padding:12px; display:flex; flex-direction:column; gap:12px; }
    .inputRow { display:flex; gap:10px; padding:12px; border-top:1px solid #eee; align-items:center; }
    textarea { width:100%; min-height:78px; padding:8px; border-radius:8px; border:1px solid #ddd; resize:vertical; }
    button { background:var(--accent); color:white; border:0; padding:8px 12px; border-radius:8px; cursor:pointer; }
    .small { font-size:13px; color:var(--muted); }
    pre.code { background:#0b1220; color:#e6eef8; padding:12px; border-radius:8px; overflow:auto; }
    .row { display:flex; gap:8px; align-items:center; }
    label { font-size:13px; color:var(--muted); }
  </style>
</head>
<body>
  <div class="app">
    <div class="left">
      <div class="header">Abtahi — Your AI (like ChatGPT)</div>
      <div id="messages" class="messages"></div>

      <div class="inputRow">
        <textarea id="prompt" placeholder="Ask Abtahi anything (e.g., 'Write a Node.js server that...')"></textarea>
        <div style="display:flex; flex-direction:column; gap:8px;">
          <button id="sendBtn">Ask</button>
          <button id="clearBtn" style="background:#666">Clear</button>
        </div>
      </div>
    </div>

    <div class="right">
      <div>
        <div class="small">OpenAI key (server-based recommended). For local testing you can paste your key here — but DO NOT publish it.</div>
        <input id="apikey" placeholder="Optional: local OpenAI key (only for GitHub Pages demo)" style="width:100%; padding:8px; border-radius:8px; border:1px solid #ddd;" />
      </div>

      <div class="row">
        <label><input type="checkbox" id="useGoogle"> Use Google Search</label>
        <label style="margin-left:auto" class="small">Model: <b>server-side</b></label>
      </div>

      <div class="small">Actions:</div>
      <div class="row">
        <button id="copyBtn" style="flex:1">Copy Last Code</button>
        <button id="downloadBtn" style="flex:1; background:#28a745">Download Code</button>
      </div>

      <div class="small">Tips: Ask "Write a complete <lang> program ..." or "Make me a <tech> demo". For big code, Abtahi will split into files (pasteable).</div>
    </div>
  </div>

<script>
const apiBase = ''; // blank => same origin (server.js)
const messagesEl = document.getElementById('messages');
const promptEl = document.getElementById('prompt');
const sendBtn = document.getElementById('sendBtn');
const clearBtn = document.getElementById('clearBtn');
const copyBtn = document.getElementById('copyBtn');
const downloadBtn = document.getElementById('downloadBtn');
const useGoogleEl = document.getElementById('useGoogle');
let lastCode = null;

function appendMessage(who, text, isCode=false) {
  const m = document.createElement('div'); m.className='msg';
  const whoEl = document.createElement('div'); whoEl.className='who'; whoEl.textContent = who;
  const content = document.createElement('div'); content.className='content';
  if (isCode) {
    const pre = document.createElement('pre'); pre.className='code'; pre.textContent = text;
    content.appendChild(pre);
    lastCode = text;
  } else {
    content.innerText = text;
  }
  m.appendChild(whoEl); m.appendChild(content);
  messagesEl.appendChild(m);
  messagesEl.scrollTop = messagesEl.scrollHeight;
}

async function askAbtahi(text) {
  appendMessage('You', text);
  appendMessage('Abtahi', 'Thinking...');
  const useGoogle = useGoogleEl.checked;
  try {
    const res = await fetch(apiBase + '/api/chat', {
      method: 'POST', headers: {'Content-Type':'application/json'},
      body: JSON.stringify({ userId: 'user1', message: text, useGoogle })
    });
    const data = await res.json();
    // replace last "Thinking..."
    messagesEl.removeChild(messagesEl.lastChild);
    if (data.error) {
      appendMessage('Abtahi', 'Error: ' + data.error);
      return;
    }
    const content = data.reply && data.reply.content ? data.reply.content : JSON.stringify(data.reply);
    // detect code fences
    if (content.includes('```')) {
      // split at fences
      const parts = content.split(/```/g);
      for (let i=0;i<parts.length;i++){
        if (i%2===0) {
          if (parts[i].trim()) appendMessage('Abtahi', parts[i].trim());
        } else {
          // possible language label at start
          let code = parts[i].replace(/^[a-zA-Z0-9+\-_.#]+\n/, '');
          appendMessage('Abtahi (code)', code, true);
        }
      }
    } else {
      appendMessage('Abtahi', content);
    }
  } catch (err) {
    messagesEl.removeChild(messagesEl.lastChild);
    appendMessage('Abtahi', 'Network/Error: ' + err.message);
  }
}

sendBtn.addEventListener('click', () => {
  const t = promptEl.value.trim();
  if (!t) return;
  askAbtahi(t);
  promptEl.value = '';
});

clearBtn.addEventListener('click', () => {
  messagesEl.innerHTML = '';
  lastCode = null;
});

copyBtn.addEventListener('click', async () => {
  if (!lastCode) return alert('No code to copy yet.');
  await navigator.clipboard.writeText(lastCode);
  alert('Code copied to clipboard ✔');
});

downloadBtn.addEventListener('click', () => {
  if (!lastCode) return alert('No code to download yet.');
  const blob = new Blob([lastCode], { type: 'text/plain' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'abtahi_code.txt'; a.click();
  URL.revokeObjectURL(url);
});
</script>
</body>
</html>
# Abtahi — AI Assistant (OpenAI + Google)

This repo provides a minimal full-stack assistant "Abtahi" you can run and deploy.

**Features**
- Chat UI (single-page)
- Multi-turn memory (in-memory)
- OpenAI Chat completions integration (generate code + answers)
- Optional Google Custom Search integration (programmable search)
- Copy / download generated code

## Quickstart (local)
1. Clone:
```bash
git clone https://github.com/YOURNAME/abtahi-ai.git
cd abtahi-ai
npm install
OPENAI_API_KEY=sk-...
GOOGLE_API_KEY=AIza...
GOOGLE_CX=...
npm start
