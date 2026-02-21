The code below contains a design. This design should be used to create a new app or be added to an existing one.

Look at the current open project to determine if a project exists. If no project is open, create a new Vite project then create this view in React after componentizing it.

If a project does exist, determine the framework being used and implement the design within that framework. Identify whether reusable components already exist that can be used to implement the design faithfully and if so use them, otherwise create new components. If other views already exist in the project, make sure to place the view in a sensible route and connect it to the other views.

Ensure the visual characteristics, layout, and interactions in the design are preserved with perfect fidelity.

Run the dev command so the user can see the app once finished.

```
<html lang="en" vid="0"><head vid="1">
    <meta charset="UTF-8" vid="2">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" vid="3">
    <title vid="4">AI Trade Optimization - Route Optimizer</title>
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
            text-decoration: none;
            color: inherit;
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
        }

        .workspace-header {
            padding: 24px;
            border-bottom: 2px solid var(--bg-dark);
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            background-color: var(--bg-canvas);
            z-index: 10;
        }

        .big-title {
            font-size: 3rem;
            line-height: 0.9;
            font-family: var(--font-sans);
            font-weight: 700;
        }

        .map-container {
            flex: 1;
            position: relative;
            background-color: #B0B0B0;
            overflow: hidden;
            background-image: 
                radial-gradient(circle, #888 1px, transparent 1px);
            background-size: 30px 30px;
        }

        .map-svg {
            width: 100%;
            height: 100%;
            filter: grayscale(1);
            opacity: 0.4;
        }

        .route-line {
            fill: none;
            stroke: var(--bg-dark);
            stroke-width: 2;
            stroke-dasharray: 8 4;
            animation: dash 30s linear infinite;
        }

        @keyframes dash {
            to { stroke-dashoffset: -1000; }
        }

        .map-point {
            fill: var(--bg-dark);
            stroke: var(--bg-canvas);
            stroke-width: 2;
        }

        .map-label {
            font-family: var(--font-pixel);
            font-size: 10px;
            fill: var(--bg-dark);
        }

        .intelligence-panel {
            background-color: var(--bg-dark);
            color: var(--text-inv);
            padding: 24px;
            display: flex;
            flex-direction: column;
            gap: 24px;
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

        .route-card {
            border: 1px solid #333;
            padding: 20px;
            cursor: pointer;
            transition: all 0.2s;
        }

        .route-card:hover {
            border-color: #666;
            background: #111;
        }

        .route-card.selected {
            border-color: #00FF41;
            box-shadow: inset 0 0 10px rgba(0,255,65,0.1);
        }

        .carrier-name {
            font-family: var(--font-pixel);
            font-size: 1.2rem;
            margin-bottom: 8px;
            display: block;
        }

        .stat-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            margin-top: 12px;
        }

        .stat-item .val {
            font-size: 1.5rem;
            font-weight: 700;
            display: block;
        }

        .btn-primary {
            background: var(--bg-dark);
            color: var(--bg-canvas);
            border: 1px solid #333;
            padding: 16px 32px;
            font-family: var(--font-pixel);
            text-transform: uppercase;
            font-size: 0.9rem;
            cursor: pointer;
            width: 100%;
            margin-top: 24px;
        }

        .btn-primary:hover {
            background: #222;
        }

        .map-overlay-controls {
            position: absolute;
            bottom: 24px;
            left: 24px;
            display: flex;
            gap: 8px;
        }

        .control-tag {
            background: var(--bg-canvas);
            border: 1px solid var(--bg-dark);
            padding: 6px 12px;
            font-size: 0.7rem;
            font-family: var(--font-pixel);
            cursor: pointer;
        }

        .tag {
            border: 1px solid var(--bg-dark);
            padding: 4px 8px;
            font-size: 0.7rem;
            text-transform: uppercase;
            display: inline-block;
        }

        .tag.dark {
            border-color: #444;
            color: #888;
        }

        .tag.active-tag {
            border-color: #00FF41;
            color: #00FF41;
        }

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: #333; }
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
                    <div class="nav-item" vid="27">
                        <span vid="28">LANDED COST</span>
                        <span class="arrow-down" vid="29">↓</span>
                    </div>
                    <div class="nav-item active" vid="30">
                        <span vid="31">ROUTE OPTIMIZER</span>
                        <span class="arrow-down" vid="32">←</span>
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
                    <h1 class="big-title" vid="45">ROUTE<br vid="46"><span class="pixel-text" vid="47">OPTIMIZER</span></h1>
                </div>
                <div style="text-align: right;" vid="48">
                    <div class="label" vid="49">PATHWAY</div>
                    <div class="pixel-text" style="font-size: 1.2rem;" vid="50">SHANGHAI → LONG BEACH</div>
                </div>
            </header>

            <div class="map-container" vid="51">
                <svg class="map-svg" viewBox="0 0 800 400" vid="52">
                    <path d="M50,150 Q200,50 400,150 T750,150" fill="none" stroke="#666" stroke-width="1" vid="53"></path>
                    <path class="route-line" d="M120,180 Q350,100 680,180" vid="54"></path>
                    
                    <circle class="map-point" cx="120" cy="180" r="5" vid="55"></circle>
                    <text x="110" y="200" class="map-label" vid="56">CNSHA</text>
                    
                    <circle class="map-point" cx="680" cy="180" r="5" vid="57"></circle>
                    <text x="670" y="200" class="map-label" vid="58">USLGB</text>
                </svg>

                <div class="map-overlay-controls" vid="59">
                    <div class="control-tag" vid="60">RECENTER</div>
                    <div class="control-tag" vid="61">LAYERS: TRAFFIC</div>
                    <div class="control-tag" vid="62">TIME_SLIDER</div>
                </div>
            </div>

            <div style="padding: 16px 24px; border-top: 1px solid var(--bg-dark); background: rgba(0,0,0,0.02);" vid="63">
                <div class="label" vid="64">CONSIGNMENT DETAILS</div>
                <div style="display: flex; gap: 32px; font-size: 0.85rem;" vid="65">
                    <div vid="66"><span style="opacity: 0.6;" vid="67">CARGO:</span> 40FT HC CONTAINER (x12)</div>
                    <div vid="68"><span style="opacity: 0.6;" vid="69">WEIGHT:</span> 242,000 KG</div>
                    <div vid="70"><span style="opacity: 0.6;" vid="71">EST. DEPARTURE:</span> 28 OCT 2023</div>
                </div>
            </div>
        </main>

        <aside class="intelligence-panel" vid="72">
            <div class="ai-header" vid="73">
                <div class="label" style="color:var(--text-inv)" vid="74">ROUTE ANALYTICS</div>
                <div class="label" style="color:#00FF41" vid="75">LIVE_DATA</div>
            </div>

            <div class="route-card selected" vid="76">
                <div class="label" style="color: #00FF41;" vid="77">RECOMMENDED / FASTEST</div>
                <span class="carrier-name" vid="78">MAERSK_LINE</span>
                <div class="stat-grid" vid="79">
                    <div class="stat-item" vid="80">
                        <span class="label" style="color: #888" vid="81">TRANSIT</span>
                        <span class="val pixel-text" vid="82">14D</span>
                    </div>
                    <div class="stat-item" vid="83">
                        <span class="label" style="color: #888" vid="84">COST</span>
                        <span class="val pixel-text" vid="85">$4,210</span>
                    </div>
                </div>
                <div style="margin-top: 12px;" vid="86">
                    <span class="tag active-tag" vid="87">DIRECT</span>
                    <span class="tag dark" vid="88">RELIABILITY: 94%</span>
                </div>
            </div>

            <div class="route-card" vid="89">
                <div class="label" style="color: #888;" vid="90">ECONOMY</div>
                <span class="carrier-name" vid="91">COSCO_SHIPPING</span>
                <div class="stat-grid" vid="92">
                    <div class="stat-item" vid="93">
                        <span class="label" style="color: #888" vid="94">TRANSIT</span>
                        <span class="val pixel-text" vid="95">19D</span>
                    </div>
                    <div class="stat-item" vid="96">
                        <span class="label" style="color: #888" vid="97">COST</span>
                        <span class="val pixel-text" vid="98">$3,150</span>
                    </div>
                </div>
                <div style="margin-top: 12px;" vid="99">
                    <span class="tag dark" vid="100">1 TRANSSHIPMENT</span>
                    <span class="tag dark" vid="101">RELIABILITY: 88%</span>
                </div>
            </div>

            <div class="route-card" vid="102">
                <div class="label" style="color: #888;" vid="103">BALANCED</div>
                <span class="carrier-name" vid="104">MSC_GLOBAL</span>
                <div class="stat-grid" vid="105">
                    <div class="stat-item" vid="106">
                        <span class="label" style="color: #888" vid="107">TRANSIT</span>
                        <span class="val pixel-text" vid="108">16D</span>
                    </div>
                    <div class="stat-item" vid="109">
                        <span class="label" style="color: #888" vid="110">COST</span>
                        <span class="val pixel-text" vid="111">$3,840</span>
                    </div>
                </div>
                <div style="margin-top: 12px;" vid="112">
                    <span class="tag dark" vid="113">DIRECT</span>
                    <span class="tag dark" vid="114">RELIABILITY: 91%</span>
                </div>
            </div>

            <div style="flex: 1;" vid="115"></div>

            <div style="padding: 16px; border: 1px dashed #333;" vid="116">
                <div class="label" style="color: #888" vid="117">EMISSIONS IMPACT</div>
                <div class="pixel-text" style="font-size: 1.1rem;" vid="118">14.2 TONS CO2e</div>
                <div style="height: 4px; background: #222; margin-top: 8px;" vid="119">
                    <div style="width: 65%; height: 100%; background: #444;" vid="120"></div>
                </div>
            </div>

            <button class="btn-primary" vid="121">
                BOOK SHIPMENT →
            </button>
        </aside>
    </div>

</body></html>
```
