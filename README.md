# AI-Abtahi
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Abtahi — AI Assistant</title>
  <style>
    body { font-family: sans-serif; margin:0; padding:0; display:flex; height:100vh; }
    .chat { margin:auto; width:800px; height:90vh; border:1px solid #ddd; border-radius:10px; display:flex; flex-direction:column; }
    .messages { flex:1; padding:10px; overflow-y:auto; background:#fafafa; }
    .msg { margin:8px 0; }
    .msg.user { text-align:right; color:#333; }
    .msg.abtahi { text-align:left; color:#005; }
    .input { display:flex; padding:10px; border-top:1px solid #ddd; gap:8px; }
    input[type=text] { flex:1; padding:8px; }
  </style>
</head>
<body>
  <div class="chat">
    <div id="messages" class="messages"></div>
    <div class="input">
      <input id="prompt" type="text" placeholder="Ask Abtahi anything..." />
      <label><input type="checkbox" id="searchToggle"> Google</label>
      <button id="sendBtn">Send</button>
    </div>
  </div>

<script>
const userId = "user1"; // simple single-user memory

function appendMsg(who, text) {
  const div = document.createElement("div");
  div.className = "msg " + who;
  div.textContent = who.toUpperCase() + ": " + text;
  document.getElementById("messages").appendChild(div);
  document.getElementById("messages").scrollTop = document.getElementById("messages").scrollHeight;
}

document.getElementById("sendBtn").onclick = async () => {
  const text = document.getElementById("prompt").value;
  const useSearch = document.getElementById("searchToggle").checked;
  if (!text) return;
  appendMsg("user", text);
  document.getElementById("prompt").value = "";

  appendMsg("abtahi", "Typing...");
  const res = await fetch("/api/chat", {
    method: "POST",
    headers: {"Content-Type":"application/json"},
    body: JSON.stringify({ userId, message: text, useSearch })
  });
  const data = await res.json();

  const msgs = document.getElementById("messages");
  msgs.removeChild(msgs.lastChild); // remove typing
  appendMsg("abtahi", data.reply.content);
};
</script>
</body>
</html>
{
  "name": "abtahi-ai",
  "version": "1.0.0",
  "main": "server.js",
  "type": "commonjs",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "dotenv": "^16.0.0",
    "express": "^4.18.0",
    "node-fetch": "^2.6.7"
  }
}
OPENAI_API_KEY=sk-xxxx
GOOGLE_API_KEY=AIza-your-key
GOOGLE_CX=your_cx_id
