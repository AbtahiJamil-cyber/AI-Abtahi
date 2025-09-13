<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Abtahi AI - Your Intelligent Assistant</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #0052D4 0%, #4364F7 50%, #6FB1FC 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .container {
            width: 100%;
            max-width: 900px;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.2);
            overflow: hidden;
            display: flex;
            flex-direction: column;
            height: 90vh;
        }
        
        .header {
            background: linear-gradient(90deg, #0077b6 0%, #03045e 100%);
            color: white;
            padding: 20px;
            text-align: center;
            position: relative;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .header-content {
            display: flex;
            align-items: center;
            gap: 15px;
        }
        
        .flag {
            font-size: 2.5rem;
            animation: wave 3s infinite;
        }
        
        @keyframes wave {
            0%, 100% { transform: rotate(0deg); }
            25% { transform: rotate(5deg); }
            75% { transform: rotate(-5deg); }
        }
        
        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 5px;
        }
        
        .header p {
            opacity: 0.9;
            font-size: 1.1rem;
        }
        
        .chat-container {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect width="100" height="100" fill="%23f8f9fa"/><path d="M0 0L100 100" stroke="%23e9ecef" stroke-width="1"/><path d="M100 0L0 100" stroke="%23e9ecef" stroke-width="1"/></svg>');
        }
        
        .message {
            max-width: 75%;
            padding: 15px 20px;
            border-radius: 20px;
            line-height: 1.5;
            position: relative;
            animation: fadeIn 0.3s ease;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .user-message {
            background: #0077b6;
            color: white;
            align-self: flex-end;
            border-bottom-right-radius: 5px;
        }
        
        .ai-message {
            background: #f1f1f1;
            color: #333;
            align-self: flex-start;
            border-bottom-left-radius: 5px;
        }
        
        .ai-message pre {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 12px;
            border-radius: 8px;
            overflow-x: auto;
            margin-top: 10px;
            font-family: 'Courier New', monospace;
            tab-size: 4;
        }
        
        .ai-message code {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 2px 6px;
            border-radius: 4px;
            font-family: 'Courier New', monospace;
        }
        
        .input-container {
            display: flex;
            padding: 15px;
            background: white;
            border-top: 1px solid #eee;
            gap: 10px;
        }
        
        input {
            flex: 1;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 30px;
            outline: none;
            font-size: 16px;
            transition: border-color 0.3s;
        }
        
        input:focus {
            border-color: #0077b6;
        }
        
        button {
            background: #0077b6;
            color: white;
            border: none;
            border-radius: 30px;
            padding: 0 25px;
            cursor: pointer;
            transition: background 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            font-weight: 600;
        }
        
        button:hover {
            background: #03045e;
        }
        
        .typing-indicator {
            display: none;
            align-self: flex-start;
            background: #f1f1f1;
            color: #333;
            padding: 12px 20px;
            border-radius: 20px;
            border-bottom-left-radius: 5px;
        }
        
        .typing-indicator span {
            height: 8px;
            width: 8px;
            float: left;
            margin: 0 3px;
            background-color: #9E9EA1;
            display: block;
            border-radius: 50%;
            opacity: 0.4;
        }
        
        .typing-indicator span:nth-of-type(1) {
            animation: typing 1s infinite;
        }
        
        .typing-indicator span:nth-of-type(2) {
            animation: typing 1s 0.33s infinite;
        }
        
        .typing-indicator span:nth-of-type(3) {
            animation: typing 1s 0.66s infinite;
        }
        
        @keyframes typing {
            0%, 100% {
                transform: translateY(0);
                opacity: 0.4;
            }
            50% {
                transform: translateY(-5px);
                opacity: 1;
            }
        }
        
        .suggestion-chips {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            padding: 10px 15px;
            background: #f8f9fa;
            border-top: 1px solid #e9ecef;
        }
        
        .chip {
            background: #e9ecef;
            color: #495057;
            padding: 8px 16px;
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 14px;
        }
        
        .chip:hover {
            background: #0077b6;
            color: white;
        }
        
        @media (max-width: 768px) {
            .container {
                height: 95vh;
                border-radius: 15px;
            }
            
            .message {
                max-width: 85%;
            }
            
            .header h1 {
                font-size: 2rem;
            }
            
            .header p {
                font-size: 1rem;
            }
            
            .flag {
                font-size: 2rem;
            }
        }
        
        .info-box {
            background: #e3f2fd;
            border-left: 4px solid #2196f3;
            padding: 10px 15px;
            margin: 10px 0;
            border-radius: 4px;
        }
        
        .story-box {
            background: #fff3e0;
            border-left: 4px solid #ff9800;
            padding: 15px;
            margin: 10px 0;
            border-radius: 4px;
            font-style: italic;
        }
        
        .search-result {
            background: #e8f5e9;
            border-left: 4px solid #4caf50;
            padding: 12px 15px;
            margin: 10px 0;
            border-radius: 4px;
        }
        
        .search-result a {
            color: #0077b6;
            text-decoration: none;
        }
        
        .search-result a:hover {
            text-decoration: underline;
        }
        
        .document-box {
            background: #fce4ec;
            border-left: 4px solid #ec407a;
            padding: 12px 15px;
            margin: 10px 0;
            border-radius: 4px;
        }
        
        .document-box a {
            color: #0077b6;
            text-decoration: none;
            font-weight: bold;
        }
        
        .tools-container {
            display: flex;
            padding: 10px 15px;
            background: #e9ecef;
            border-top: 1px solid #ddd;
            gap: 10px;
            justify-content: center;
        }
        
        .tool-btn {
            background: #6c757d;
            color: white;
            border: none;
            border-radius: 20px;
            padding: 8px 15px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 5px;
            font-size: 14px;
        }
        
        .tool-btn:hover {
            background: #5a6268;
        }
        
        .tool-btn.active {
            background: #0077b6;
        }
        
        .api-key-container {
            padding: 10px 15px;
            background: #fff3cd;
            border-top: 1px solid #ffeaa7;
            display: flex;
            gap: 10px;
            align-items: center;
        }
        
        .api-key-input {
            flex: 1;
            padding: 8px 12px;
            border: 1px solid #ffeaa7;
            border-radius: 4px;
            font-size: 14px;
        }
        
        .save-api-btn {
            background: #ffc107;
            color: #000;
            border: none;
            border-radius: 4px;
            padding: 8px 12px;
            cursor: pointer;
            font-size: 14px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <div class="header-content">
                <div class="flag">🇧🇩</div>
                <div>
                    <h1>Abtahi AI</h1>
                    <p>Your intelligent Bangladeshi assistant that can answer anything</p>
                </div>
            </div>
        </div>
        
        <div class="api-key-container" id="apiKeyContainer">
            <input type="password" class="api-key-input" id="apiKeyInput" placeholder="Enter Google Search API Key (optional)">
            <button class="save-api-btn" id="saveApiBtn">Save Key</button>
        </div>
        
        <div class="chat-container" id="chatContainer">
            <div class="message ai-message">
                <p>Hello! I'm Abtahi, your AI assistant from Bangladesh. 🇧🇩</p>
                <p>I can answer questions, generate code, write stories, find information using Google search, and help with documents. How can I help you today?</p>
                <p>For the best experience with web searches, you can optionally add a Google Search API key above.</p>
            </div>
            <div class="typing-indicator" id="typingIndicator">
                <span></span>
                <span></span>
                <span></span>
            </div>
        </div>
        
        <div class="suggestion-chips">
            <div class="chip" data-prompt="Generate a Python calculator">Python Calculator</div>
            <div class="chip" data-prompt="Tell me a story about space">Space Story</div>
            <div class="chip" data-prompt="Search for latest AI developments">AI Developments</div>
            <div class="chip" data-prompt="Generate a responsive navbar">HTML Navbar</div>
            <div class="chip" data-prompt="Find research papers on machine learning">Research Papers</div>
        </div>
        
        <div class="tools-container">
            <button class="tool-btn" data-action="clear-chat"><i class="fas fa-broom"></i> Clear Chat</button>
            <button class="tool-btn" data-action="export-chat"><i class="fas fa-download"></i> Export Chat</button>
            <button class="tool-btn" data-action="change-theme"><i class="fas fa-palette"></i> Change Theme</button>
        </div>
        
        <div class="input-container">
            <input type="text" id="userInput" placeholder="Ask me anything or request code..." autocomplete="off">
            <button id="sendButton"><i class="fas fa-paper-plane"></i> Send</button>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const chatContainer = document.getElementById('chatContainer');
            const userInput = document.getElementById('userInput');
            const sendButton = document.getElementById('sendButton');
            const typingIndicator = document.getElementById('typingIndicator');
            const chips = document.querySelectorAll('.chip');
            const toolButtons = document.querySelectorAll('.tool-btn');
            const apiKeyInput = document.getElementById('apiKeyInput');
            const saveApiBtn = document.getElementById('saveApiBtn');
            const apiKeyContainer = document.getElementById('apiKeyContainer');
            
            // Check if API key is already saved
            const savedApiKey = localStorage.getItem('googleApiKey');
            if (savedApiKey) {
                apiKeyInput.value = savedApiKey;
                apiKeyContainer.style.display = 'none';
            }
            
            // Focus on input field
            userInput.focus();
            
            // Save API key
            saveApiBtn.addEventListener('click', function() {
                const apiKey = apiKeyInput.value.trim();
                if (apiKey) {
                    localStorage.setItem('googleApiKey', apiKey);
                    apiKeyContainer.style.display = 'none';
                    addMessage("Google API key saved successfully! You can now use web search features.", false);
                }
            });
            
            // Responses database
            const responses = {
                greetings: [
                    "Hello! I'm Abtahi, your AI assistant from Bangladesh. How can I help you today? 🇧🇩",
                    "Hi there! Abtahi here, from the beautiful country of Bangladesh. What can I do for you?",
                    "Greetings! I'm Abtahi, ready to assist you. I'm proud to be a Bangladeshi AI!"
                ],
                farewell: [
                    "Goodbye! Feel free to return if you have more questions.",
                    "See you later! Don't hesitate to ask if you need more help.",
                    "Farewell! Remember, I'm here whenever you need assistance."
                ],
                thanks: [
                    "You're welcome! Happy to help.",
                    "Anytime! That's what I'm here for.",
                    "Glad I could assist you!"
                ],
                coding: [
                    "I'd be happy to help with code. What language are you working with?",
                    "Sure, I can generate code for you. What do you need?",
                    "Programming assistance coming right up. What's your requirement?"
                ],
                bangladesh: [
                    "I'm from Bangladesh, a beautiful country in South Asia known for its rich culture, history, and the mighty rivers that flow through it!",
                    "As a Bangladeshi AI, I'm proud to represent my country which is known for its vibrant culture, delicious cuisine, and the world's largest river delta.",
                    "Bangladesh is my home country! It's famous for its lush green landscapes, the Sundarbans mangrove forest, and of course, our passion for cricket."
                ]
            };
            
            // Code templates
            const codeTemplates = {
                python: {
                    hello: "print('Hello, World!')",
                    loop: "for i in range(10):\n    print(i)",
                    function: "def example_function():\n    return 'This is an example'",
                    calculator: "def add(a, b):\n    return a + b\n\ndef subtract(a, b):\n    return a - b\n\ndef multiply(a, b):\n    return a * b\n\ndef divide(a, b):\n    if b != 0:\n        return a / b\n    else:\n        return 'Cannot divide by zero'\n\n# Example usage\nresult = add(5, 3)\nprint(f'5 + 3 = {result}')",
                    web_scraper: "import requests\nfrom bs4 import BeautifulSoup\n\n# Fetch webpage content\nurl = 'https://example.com'\nresponse = requests.get(url)\n\n# Parse HTML content\nsoup = BeautifulSoup(response.text, 'html.parser')\n\n# Extract all links\nlinks = soup.find_all('a')\nfor link in links:\n    print(link.get('href'))"
                },
                javascript: {
                    hello: "console.log('Hello, World!');",
                    loop: "for(let i = 0; i < 10; i++) {\n    console.log(i);\n}",
                    function: "function exampleFunction() {\n    return 'This is an example';\n}",
                    calculator: "function add(a, b) {\
