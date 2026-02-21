The code below contains a design. This design should be used to create a new app or be added to an existing one.

Look at the current open project to determine if a project exists. If no project is open, create a new Vite project then create this view in React after componentizing it.

If a project does exist, determine the framework being used and implement the design within that framework. Identify whether reusable components already exist that can be used to implement the design faithfully and if so use them, otherwise create new components. If other views already exist in the project, make sure to place the view in a sensible route and connect it to the other views.

Ensure the visual characteristics, layout, and interactions in the design are preserved with perfect fidelity.

Run the dev command so the user can see the app once finished.

```
<html lang="en" vid="0"><head vid="1">
    <meta charset="UTF-8" vid="2">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" vid="3">
    <title vid="4">Trade Optimizer | Shipment Tracking</title>
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
            overflow: hidden;
            position: relative;
        }

        .workspace-header {
            padding: 24px;
            border-bottom: 2px solid var(--bg-dark);
            display: flex;
            justify-content: space-between;
            align-items: flex-end;
            z-index: 10;
            background: var(--bg-canvas);
        }

        .big-title {
            font-size: 3rem;
            line-height: 0.9;
            font-family: var(--font-sans);
            font-weight: 700;
        }

        
        .map-container {
            flex: 1;
            background-color: #B0B0B0;
            position: relative;
            overflow: hidden;
            background-image: radial-gradient(circle, #999 1px, transparent 1px);
            background-size: 30px 30px;
        }

        .map-svg {
            width: 100%;
            height: 100%;
            opacity: 0.3;
        }

        .marker {
            position: absolute;
            width: 12px;
            height: 12px;
            background: var(--bg-dark);
            border: 2px solid #fff;
            border-radius: 50%;
            transform: translate(-50%, -50%);
        }

        .marker-pulse {
            position: absolute;
            width: 12px;
            height: 12px;
            background: var(--bg-dark);
            border-radius: 50%;
            transform: translate(-50%, -50%);
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { transform: translate(-50%, -50%) scale(1); opacity: 0.8; }
            100% { transform: translate(-50%, -50%) scale(4); opacity: 0; }
        }

        .ship-path {
            position: absolute;
            border-top: 2px dashed var(--bg-dark);
            height: 0;
            transform-origin: 0 0;
            opacity: 0.5;
        }

        
        .timeline-container {
            padding: 24px;
            border-top: 2px solid var(--bg-dark);
            background: var(--bg-canvas);
            max-height: 300px;
            overflow-y: auto;
        }

        .timeline {
            display: flex;
            gap: 40px;
            padding: 20px 0;
            position: relative;
        }

        .timeline::before {
            content: '';
            position: absolute;
            top: 36px;
            left: 0;
            right: 0;
            height: 1px;
            background: var(--bg-dark);
            z-index: 1;
        }

        .timeline-step {
            position: relative;
            z-index: 2;
            min-width: 120px;
        }

        .step-node {
            width: 24px;
            height: 24px;
            background: var(--bg-canvas);
            border: 2px solid var(--bg-dark);
            margin-bottom: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .step-node.completed {
            background: var(--bg-dark);
        }

        .step-node.active {
            animation: blink 1s infinite;
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

        .notification-card {
            border-left: 4px solid #fff;
            background: #111;
            padding: 16px;
            margin-bottom: 12px;
            font-size: 0.85rem;
            position: relative;
        }

        .notification-card.alert {
            border-left-color: #FF4100;
        }

        .time-stamp {
            font-family: var(--font-pixel);
            font-size: 0.65rem;
            opacity: 0.5;
            margin-bottom: 4px;
            display: block;
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
            border-color: var(--text-inv);
            color: var(--text-inv);
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
            margin-top: auto;
        }

        .spacer { flex: 1; }

        @keyframes blink {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.4; }
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
                    <div class="nav-item" vid="24">
                        <span vid="25">HS CLASSIFIER</span>
                        <span class="arrow-down" vid="26">↓</span>
                    </div>
                    <div class="nav-item active" vid="27">
                        <span vid="28">LIVE TRACKING</span>
                        <span class="arrow-down" vid="29">←</span>
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
                    <div class="label" vid="44">ACTIVE SHIPMENT</div>
                    <h1 class="big-title" vid="45">CARGO<br vid="46"><span class="pixel-text" vid="47">LOCATOR</span></h1>
                </div>
                <div style="text-align: right;" vid="48">
                    <div class="label" vid="49">TRACKING ID</div>
                    <div class="pixel-text" style="font-size: 1.2rem;" vid="50">#MSC-992-XQ</div>
                </div>
            </header>

            <div class="map-container" vid="51">
                
                <div class="ship-path" style="width: 300px; transform: rotate(-25deg); left: 20%; top: 40%;" vid="52"></div>
                
                
                <div class="marker" style="left: 20%; top: 40%;" vid="53"></div>
                <div style="position: absolute; left: 21%; top: 42%; font-size: 0.7rem; font-weight: bold;" vid="54">SHANGHAI (CNSHA)</div>

                
                <div class="marker-pulse" style="left: 45%; top: 30%;" vid="55"></div>
                <div class="marker" style="left: 45%; top: 30%; background: #fff; border-color: var(--bg-dark);" vid="56"></div>
                <div style="position: absolute; left: 46%; top: 25%; font-family: var(--font-pixel); font-size: 0.8rem; background: var(--bg-dark); color: #fff; padding: 4px 8px;" vid="57">MSC OSCAR</div>

                
                <div class="marker" style="left: 80%; top: 45%; opacity: 0.3;" vid="58"></div>
                <div style="position: absolute; left: 81%; top: 47%; font-size: 0.7rem; font-weight: bold; opacity: 0.5;" vid="59">LOS ANGELES (USLAX)</div>
            </div>

            <div class="timeline-container" vid="60">
                <div class="label" vid="61">DELIVERY PIPELINE</div>
                <div class="timeline" vid="62">
                    <div class="timeline-step" vid="63">
                        <div class="step-node completed" vid="64"></div>
                        <div class="label" vid="65">DEPARTED</div>
                        <div style="font-size: 0.8rem;" vid="66">Shanghai Terminal</div>
                        <div class="time-stamp" style="opacity: 0.8;" vid="67">21 OCT 04:30</div>
                    </div>
                    <div class="timeline-step" vid="68">
                        <div class="step-node active" vid="69"></div>
                        <div class="label" vid="70">IN TRANSIT</div>
                        <div style="font-size: 0.8rem;" vid="71">Pacific North</div>
                        <div class="time-stamp" style="opacity: 0.8; color: #333;" vid="72">CURRENT SECTOR</div>
                    </div>
                    <div class="timeline-step" vid="73">
                        <div class="step-node" vid="74"></div>
                        <div class="label" vid="75">EST. ARRIVAL</div>
                        <div style="font-size: 0.8rem;" vid="76">LA Gateway</div>
                        <div class="time-stamp" style="opacity: 0.8;" vid="77">29 OCT 18:00</div>
                    </div>
                    <div class="timeline-step" vid="78">
                        <div class="step-node" vid="79"></div>
                        <div class="label" vid="80">CUSTOMS</div>
                        <div style="font-size: 0.8rem;" vid="81">Port Clearance</div>
                        <div class="time-stamp" style="opacity: 0.8;" vid="82">PENDING</div>
                    </div>
                </div>
            </div>
        </main>

        <aside class="intelligence-panel" vid="83">
            <div class="ai-header" style="border-bottom: 1px solid #333; padding-bottom: 16px;" vid="84">
                <div class="label" style="color:var(--text-inv)" vid="85">LIVE NOTIFICATIONS</div>
                <div class="ai-status" style="display: flex; align-items: center; gap: 8px; font-family: var(--font-pixel); font-size: 0.8rem;" vid="86">
                    <div style="width: 10px; height: 10px; background: #00FF41; animation: blink 1s infinite;" vid="87"></div>
                    STREAMING
                </div>
            </div>

            <div class="notification-card alert" vid="88">
                <span class="time-stamp" vid="89">02 MINS AGO</span>
                <div class="label" style="color: #FF4100;" vid="90">WEATHER ADVISORY</div>
                <div style="margin-top: 4px;" vid="91">Strong headwinds detected in Sector 4. Fuel optimization reroute engaged.</div>
            </div>

            <div class="notification-card" vid="92">
                <span class="time-stamp" vid="93">14 MINS AGO</span>
                <div class="label" style="color: #888;" vid="94">AIS UPDATE</div>
                <div style="margin-top: 4px;" vid="95">MSC OSCAR speed stabilized at 18.4 knots. Signal strength nominal.</div>
            </div>

            <div class="notification-card" vid="96">
                <span class="time-stamp" vid="97">1 HOUR AGO</span>
                <div class="label" style="color: #888;" vid="98">DOCUMENTATION</div>
                <div style="margin-top: 4px;" vid="99">Digital Bill of Lading (B/L) verified by US Customs preliminary portal.</div>
            </div>

            <div class="notification-card" vid="100">
                <span class="time-stamp" vid="101">3 HOURS AGO</span>
                <div class="label" style="color: #888;" vid="102">SYSTEM</div>
                <div style="margin-top: 4px;" vid="103">Cargo temperature sensors calibrated. All 42 reefer units reporting stable at -18°C.</div>
            </div>

            <div class="spacer" vid="104"></div>

            <div style="border: 1px solid #333; padding: 16px;" vid="105">
                <div class="label" style="color: #888" vid="106">ESTIMATED REMAINING</div>
                <div class="pixel-text" style="font-size: 1.8rem; margin: 8px 0;" vid="107">05D 14H 22M</div>
                <div class="label" style="color: #888" vid="108">RELIABILITY INDEX</div>
                <div style="height: 4px; background: #222; margin-top: 8px;" vid="109">
                    <div style="width: 92%; height: 100%; background: #00FF41;" vid="110"></div>
                </div>
            </div>

            <button class="btn-primary" vid="111">
                GENERATE STATUS REPORT
            </button>
        </aside>
    </div>


</body></html>
```
