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
                    calculator: "function add(a, b) {\n    return a + b;\n}\n\nfunction subtract(a, b) {\n    return a - b;\n}\n\nfunction multiply(a, b) {\n    return a * b;\n}\n\nfunction divide(a, b) {\n    if (b !== 0) {\n        return a / b;\n    } else {\n        return 'Cannot divide by zero';\n    }\n}\n\n// Example usage\nconst result = add(5, 3);\nconsole.log(`5 + 3 = ${result}`);",
                    weather_app: "async function getWeather(city) {\n    const apiKey = 'YOUR_API_KEY';\n    const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`;\n    \n    try {\n        const response = await fetch(url);\n        const data = await response.json();\n        console.log(`Temperature in ${city}: ${data.main.temp}°C`);\n    } catch (error) {\n        console.error('Error fetching weather data:', error);\n    }\n}\n\ngetWeather('Dhaka');"
                },
                html: {
                    basic: "<!DOCTYPE html>\n<html>\n<head>\n    <title>My Page</title>\n</head>\n<body>\n    <h1>Hello World</h1>\n    <p>Welcome to my website!</p>\n</body>\n</html>",
                    form: "<!DOCTYPE html>\n<html>\n<head>\n    <title>Form Example</title>\n    <style>\n        body { font-family: Arial, sans-serif; }\n        form { max-width: 400px; margin: 20px auto; }\n        input, textarea { width: 100%; padding: 8px; margin: 5px 0; }\n    </style>\n</head>\n<body>\n    <form>\n        <label for='name'>Name:</label>\n        <input type='text' id='name' name='name'>\n        \n        <label for='email'>Email:</label>\n        <input type='email' id='email' name='email'>\n        \n        <label for='message'>Message:</label>\n        <textarea id='message' name='message'></textarea>\n        \n        <input type='submit' value='Submit'>\n    </form>\n</body>\n</html>",
                    navbar: "<!DOCTYPE html>\n<html>\n<head>\n    <title>Navigation Bar</title>\n    <style>\n        nav {\n            background-color: #333;\n            overflow: hidden;\n        }\n        \n        nav a {\n            float: left;\n            display: block;\n            color: white;\n            text-align: center;\n            padding: 14px 16px;\n            text-decoration: none;\n        }\n        \n        nav a:hover {\n            background-color: #ddd;\n            color: black;\n        }\n        \n        @media screen and (max-width: 600px) {\n            nav a {\n                float: none;\n                width: 100%;\n            }\n        }\n    </style>\n</head>\n<body>\n    <nav>\n        <a href='#home'>Home</a>\n        <a href='#news'>News</a>\n        <a href='#contact'>Contact</a>\n        <a href='#about'>About</a>\n    </nav>\n</body>\n</html>",
                    portfolio: "<!DOCTYPE html>\n<html>\n<head>\n    <title>My Portfolio</title>\n    <style>\n        body { font-family: Arial, sans-serif; margin: 0; padding: 0; }\n        .header { background: #0077b6; color: white; padding: 20px; text-align: center; }\n        .projects { display: flex; flex-wrap: wrap; justify-content: center; padding: 20px; }\n        .project { background: #f1f1f1; margin: 10px; padding: 15px; border-radius: 5px; width: 300px; }\n    </style>\n</head>\n<body>\n    <div class='header'>\n        <h1>My Portfolio</h1>\n        <p>Welcome to my work portfolio</p>\n    </div>\n    <div class='projects'>\n        <div class='project'>\n            <h3>Project 1</h3>\n            <p>Description of project 1</p>\n        </div>\n        <div class='project'>\n            <h3>Project 2</h3>\n            <p>Description of project 2</p>\n        </div>\n    </div>\n</body>\n</html>"
                },
                css: {
                    basic: "body {\n    font-family: Arial, sans-serif;\n    margin: 0;\n    padding: 0;\n    background-color: #f4f4f4;\n    line-height: 1.6;\n}\n\n.container {\n    width: 80%;\n    margin: auto;\n    overflow: hidden;\n}",
                    button: ".btn {\n    background-color: #4CAF50;\n    border: none;\n    color: white;\n    padding: 15px 32px;\n    text-align: center;\n    text-decoration: none;\n    display: inline-block;\n    font-size: 16px;\n    margin: 4px 2px;\n    cursor: pointer;\n    border-radius: 4px;\n    transition: background-color 0.3s;\n}\n\n.btn:hover {\n    background-color: #45a049;\n}",
                    grid: ".grid-container {\n    display: grid;\n    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));\n    gap: 20px;\n    padding: 20px;\n}\n\n.grid-item {\n    background: #f9f9f9;\n    padding: 20px;\n    border-radius: 5px;\n    box-shadow: 0 2px 5px rgba(0,0,0,0.1);\n}",
                    animation: "@keyframes fadeIn {\n    from { opacity: 0; }\n    to { opacity: 1; }\n}\n\n.fade-in {\n    animation: fadeIn 1s ease-in;\n}\n\n@keyframes slideIn {\n    from { transform: translateX(-100%); }\n    to { transform: translateX(0); }\n}\n\n.slide-in {\n    animation: slideIn 0.5s ease-out;\n}"
                }
            };
            
            // Story templates
            const storyTemplates = {
                space: "In the year 2150, humanity had finally achieved what seemed impossible just a century before - a thriving colony on Mars. Captain Ayesha Rahman, a brilliant astrophysicist from Bangladesh, was leading the mission to explore the mysterious valleys of Valles Marineris. What her team discovered there would change our understanding of life in the universe forever...",
                adventure: "Deep in the Amazon rainforest, a team of explorers discovered an ancient temple hidden for millennia. Inside, they found not gold or jewels, but something far more valuable - knowledge that could solve the world's energy crisis. But they weren't alone in their search...",
                fantasy: "In the mystical land of Eldoria, where magic flowed like rivers, a young apprentice named Kael discovered he had a unique ability - he could communicate with dragons, creatures thought to be extinct for centuries. This gift would lead him on an epic journey to restore balance to the world...",
                scifi: "On a distant planet orbiting a red dwarf star, Dr. Chen's research team made an incredible breakthrough. They developed a device that could manipulate time on a small scale. But when corporate interests tried to weaponize their invention, the team had to make a difficult choice...",
                historical: "In 1971, during Bangladesh's Liberation War, a young freedom fighter named Anwar found himself behind enemy lines. With courage and ingenuity, he managed to gather crucial intelligence that would turn the tide of a key battle. His story became legend, inspiring generations to come..."
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
            
            // Document templates
            const documentTemplates = {
                research: [
                    {
                        title: "Deep Learning for Computer Vision: A Comprehensive Review",
                        authors: "Smith, J., Johnson, A., & Williams, R.",
                        year: "2023",
                        url: "https://example.com/deep-learning-vision",
                        abstract: "This paper provides a comprehensive review of deep learning techniques applied to computer vision tasks, covering convolutional neural networks, transformers, and recent advances in the field."
                    },
                    {
                        title: "Natural Language Processing in the Era of Large Language Models",
                        authors: "Chen, L., Gupta, S., & Rodriguez, M.",
                        year: "2022",
                        url: "https://example.com/nlp-llm",
                        abstract: "We survey the landscape of natural language processing following the emergence of large language models, discussing their capabilities, limitations, and ethical considerations."
                    }
                ],
                legal: [
                    {
                        title: "A Guide to Intellectual Property Law for Software Developers",
                        authors: "Legal Advisory Board",
                        year: "2023",
                        url: "https://example.com/ip-law-software",
                        abstract: "This guide explains key intellectual property concepts relevant to software development, including copyright, patents, and open-source licensing."
                    }
                ],
                business: [
                    {
                        title: "The Future of Remote Work: Trends and Predictions",
                        authors: "Global Business Institute",
                        year: "2023",
                        url: "https://example.com/future-remote-work",
                        abstract: "An analysis of how remote work is evolving, with predictions for how businesses will adapt their strategies in the coming years."
                    }
                ]
            };
            
            // Knowledge base for various topics
            const knowledgeBase = {
                technology: {
                    ai: "Artificial Intelligence (AI) refers to the simulation of human intelligence in machines that are programmed to think like humans and mimic their actions. Key areas include machine learning, natural language processing, computer vision, and robotics.",
                    blockchain: "Blockchain is a distributed, immutable ledger that facilitates the process of recording transactions and tracking assets in a business network. It's the technology behind cryptocurrencies like Bitcoin and Ethereum.",
                    iot: "The Internet of Things (IoT) describes the network of physical objects—'things'—that are embedded with sensors, software, and other technologies for the purpose of connecting and exchanging data with other devices and systems over the internet."
                },
                science: {
                    quantum: "Quantum computing is an area of computing focused on developing computer technology based on the principles of quantum theory, which explains the nature and behavior of energy and matter on the quantum (atomic and subatomic) level.",
                    biotechnology: "Biotechnology is the use of biological processes, organisms, or systems to manufacture products intended to improve the quality of human life. It has applications in medicine, agriculture, and industry.",
                    astrophysics: "Astrophysics is a branch of space science that applies the laws of physics and chemistry to explain the birth, life and death of stars, planets, galaxies, nebulae and other objects in the universe."
                },
                history: {
                    bangladesh: "Bangladesh gained independence from Pakistan in 1971 after a nine-month war. The Language Movement of 1952, which is commemorated by International Mother Language Day, was a key event in the history of Bangladesh's independence struggle.",
                    internet: "The Internet originated with the development of electronic computers in the 1950s. The public was first introduced to the concepts that would lead to the Internet when a message was sent over the ARPANET from computer science Professor Leonard Kleinrock's laboratory at University of California, Los Angeles (UCLA) to the second network node at Stanford Research Institute (SRI) on October 29, 1969."
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
            
            // Simulate Google search (in a real implementation, this would use the Google Search API)
            async function simulateGoogleSearch(query) {
                showTypingIndicator();
                
                // Simulate API delay
                await new Promise(resolve => setTimeout(resolve, 2000));
                
                hideTypingIndicator();
                
                // Check if we have predefined results for this query
                if (query.toLowerCase().includes('ai') || query.toLowerCase().includes('artificial intelligence')) {
                    const results = searchResults.ai;
                    let response = `<div class='search-result'>I found these results for '${results.query}':</div>`;
                    
                    results.results.forEach((result, index) => {
                        response += `<div class='search-result'><strong>${index + 1}. <a href="${result.url}" target="_blank">${result.title}</a></strong><br>${result.snippet}</div>`;
                    });
                    
                    return response;
                } else if (query.toLowerCase().includes('bangladesh')) {
                    const results = searchResults.bangladesh;
                    let response = `<div class='search-result'>I found these results for '${results.query}':</div>`;
                    
                    results.results.forEach((result, index) => {
                        response += `<div class='search-result'><strong>${index + 1}. <a href="${result.url}" target="_blank">${result.title}</a></strong><br>${result.snippet}</div>`;
                    });
                    
                    return response;
                } else if (query.toLowerCase().includes('programming') || query.toLowerCase().includes('code')) {
                    const results = searchResults.programming;
                    let response = `<div class='search-result'>I found these results for '${results.query}':</div>`;
                    
                    results.results.forEach((result, index) => {
                        response += `<div class='search-result'><strong>${index + 1}. <a href="${result.url}" target="_blank">${result.title}</a></strong><br>${result.snippet}</div>`;
                    });
                    
                    return response;
                } else {
                    // Generic response for other queries
                    return `<div class='search-result'>I searched for "${query}" and found several relevant results. For more specific information, try refining your search query or ask me about a particular topic.</div>
                    <div class='search-result'><strong>1. <a href="https://example.com/search?q=${encodeURIComponent(query)}" target="_blank">Top results for "${query}"</a></strong><br>Comprehensive information about ${query} from various sources.</div>
                    <div class='search-result'><strong>2. <a href="https://example.com/wiki/${encodeURIComponent(query)}" target="_blank">Wikipedia: ${query}</a></strong><br>Encyclopedia article about ${query} with detailed information.</div>
                    <div class='search-result'><strong>3. <a href="https://example.com/news?q=${encodeURIComponent(query)}" target="_blank">Latest news about
