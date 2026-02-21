The code below contains a design. This design should be used to create a new app or be added to an existing one.

Look at the current open project to determine if a project exists. If no project is open, create a new Vite project then create this view in React after componentizing it.

If a project does exist, determine the framework being used and implement the design within that framework. Identify whether reusable components already exist that can be used to implement the design faithfully and if so use them, otherwise create new components. If other views already exist in the project, make sure to place the view in a sensible route and connect it to the other views.

Ensure the visual characteristics, layout, and interactions in the design are preserved with perfect fidelity.

Run the dev command so the user can see the app once finished.

```
<html lang="en" vid="0"><head vid="1">
    <meta charset="UTF-8" vid="2">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" vid="3">
    <title vid="4">Landed Cost Calculator | Trade Optimizer</title>
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

        .form-section {
            padding: 24px;
            border-bottom: 1px solid var(--bg-dark);
        }

        .form-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 16px;
        }

        .input-group {
            display: flex;
            flex-direction: column;
        }

        input, select {
            background: transparent;
            border: 1px solid var(--bg-dark);
            padding: 12px;
            font-family: var(--font-sans);
            font-size: 0.9rem;
            color: var(--text-main);
            border-radius: 0;
            outline: none;
        }

        .comparison-area {
            padding: 24px;
            flex-grow: 1;
        }

        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 16px;
        }

        .data-table th {
            text-align: left;
            padding: 12px 16px;
            border-bottom: 2px solid var(--bg-dark);
            font-family: var(--font-pixel);
            font-size: 0.75rem;
            text-transform: uppercase;
        }

        .data-table td {
            padding: 16px;
            border-bottom: 1px solid var(--bg-dark);
            font-size: 0.9rem;
        }

        .highlight-cell {
            background: rgba(0,0,0,0.03);
            font-weight: 600;
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

        .result-card {
            border: 1px solid #333;
            padding: 20px;
        }

        .metric-title {
            font-family: var(--font-pixel);
            font-size: 1.8rem;
            margin: 8px 0;
        }

        .breakdown-row {
            display: flex;
            justify-content: space-between;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid #333;
            font-size: 0.85rem;
        }

        .btn-primary {
            background: var(--bg-dark);
            color: var(--bg-canvas);
            border: none;
            padding: 16px;
            font-family: var(--font-pixel);
            text-transform: uppercase;
            font-size: 0.9rem;
            cursor: pointer;
            width: 100%;
            transition: background 0.2s;
        }

        .btn-primary:hover {
            background: #222;
        }

        .method-tag {
            font-family: var(--font-pixel);
            font-size: 0.65rem;
            padding: 2px 6px;
            border: 1px solid var(--bg-dark);
            margin-bottom: 4px;
            display: inline-block;
        }

        .spacer { flex: 1; }

        .tag {
            border: 1px solid var(--bg-dark);
            padding: 4px 8px;
            font-size: 0.7rem;
            text-transform: uppercase;
            display: inline-block;
        }

        .tag.best-value {
            background: #000;
            color: #C8C8C8;
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
                        <span vid="28">LANDED COST</span>
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
                    <div class="label" vid="44">ACTIVE MODULE</div>
                    <h1 class="big-title" vid="45">LANDED<br vid="46"><span class="pixel-text" vid="47">CALCULATOR</span></h1>
                </div>
                <div style="text-align: right;" vid="48">
                    <div class="label" vid="49">CURRENCY</div>
                    <div vid="50">USD ($) / 1.00</div>
                </div>
            </header>

            <section class="form-section" vid="51">
                <div class="label" vid="52">CARGO PARAMETERS</div>
                <div class="form-grid" vid="53">
                    <div class="input-group" vid="54">
                        <label class="label" vid="55">UNIT PRICE (FOB)</label>
                        <input type="text" value="42.50" vid="56">
                    </div>
                    <div class="input-group" vid="57">
                        <label class="label" vid="58">QUANTITY</label>
                        <input type="text" value="500" vid="59">
                    </div>
                    <div class="input-group" vid="60">
                        <label class="label" vid="61">HS CODE</label>
                        <input type="text" value="8517.62.00" vid="62">
                    </div>
                    <div class="input-group" vid="63">
                        <label class="label" vid="64">WEIGHT (KG)</label>
                        <input type="text" value="1,240" vid="65">
                    </div>
                </div>
            </section>

            <div class="comparison-area" vid="66">
                <div class="label" vid="67">METHOD COMPARISON MATRIX</div>
                <table class="data-table" vid="68">
                    <thead vid="69">
                        <tr vid="70">
                            <th vid="71">SHIPPING METHOD</th>
                            <th vid="72">FREIGHT</th>
                            <th vid="73">INSURANCE</th>
                            <th vid="74">CUSTOMS/FEES</th>
                            <th vid="75">TOTAL COST</th>
                            <th vid="76">UNIT COST</th>
                        </tr>
                    </thead>
                    <tbody vid="77">
                        <tr vid="78">
                            <td vid="79">
                                <div class="method-tag" vid="80">OCEAN_FCL</div><br vid="81">
                                20ft Standard Container
                            </td>
                            <td vid="82">$1,850.00</td>
                            <td vid="83">$125.00</td>
                            <td vid="84">$942.50</td>
                            <td class="highlight-cell" vid="85">$24,167.50</td>
                            <td vid="86">$48.33</td>
                        </tr>
                        <tr vid="87">
                            <td vid="88">
                                <div class="method-tag" vid="89">OCEAN_LCL</div><br vid="90">
                                Less than Container Load
                            </td>
                            <td vid="91">$640.00</td>
                            <td vid="92">$85.00</td>
                            <td vid="93">$1,012.00</td>
                            <td vid="94">$22,987.00</td>
                            <td vid="95">$45.97 <span class="tag best-value" vid="96">BEST</span></td>
                        </tr>
                        <tr vid="97">
                            <td vid="98">
                                <div class="method-tag" vid="99">AIR_EXPRESS</div><br vid="100">
                                Priority Cargo
                            </td>
                            <td vid="101">$4,200.00</td>
                            <td vid="102">$210.00</td>
                            <td vid="103">$890.00</td>
                            <td vid="104">$26,550.00</td>
                            <td vid="105">$53.10</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </main>

        <aside class="intelligence-panel" vid="106">
            <div class="ai-header" style="display: flex; justify-content: space-between; border-bottom: 1px solid #333; padding-bottom: 16px;" vid="107">
                <div class="label" style="color:var(--text-inv)" vid="108">COST ANALYSIS</div>
                <div class="label" style="color: #00FF41" vid="109">OPTIMIZED</div>
            </div>

            <div class="result-card" vid="110">
                <div class="label" style="color: #888" vid="111">EST. TOTAL LANDED</div>
                <div class="metric-title pixel-text" vid="112">$22,987.00</div>
                <div class="label" style="color: #888" vid="113">PER UNIT: $45.97</div>
            </div>

            <div class="result-card" vid="114">
                <div class="label" style="color: #888" vid="115">FEE BREAKDOWN (LCL)</div>
                <div class="breakdown-row" vid="116">
                    <span vid="117">FOB Value</span>
                    <span vid="118">$21,250.00</span>
                </div>
                <div class="breakdown-row" vid="119">
                    <span vid="120">Freight &amp; Logistics</span>
                    <span vid="121">$640.00</span>
                </div>
                <div class="breakdown-row" vid="122">
                    <span vid="123">Import Duty (3.2%)</span>
                    <span vid="124">$680.00</span>
                </div>
                <div class="breakdown-row" vid="125">
                    <span vid="126">MPF / HMF Fees</span>
                    <span vid="127">$112.00</span>
                </div>
                <div class="breakdown-row" vid="128">
                    <span vid="129">Inland Carriage</span>
                    <span vid="130">$220.00</span>
                </div>
                <div class="breakdown-row" style="font-weight: bold; border-top: 2px solid #555;" vid="131">
                    <span vid="132">Total Landed</span>
                    <span vid="133">$22,987.00</span>
                </div>
            </div>

            <div class="result-card" style="border-style: dashed; opacity: 0.8;" vid="134">
                <div class="label" style="color: #888" vid="135">RISK ADVISORY</div>
                <div style="font-size: 0.8rem; line-height: 1.4;" vid="136">
                    Current LCL congestion at Destination Port suggests +4 day delay. Ocean FCL remains the most stable timeline for Q4 delivery.
                </div>
            </div>

            <div class="spacer" vid="137"></div>

            <button class="btn-primary" vid="138">
                GENERATE QUOTE →
            </button>
        </aside>
    </div>


</body></html>
```
