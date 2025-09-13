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
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Abtahi AI Assistant</title>
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
        
        <div class="chat-container" id="chatContainer">
            <div class="message ai-message">
                <p>Hello! I'm Abtahi, your AI assistant from Bangladesh. 🇧🇩</p>
                <p>I can answer questions, generate code, write stories, and find information using Google search. How can I help you today?</p>
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
            
            // Focus on input field
            userInput.focus();
            
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
                    calculator: "def add(a, b):\n    return a + b\n\ndef subtract(a, b):\n    return a - b\n\ndef multiply(a, b):\n    return a * b\n\ndef divide(a, b):\n    if b != 0:\n        return a / b\n    else:\n        return 'Cannot divide by zero'\n\n# Example usage\nresult = add(5, 3)\nprint(f'5 + 3 = {result}')"
                },
                javascript: {
                    hello: "console.log('Hello, World!');",
                    loop: "for(let i = 0; i < 10; i++) {\n    console.log(i);\n}",
                    function: "function exampleFunction() {\n    return 'This is an example';\n}",
                    calculator: "function add(a, b) {\n    return a + b;\n}\n\nfunction subtract(a, b) {\n    return a - b;\n}\n\nfunction multiply(a, b) {\n    return a * b;\n}\n\nfunction divide(a, b) {\n    if (b !== 0) {\n        return a / b;\n    } else {\n        return 'Cannot divide by zero';\n    }\n}\n\n// Example usage\nconst result = add(5, 3);\nconsole.log(`5 + 3 = ${result}`);"
                },
                html: {
                    basic: "<!DOCTYPE html>\n<html>\n<head>\n    <title>My Page</title>\n</head>\n<body>\n    <h1>Hello World</h1>\n    <p>Welcome to my website!</p>\n</body>\n</html>",
                    form: "<!DOCTYPE html>\n<html>\n<head>\n    <title>Form Example</title>\n    <style>\n        body { font-family: Arial, sans-serif; }\n        form { max-width: 400px; margin: 20px auto; }\n        input, textarea { width: 100%; padding: 8px; margin: 5px 0; }\n    </style>\n</head>\n<body>\n    <form>\n        <label for='name'>Name:</label>\n        <input type='text' id='name' name='name'>\n        \n        <label for='email'>Email:</label>\n        <input type='email' id='email' name='email'>\n        \n        <label for='message'>Message:</label>\n        <textarea id='message' name='message'></textarea>\n        \n        <input type='submit' value='Submit'>\n    </form>\n</body>\n</html>",
                    navbar: "<!DOCTYPE html>\n<html>\n<head>\n    <title>Navigation Bar</title>\n    <style>\n        nav {\n            background-color: #333;\n            overflow: hidden;\n        }\n        \n        nav a {\n            float: left;\n            display: block;\n            color: white;\n            text-align: center;\n            padding: 14px 16px;\n            text-decoration: none;\n        }\n        \n        nav a:hover {\n            background-color: #ddd;\n            color: black;\n        }\n        \n        @media screen and (max-width: 600px) {\n            nav a {\n                float: none;\n                width: 100%;\n            }\n        }\n    </style>\n</head>\n<body>\n    <nav>\n        <a href='#home'>Home</a>\n        <a href='#news'>News</a>\n        <a href='#contact'>Contact</a>\n        <a href='#about'>About</a>\n    </nav>\n</body>\n</html>"
                },
                css: {
                    basic: "body {\n    font-family: Arial, sans-serif;\n    margin: 0;\n    padding: 0;\n    background-color: #f4f4f4;\n    line-height: 1.6;\n}\n\n.container {\n    width: 80%;\n    margin: auto;\n    overflow: hidden;\n}",
                    button: ".btn {\n    background-color: #4CAF50;\n    border: none;\n    color: white;\n    padding: 15px 32px;\n    text-align: center;\n    text-decoration: none;\n    display: inline-block;\n    font-size: 16px;\n    margin: 4px 2px;\n    cursor: pointer;\n    border-radius: 4px;\n    transition: background-color 0.3s;\n}\n\n.btn:hover {\n    background-color: #45a049;\n}"
                }
            };
            
            // Story templates
            const storyTemplates = {
                space: "In the year 2150, humanity had finally achieved what seemed impossible just a century before - a thriving colony on Mars. Captain Ayesha Rahman, a brilliant astrophysicist from Bangladesh, was leading the mission to explore the mysterious valleys of Valles Marineris. What her team discovered there would change our understanding of life in the universe forever...",
                adventure: "Deep in the Amazon rainforest, a team of explorers discovered an ancient temple hidden for millennia. Inside, they found not gold or jewels, but something far more valuable - knowledge that could solve the world's energy crisis. But they weren't alone in their search...",
                fantasy: "In the mystical land of Eldoria, where magic flowed like rivers, a young apprentice named Kael discovered he had a unique ability - he could communicate with dragons, creatures thought to be extinct for centuries. This gift would lead him on an epic journey to restore balance to the world...",
                scifi: "On a distant planet orbiting a red dwarf star, Dr. Chen's research team made an incredible breakthrough. They developed a device that could manipulate time on a small scale. But when corporate interests tried to weaponize their invention, the team had to make a difficult choice..."
            };
            
            // Search results templates (simulated Google search)
            const searchResults = {
                ai: {
                    query: "latest AI developments",
                    results: [
                        {
                            title: "Breakthrough in Quantum Machine Learning",
                            url: "https://example.com/quantum-ai",
                            snippet: "Researchers have developed a new quantum algorithm that dramatically accelerates machine learning processes, potentially revolutionizing AI capabilities."
                        },
                        {
                            title: "GPT-5: What to Expect",
                            url: "https://example.com/gpt5-preview",
                            snippet: "Industry insiders suggest GPT-5 will feature significantly improved reasoning capabilities and reduced hallucinations, with a planned release next year."
                        },
                        {
                            title: "AI in Healthcare: New Diagnostic Tools",
                            url: "https://example.com/ai-healthcare",
                            snippet: "New AI systems can now detect early signs of diseases from medical imaging with accuracy surpassing human experts in several domains."
                        }
                    ]
                },
                bangladesh: {
                    query: "Bangladesh technology growth",
                    results: [
                        {
                            title: "Bangladesh's Digital Transformation",
                            url: "https://example.com/bd-digital",
                            snippet: "Bangladesh has seen remarkable growth in its technology sector, with digital exports increasing by 40% in the past year alone."
                        },
                        {
                            title: "Startup Ecosystem in Dhaka",
                            url: "https://example.com/dhaka-startups",
                            snippet: "Dhaka's startup scene is booming, with several tech startups reaching unicorn status and attracting international investment."
                        }
                    ]
                },
                programming: {
                    query: "best programming languages to learn 2024",
                    results: [
                        {
                            title: "Top 10 Programming Languages for 2024",
                            url: "https://example.com/top-languages",
                            snippet: "Python continues to dominate, but Rust and TypeScript are seeing the fastest growth in demand according to industry surveys."
                        },
                        {
                            title: "The Rise of WebAssembly",
                            url: "https://example.com/webassembly-rise",
                            snippet: "WebAssembly is becoming increasingly important for high-performance web applications, with all major browsers now offering full support."
                        }
                    ]
                }
            };
            
            // Add message to chat
            function addMessage(text, isUser = false) {
                const messageDiv = document.createElement('div');
                messageDiv.classList.add('message');
                messageDiv.classList.add(isUser ? 'user-message' : 'ai-message');
                
                // Check if the response contains code
                if (!isUser && text.includes('```')) {
                    const parts = text.split('```');
                    let formattedText = '';
                    
                    for (let i = 0; i < parts.length; i++) {
                        if (i % 2 === 0) {
                            formattedText += parts[i];
                        } else {
                            formattedText += `<pre>${parts[i]}</pre>`;
                        }
                    }
                    
                    messageDiv.innerHTML = formattedText;
                } else {
                    messageDiv.innerHTML = text;
                }
                
                chatContainer.insertBefore(messageDiv, typingIndicator);
                chatContainer.scrollTop = chatContainer.scrollHeight;
            }
            
            // Show typing indicator
            function showTypingIndicator() {
                typingIndicator.style.display = 'block';
                chatContainer.scrollTop = chatContainer.scrollHeight;
            }
            
            // Hide typing indicator
            function hideTypingIndicator() {
                typingIndicator.style.display = 'none';
            }
            
            // Get AI response
            function getAIResponse(input) {
                input = input.toLowerCase();
                
                // Check for greetings
                if (input.includes('hello') || input.includes('hi') || input.includes('hey')) {
                    return responses.greetings[Math.floor(Math.random() * responses.greetings.length)];
                }
                
                // Check for farewells
                if (input.includes('bye') || input.includes('goodbye') || input.includes('see you')) {
                    return responses.farewell[Math.floor(Math.random() * responses.farewell.length)];
                }
                
                // Check for thanks
                if (input.includes('thank') || input.includes('thanks') || input.includes('appreciate')) {
                    return responses.thanks[Math.floor(Math.random() * responses.thanks.length)];
                }
                
                // Check for Bangladesh related questions
                if (input.includes('bangladesh') || input.includes('bd ') || input.includes('bd.') || 
                    input.includes('dhaka') || input.includes('where are you from')) {
                    let response = responses.bangladesh[Math.floor(Math.random() * responses.bangladesh.length)];
                    
                    if (input.includes('capital')) {
                        response += "<div class='info-box'><strong>Capital:</strong> Dhaka</div>";
                    }
                    
                    if (input.includes('population')) {
                        response += "<div class='info-box'><strong>Population:</strong> Approximately 170 million</div>";
                    }
                    
                    if (input.includes('language')) {
                        response += "<div class='info-box'><strong>Language:</strong> Bengali (Bangla)</div>";
                    }
                    
                    return response;
                }
                
                // Check for coding requests
                if (input.includes('code') || input.includes('program') || input.includes('script') || input.includes('generate')) {
                    let response = responses.coding[Math.floor(Math.random() * responses.coding.length)];
                    
                    // Check for specific languages and patterns
                    if (input.includes('python')) {
                        if (input.includes('hello')) {
                            return response + "<br><br>Here's a Python hello world:<br><br>```" + codeTemplates.python.hello + "```";
                        } else if (input.includes('loop')) {
                            return response + "<br><br>Here's a Python loop:<br><br>```" + codeTemplates.python.loop + "```";
                        } else if (input.includes('function')) {
                            return response + "<br><br>Here's a Python function:<br><br>```" + codeTemplates.python.function + "```";
                        } else if (input.includes('calculator')) {
                            return response + "<br><br>Here's a simple Python calculator:<br><br>```" + codeTemplates.python.calculator + "```";
                        } else {
                            return response + "<br><br>I can generate Python code for: hello, loop, function, calculator";
                        }
                    } else if (input.includes('javascript') || input.includes('js')) {
                        if (input.includes('hello')) {
                            return response + "<br><br>Here's a JavaScript hello world:<br><br>```" + codeTemplates.javascript.hello + "```";
                        } else if (input.includes('loop')) {
                            return response + "<br><br>Here's a JavaScript loop:<br><br>```" + codeTemplates.javascript.loop + "```";
                        } else if (input.includes('function')) {
                            return response + "<br><br>Here's a JavaScript function:<br><br>```" + codeTemplates.javascript.function + "```";
                        } else if (input.includes('calculator')) {
                            return response + "<br><br>Here's a simple JavaScript calculator:<br><br>```" + codeTemplates.javascript.calculator + "```";
                        } else {
                            return response + "<br><br>I can generate JavaScript code for: hello, loop, function, calculator";
                        }
                    } else if (input.includes('html')) {
                        if (input.includes('form')) {
                            return response + "<br><br>Here's an HTML form:<br><br>```" + codeTemplates.html.form + "```";
                        } else if (input.includes('nav') || input.includes('navbar')) {
                            return response + "<br><br>Here's a responsive HTML navbar:<br><br>```" + codeTemplates.html.navbar + "```";
                        } else {
                            return response + "<br><br>Here's a basic HTML template:<br><br>```" + codeTemplates.html.basic + "```";
                        }
                    } else if (input.includes('css')) {
                        if (input.includes('button')) {
                            return response + "<br><br>Here's some CSS for a button:<br><br>```" + codeTemplates.css.button + "```";
                        } else {
                            return response + "<br><br>Here's some basic CSS:<br><br>```" + codeTemplates.css.basic + "```";
                        }
                    } else {
                        return response + "<br><br>I can generate code in Python, JavaScript, HTML, and CSS. Just specify what you need!";
                    }
                }
                
                // Check for story requests
                if (input.includes('story') || input.includes('tell me a') || input.includes('write a')) {
                    if (input.includes('space') || input.includes('scifi') || input.includes('science')) {
                        return "<div class='story-box'>" + storyTemplates.space + "</div>";
                    } else if (input.includes('adventure')) {
                        return "<div class='story-box'>" + storyTemplates.adventure + "</div>";
                    } else if (input.includes('fantasy')) {
                        return "<div class='story-box'>" + storyTemplates.fantasy + "</div>";
                    } else {
                        return "<div class='story-box'>" + storyTemplates.scifi + "</div>";
                    }
                }
                
                // Check for search requests
                if (input.includes('search') || input.includes('find') || input.includes('google') || input.includes('look up')) {
                    let searchQuery = "";
                    
                    if (input.includes('ai') || input.includes('artificial intelligence')) {
                        searchQuery = "ai";
                    } else if (input.includes('bangladesh')) {
                        searchQuery = "bangladesh";
                    } else if (input.includes('programming') || input.includes('code') || input.includes('language')) {
                        searchQuery = "programming";
                    } else {
                        // Default search result
                        return "I can help you search for information about AI, Bangladesh, or programming. Try asking 'Search for latest AI developments' or 'Find information about Bangladesh'.";
                    }
                    
                    const results = searchResults[searchQuery];
                    let response = `<div class='search-result'>I found these results for '${results.query}':</div>`;
                    
                    results.results.forEach((result, index) => {
                        response += `<div class='search-result'><strong>${index + 1}. <a href="${result.url}" target="_blank">${result.title}</a></strong><br>${result.snippet}</div>`;
                    });
                    
                    return response;
                }
                
                // Check for name
                if (input.includes('name') && input.includes('my')) {
                    const name = input.replace(/.*name( is)?/, '').trim();
                    if (name) {
                        return `Nice to meet you, ${name}! I'm Abtahi, your AI assistant from Bangladesh. How can I help you today?`;
                    }
                }
                
                // Default responses for other queries
                const defaultResponses = [
                    "I'm designed to assist with various topics. Could you elaborate?",
                    "I'm Abtahi, an AI assistant from Bangladesh. I can help answer questions, generate code, write stories, and search for information!",
                    "I'm not sure about that, but I'm happy to help with coding questions, stories, or finding information!",
                    "Let me process that request... In the meantime, would you like me to generate some code, tell a story, or search for information?",
                    "I'm constantly evolving. Maybe ask me something about programming, storytelling, or search for information?",
                    "That's an interesting question. As an AI focused on multiple capabilities, I might not have all the answers but I can certainly help with programming, stories, or finding information!",
                    "I'm still learning about that topic. Would you like me to generate some code, tell a story, or search for information instead?"
                ];
                
                return defaultResponses[Math.floor(Math.random() * defaultResponses.length)];
            }
            
            // Handle user message
            function handleUserMessage() {
                const message = userInput.value.trim();
                if (message === '') return;
                
                addMessage(message, true);
                userInput.value = '';
                
                showTypingIndicator();
                
                // Simulate thinking time
                setTimeout(() => {
                    hideTypingIndicator();
                    const response = getAIResponse(message);
                    addMessage(response);
                }, 1000 + Math.random() * 1000);
            }
            
            // Event listeners
            sendButton.addEventListener('click', handleUserMessage);
            userInput.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    handleUserMessage();
                }
            });
            
            // Add chip functionality
            chips.forEach(chip => {
                chip.addEventListener('click', function() {
                    userInput.value = this.getAttribute('data-prompt');
                    handleUserMessage();
                });
            });
        });
    </script>
</body>
</html>
