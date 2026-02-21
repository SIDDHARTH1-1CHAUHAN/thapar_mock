The code below contains a design. This design should be used to create a new app or be added to an existing one.

Look at the current open project to determine if a project exists. If no project is open, create a new Vite project then create this view in React after componentizing it.

If a project does exist, determine the framework being used and implement the design within that framework. Identify whether reusable components already exist that can be used to implement the design faithfully and if so use them, otherwise create new components. If other views already exist in the project, make sure to place the view in a sensible route and connect it to the other views.

Ensure the visual characteristics, layout, and interactions in the design are preserved with perfect fidelity.

Run the dev command so the user can see the app once finished.

```
<html lang="en" vid="0"><head vid="1">
    <meta charset="UTF-8" vid="2">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" vid="3">
    <title vid="4">AI Trade Optimization</title>
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
            gap: 0; 
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
            overflow-y: auto;
            position: relative;
        }

        .workspace-header {
            padding: 24px;
            border-bottom: 2px solid var(--bg-dark);
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
        }

        .big-title {
            font-size: 3rem;
            line-height: 0.9;
            font-family: var(--font-sans);
            font-weight: 700;
        }

        .form-grid {
            padding: 24px;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 24px;
        }

        .input-group {
            display: flex;
            flex-direction: column;
            position: relative;
        }

        .input-group.full-width {
            grid-column: span 2;
        }

        input, textarea, select {
            background: transparent;
            border: 1px solid var(--bg-dark);
            padding: 16px;
            font-family: var(--font-sans);
            font-size: 1rem;
            color: var(--text-main);
            border-radius: 0;
            width: 100%;
            outline: none;
        }

        input:focus, textarea:focus, select:focus {
            background: #E8E8E8;
        }

        textarea {
            resize: none;
            height: 120px;
        }

        .file-drop {
            border: 1px dashed var(--bg-dark);
            padding: 24px;
            text-align: center;
            cursor: pointer;
            transition: background 0.2s;
        }

        .file-drop:hover {
            background: rgba(0,0,0,0.05);
            border-style: solid;
        }

        
        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 24px;
        }

        .data-table th {
            text-align: left;
            padding: 12px 24px;
            border-bottom: 2px solid var(--bg-dark);
            font-family: var(--font-pixel);
            font-size: 0.8rem;
            text-transform: uppercase;
        }

        .data-table td {
            padding: 16px 24px;
            border-bottom: 1px solid var(--bg-dark);
            font-size: 0.9rem;
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
            color: #00FF41; 
            color: var(--text-inv); 
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
            padding: 24px;
            position: relative;
        }

        .result-card:hover {
            border-color: #666;
        }

        .confidence-score {
            font-family: var(--font-pixel);
            font-size: 3rem;
            line-height: 1;
            margin: 16px 0;
            display: block;
        }

        .hs-code-display {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: 8px;
            letter-spacing: 0.05em;
        }

        .breakdown-row {
            display: flex;
            justify-content: space-between;
            margin-top: 12px;
            padding-top: 12px;
            border-top: 1px solid #333;
            font-size: 0.9rem;
        }

        
        .btn-primary {
            background: var(--bg-dark);
            color: var(--bg-canvas);
            border: none;
            padding: 16px 32px;
            font-family: var(--font-pixel);
            text-transform: uppercase;
            font-size: 0.9rem;
            cursor: pointer;
            width: 100%;
            margin-top: 24px;
            transition: transform 0.1s;
        }

        .btn-primary:active {
            transform: translateY(2px);
        }

        .btn-primary:hover {
            background: #222;
        }

        
        .spacer { flex: 1; }
        
        .tag {
            border: 1px solid var(--bg-dark);
            padding: 4px 8px;
            font-size: 0.7rem;
            text-transform: uppercase;
            display: inline-block;
            margin-right: 4px;
        }

        .tag.dark {
            border-color: var(--text-inv);
            color: var(--text-inv);
        }

        
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: transparent;
        }
        ::-webkit-scrollbar-thumb {
            background: var(--bg-dark);
        }
        .intelligence-panel::-webkit-scrollbar-thumb {
            background: #333;
        }

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
                    <div class="nav-item active" vid="24">
                        <span vid="25">HS CLASSIFIER</span>
                        <span class="arrow-down" vid="26">←</span>
                    </div>
                    <div class="nav-item" vid="27">
                        <span vid="28">LANDED COST</span>
                        <span class="arrow-down" vid="29">↓</span>
                    </div>
                    <div class="nav-item" vid="30">
                        <span vid="31">ROUTE OPTIMIZER</span>
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
                    <div class="label" vid="44">ACTIVE TASK</div>
                    <h1 class="big-title" vid="45">CLASSIFY<br vid="46"><span class="pixel-text" vid="47">PRODUCT</span></h1>
                </div>
                <div style="text-align: right;" vid="48">
                    <div class="label" vid="49">DATE</div>
                    <div vid="50">24 OCT 2023</div>
                </div>
            </header>

            <div class="form-grid" vid="51">
                <div class="input-group full-width" vid="52">
                    <label class="label" vid="53">PRODUCT DESCRIPTION</label>
                    <textarea placeholder="e.g. Electric toothbrush with lithium-ion battery and travel case..." vid="54"></textarea>
                </div>

                <div class="input-group" vid="55">
                    <label class="label" vid="56">ORIGIN COUNTRY</label>
                    <select vid="57">
                        <option vid="58">China (CN)</option>
                        <option vid="59">Vietnam (VN)</option>
                        <option vid="60">Germany (DE)</option>
                    </select>
                </div>

                <div class="input-group" vid="61">
                    <label class="label" vid="62">DESTINATION</label>
                    <select vid="63">
                        <option vid="64">United States (US)</option>
                        <option vid="65">Canada (CA)</option>
                        <option vid="66">European Union (EU)</option>
                    </select>
                </div>

                <div class="input-group full-width" vid="67">
                    <label class="label" vid="68">SUPPORTING DOCUMENTATION</label>
                    <div class="file-drop" vid="69">
                        <span class="pixel-text" vid="70">DRAG_DROP_IMAGE_OR_PDF</span>
                        <br vid="71">
                        <span style="font-size: 0.8rem; margin-top: 8px; display:block;" vid="72">Technical Specs, Blueprints, Invoices</span>
                    </div>
                </div>
            </div>

            <div style="padding: 0 24px 24px 24px;" vid="73">
                <div class="label" vid="74">RECENT CLASSIFICATIONS</div>
                <table class="data-table" vid="75">
                    <thead vid="76">
                        <tr vid="77">
                            <th vid="78">REF ID</th>
                            <th vid="79">ITEM</th>
                            <th vid="80">HS CODE</th>
                            <th vid="81">STATUS</th>
                        </tr>
                    </thead>
                    <tbody vid="82">
                        <tr vid="83">
                            <td vid="84">#TR-8821</td>
                            <td vid="85">Cotton T-Shirt (Knitted)</td>
                            <td vid="86">6109.10.00</td>
                            <td vid="87"><span class="tag" vid="88">VERIFIED</span></td>
                        </tr>
                        <tr vid="89">
                            <td vid="90">#TR-8820</td>
                            <td vid="91">Ceramic Coffee Mug</td>
                            <td vid="92">6912.00.25</td>
                            <td vid="93"><span class="tag" vid="94">VERIFIED</span></td>
                        </tr>
                        <tr vid="95">
                            <td vid="96">#TR-8819</td>
                            <td vid="97">Wireless Headphones</td>
                            <td vid="98">8518.30.20</td>
                            <td vid="99"><span class="tag" vid="100">PENDING</span></td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </main>

        
        <aside class="intelligence-panel" vid="101">
            <div class="ai-header" vid="102">
                <div class="label" style="color:var(--text-inv)" vid="103">AI ANALYSIS</div>
                <div class="ai-status" vid="104">
                    <div class="spinner" vid="105"></div>
                    PROCESSING
                </div>
            </div>

            <div class="result-card" vid="106">
                <div class="label" style="color: #888" vid="107">PREDICTED HS CODE</div>
                <div class="hs-code-display pixel-text" vid="108">8509.80</div>
                <div class="label" style="color: #888" vid="109">SUBHEADING</div>
                <div style="font-size: 0.9rem; margin-bottom: 16px;" vid="110">Electro-mechanical domestic appliances, with self-contained electric motor.</div>
                
                <div class="label" style="color: #888" vid="111">CONFIDENCE</div>
                <span class="confidence-score" vid="112">98.4%</span>
                
                <div style="margin-top: 16px;" vid="113">
                    <span class="tag dark" vid="114">DUTY: 4.2%</span>
                    <span class="tag dark" vid="115">FTA ELIGIBLE</span>
                </div>
            </div>

            <div class="result-card" vid="116">
                <div class="label" style="color: #888" vid="117">LANDED COST EST.</div>
                <div class="hs-code-display pixel-text" style="font-size: 2rem;" vid="118">$2,450.00</div>
                
                <div class="breakdown-row" vid="119">
                    <span vid="120">FOB Value</span>
                    <span vid="121">$2,100.00</span>
                </div>
                <div class="breakdown-row" vid="122">
                    <span vid="123">Duty (4.2%)</span>
                    <span vid="124">$88.20</span>
                </div>
                <div class="breakdown-row" vid="125">
                    <span vid="126">MPF / HMF</span>
                    <span vid="127">$45.00</span>
                </div>
                <div class="breakdown-row" vid="128">
                    <span vid="129">Freight</span>
                    <span vid="130">$216.80</span>
                </div>
            </div>

            <div class="spacer" vid="131"></div>

            <button class="btn-primary" vid="132">
                EXPORT DOCUMENTS ↓
            </button>
        </aside>
    </div>


</body></html>
```
