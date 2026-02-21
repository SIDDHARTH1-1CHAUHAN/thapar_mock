The code below contains a design. This design should be used to create a new app or be added to an existing one.

Look at the current open project to determine if a project exists. If no project is open, create a new Vite project then create this view in React after componentizing it.

If a project does exist, determine the framework being used and implement the design within that framework. Identify whether reusable components already exist that can be used to implement the design faithfully and if so use them, otherwise create new components. If other views already exist in the project, make sure to place the view in a sensible route and connect it to the other views.

Ensure the visual characteristics, layout, and interactions in the design are preserved with perfect fidelity.

Run the dev command so the user can see the app once finished.

```
<html lang="en" vid="0"><head vid="1">
    <meta charset="UTF-8" vid="2">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" vid="3">
    <title vid="4">AI Trade Optimization - Chat Assistant</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" vid="5">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="" vid="6">
    <link href="https://fonts.googleapis.com/css2?family=Archivo:wdth,wght@100,400;100,700&amp;family=Silkscreen&amp;family=Space+Grotesk:wght@400;600&amp;display=swap" rel="stylesheet" vid="7">
    <style vid="8">
        :root {
            --bg-canvas: #C8C8C8; 
            --bg-dark: #080808;    
            --text-main: #000000;
            --text-inv: #E8E8E8;
            --accent-border: #000000;
            --font-sans: 'Space Grotesk', 'Helvetica Neue', sans-serif; 
            --font-pixel: 'Silkscreen', monospace; 
            --radius: 0px; 
            --spacing-unit: 8px;
        }

        * {
            box-sizing: border-box;
            -webkit-font-smoothing: antialiased;
        }

        body {
            margin: 0;
            padding: 0;
            background-color: var(--bg-canvas);
            color: var(--text-main);
            font-family: var(--font-sans);
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        h1, h2, h3, h4, .label {
            text-transform: uppercase;
            letter-spacing: 0.02em;
            margin: 0;
            font-weight: 600;
        }

        .label {
            font-size: 0.7rem;
            letter-spacing: 0.05em;
            margin-bottom: 4px;
            opacity: 0.8;
            display: flex;
            align-items: center;
            gap: 4px;
        }

        .pixel-text {
            font-family: var(--font-pixel);
            letter-spacing: -0.05em;
        }

        .layout-grid {
            display: grid;
            grid-template-columns: 260px 1fr 400px; 
            height: 100%;
            border-top: 2px solid var(--bg-dark);
        }

        .sidebar {
            border-right: 2px solid var(--bg-dark);
            padding: 24px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .logo-area {
            margin-bottom: 48px;
        }

        .logo-icon {
            width: 48px;
            height: 48px;
            border: 2px solid var(--bg-dark);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 16px;
        }

        .logo-icon svg {
            width: 24px;
            height: 24px;
        }

        .nav-menu {
            display: flex;
            flex-direction: column;
        }

        .nav-item {
            padding: 12px 0;
            border-bottom: 1px solid var(--bg-dark);
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            transition: padding-left 0.2s;
        }

        .nav-item:hover {
            padding-left: 8px;
            background: rgba(0,0,0,0.05);
        }

        .nav-item.active {
            font-family: var(--font-pixel);
            font-size: 1.1em;
        }

        .arrow-down {
            font-size: 0.8em;
            margin-top: 2px;
        }

        .workspace {
            padding: 0;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            position: relative;
            background-color: #D1D1D1;
        }

        .workspace-header {
            padding: 24px;
            border-bottom: 2px solid var(--bg-dark);
            background-color: var(--bg-canvas);
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            flex-shrink: 0;
        }

        .big-title {
            font-size: 3rem;
            line-height: 0.9;
            font-family: var(--font-sans);
            font-weight: 700;
        }

        
        .chat-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            padding: 24px;
            gap: 20px;
        }

        .messages-scroll {
            flex: 1;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 16px;
            padding-right: 8px;
        }

        .message {
            max-width: 80%;
            padding: 16px;
            font-size: 0.95rem;
            line-height: 1.5;
            position: relative;
            animation: slideIn 0.3s ease-out;
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .message.ai {
            align-self: flex-start;
            background: var(--bg-dark);
            color: var(--text-inv);
            border: 1px solid #333;
        }

        .message.user {
            align-self: flex-end;
            background: transparent;
            border: 1px solid var(--bg-dark);
            color: var(--text-main);
        }

        .typing-indicator {
            display: flex;
            gap: 4px;
            padding: 12px 16px;
            background: var(--bg-dark);
            width: fit-content;
        }

        .dot {
            width: 6px;
            height: 6px;
            background: var(--text-inv);
            animation: pulse 1s infinite ease-in-out;
        }

        .dot:nth-child(2) { animation-delay: 0.2s; }
        .dot:nth-child(3) { animation-delay: 0.4s; }

        @keyframes pulse {
            0%, 100% { opacity: 0.3; transform: scale(0.8); }
            50% { opacity: 1; transform: scale(1.1); }
        }

        .suggested-actions {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-top: 8px;
        }

        .suggestion-chip {
            border: 1px solid #444;
            padding: 6px 12px;
            font-size: 0.75rem;
            font-family: var(--font-pixel);
            cursor: pointer;
            transition: all 0.2s;
            color: #AAA;
        }

        .suggestion-chip:hover {
            border-color: var(--text-inv);
            color: var(--text-inv);
            background: rgba(255,255,255,0.05);
        }

        .chat-input-area {
            padding: 24px;
            background-color: var(--bg-canvas);
            border-top: 2px solid var(--bg-dark);
            display: flex;
            gap: 12px;
        }

        .chat-input {
            flex: 1;
            background: transparent;
            border: 1px solid var(--bg-dark);
            padding: 16px;
            font-family: var(--font-sans);
            font-size: 1rem;
        }

        .send-btn {
            background: var(--bg-dark);
            color: var(--bg-canvas);
            border: none;
            padding: 0 24px;
            font-family: var(--font-pixel);
            cursor: pointer;
        }

        
        .intelligence-panel {
            background-color: var(--bg-dark);
            color: var(--text-inv);
            padding: 24px;
            display: flex;
            flex-direction: column;
            gap: 32px;
            border-left: 2px solid var(--bg-dark);
            overflow-y: auto;
        }

        .ai-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding-bottom: 24px;
            border-bottom: 1px solid #333;
        }

        .ai-status {
            display: flex;
            align-items: center;
            gap: 8px;
            font-family: var(--font-pixel);
            font-size: 0.8rem;
        }

        .spinner {
            width: 12px;
            height: 12px;
            background: var(--text-inv);
            animation: blink 1s infinite;
        }

        @keyframes blink {
            0%, 100% { opacity: 1; }
            50% { opacity: 0; }
        }

        .result-card {
            border: 1px solid #333;
            padding: 20px;
        }

        .hs-code-mini {
            font-family: var(--font-pixel);
            font-size: 1.5rem;
            margin: 10px 0;
        }

        .tag {
            border: 1px solid var(--bg-dark);
            padding: 4px 8px;
            font-size: 0.7rem;
            text-transform: uppercase;
            display: inline-block;
            margin-right: 4px;
        }

        .tag.dark {
            border-color: #555;
            color: #AAA;
        }

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: var(--bg-dark); }
        .intelligence-panel::-webkit-scrollbar-thumb { background: #333; }

    </style>
</head>
<body vid="9">

    <div class="layout-grid" vid="10">
        
        <aside class="sidebar" vid="11">
            <div vid="12">
                <div class="logo-area" vid="13">
                    <div class="logo-icon" vid="14">
                        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" vid="15">
                            <line x1="2" y1="22" x2="22" y2="2" vid="16"></line>
                            <polyline points="12 2 22 2 22 12" vid="17"></polyline>
                            <circle cx="12" cy="12" r="10" stroke-width="1.5" vid="18"></circle>
                        </svg>
                    </div>
                    <div class="label" vid="19">SYSTEM</div>
                    <div style="font-weight:bold; font-size: 1.2rem;" vid="20">TRADE<br vid="21">OPTIMIZER</div>
                </div>

                <nav class="nav-menu" vid="22">
                    <div class="label" vid="23">MODULES</div>
                    <div class="nav-item" vid="24">
                        <span vid="25">HS CLASSIFIER</span>
                        <span class="arrow-down" vid="26">↓</span>
                    </div>
                    <div class="nav-item active" vid="27">
                        <span vid="28">AI ASSISTANT</span>
                        <span class="arrow-down" vid="29">←</span>
                    </div>
                    <div class="nav-item" vid="30">
                        <span vid="31">LANDED COST</span>
                        <span class="arrow-down" vid="32">↓</span>
                    </div>
                    <div class="nav-item" vid="33">
                        <span vid="34">COMPLIANCE</span>
                        <span class="arrow-down" vid="35">↓</span>
                    </div>
                </nav>
            </div>

            <div vid="36">
                <div class="label" vid="37">USER</div>
                <div style="font-size: 0.9rem;" vid="38">DIMA VTULKIN<br vid="39">GLOBAL LOGISTICS</div>
                <div style="margin-top: 24px; font-size: 0.8rem; opacity: 0.6;" vid="40">V.2.4.0</div>
            </div>
        </aside>

        
        <main class="workspace" vid="41">
            <header class="workspace-header" vid="42">
                <div vid="43">
                    <div class="label" vid="44">ACTIVE MODULE</div>
                    <h1 class="big-title" vid="45">AI<br vid="46"><span class="pixel-text" vid="47">ASSISTANT</span></h1>
                </div>
                <div style="text-align: right;" vid="48">
                    <div class="label" vid="49">SESSION ID</div>
                    <div class="pixel-text" style="font-size: 0.8rem;" vid="50">CHAT_8824_XQ</div>
                </div>
            </header>

            <div class="chat-container" vid="51">
                <div class="messages-scroll" vid="52">
                    
                    <div class="message ai" vid="53">
                        <div class="label" style="color: #888" vid="54">SYSTEM_CORE</div>
                        Hello Dima. I've analyzed your recent imports. Would you like to review the classification for the "Wireless Headphones" pending in your queue, or start a new product inquiry?
                        <div class="suggested-actions" vid="55">
                            <div class="suggestion-chip" vid="56">REVIEW_PENDING</div>
                            <div class="suggestion-chip" vid="57">NEW_CLASSIFICATION</div>
                            <div class="suggestion-chip" vid="58">IMPORT_POLICY_QUERY</div>
                        </div>
                    </div>

                    
                    <div class="message user" vid="59">
                        I need to check the HS code for a "Solar-powered portable charger with integrated LED flashlight" shipping from Shenzhen to Los Angeles.
                    </div>

                    
                    <div class="message ai" vid="60">
                        <div class="label" style="color: #888" vid="61">SYSTEM_CORE</div>
                        Processing technical specifications for solar-integrated power banks. Based on General Interpretive Rule (GIR) 3(b), composite goods are classified by the component that provides the essential character.
                        <br vid="62"><br vid="63">
                        I am leaning towards Heading 8504 (Static Converters) rather than 8513 (Flashlights). Let me verify the Section XVI notes.
                    </div>

                    
                    <div class="typing-indicator" vid="64">
                        <div class="dot" vid="65"></div>
                        <div class="dot" vid="66"></div>
                        <div class="dot" vid="67"></div>
                    </div>
                </div>
            </div>

            <div class="chat-input-area" vid="68">
                <input type="text" class="chat-input" placeholder="Query trade database or ask for classification help..." vid="69">
                <button class="send-btn" vid="70">SEND_&gt;</button>
            </div>
        </main>

        
        <aside class="intelligence-panel" vid="71">
            <div class="ai-header" vid="72">
                <div class="label" style="color:var(--text-inv)" vid="73">LIVE_CONTEXT</div>
                <div class="ai-status" vid="74">
                    <div class="spinner" vid="75"></div>
                    THINKING
                </div>
            </div>

            <div class="result-card" vid="76">
                <div class="label" style="color: #888" vid="77">PROBABLE HS CODE</div>
                <div class="hs-code-mini pixel-text" vid="78">8504.40.95</div>
                <div style="font-size: 0.8rem; opacity: 0.7; margin-bottom: 12px;" vid="79">Static converters; power supplies for ADP machines.</div>
                
                <div class="label" style="color: #888" vid="80">RULING REFERENCES</div>
                <div style="font-size: 0.75rem; color: #BBB; line-height: 1.4;" vid="81">
                    • CROSS Ruling N302451<br vid="82">
                    • NY N293245 (Similar solar units)
                </div>
            </div>

            <div class="result-card" vid="83">
                <div class="label" style="color: #888" vid="84">TRADE BARRIERS</div>
                <div style="margin-top: 8px;" vid="85">
                    <div class="tag dark" vid="86">SECTION 301: 25%</div>
                    <div class="tag dark" vid="87">LITHIUM BATTERY REGS</div>
                </div>
                <div style="font-size: 0.75rem; margin-top: 12px; color: #FF4141;" vid="88">
                    ! REQUIRES UN38.3 CERTIFICATION
                </div>
            </div>

            <div style="flex: 1;" vid="89"></div>

            <div style="border-top: 1px solid #333; padding-top: 16px;" vid="90">
                <div class="label" style="color: #888" vid="91">KNOWLEDGE BASE</div>
                <div style="font-size: 0.8rem; opacity: 0.6;" vid="92">WCO 2022 Nomenclature Index v1.4</div>
            </div>
        </aside>
    </div>

</body></html>
```
