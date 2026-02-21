The code below contains a design. This design should be used to create a new app or be added to an existing one.

Look at the current open project to determine if a project exists. If no project is open, create a new Vite project then create this view in React after componentizing it.

If a project does exist, determine the framework being used and implement the design within that framework. Identify whether reusable components already exist that can be used to implement the design faithfully and if so use them, otherwise create new components. If other views already exist in the project, make sure to place the view in a sensible route and connect it to the other views.

Ensure the visual characteristics, layout, and interactions in the design are preserved with perfect fidelity.

Run the dev command so the user can see the app once finished.

```
<html lang="en" vid="0"><head vid="1">
    <meta charset="UTF-8" vid="2">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" vid="3">
    <title vid="4">Compliance Dashboard - Trade Optimizer</title>
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

        .content-section {
            padding: 24px;
            border-bottom: 1px solid var(--bg-dark);
        }

        .grid-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 24px;
        }

        .compliance-card {
            border: 1px solid var(--bg-dark);
            padding: 20px;
            background: rgba(255,255,255,0.2);
        }

        .status-pill {
            display: inline-block;
            padding: 2px 8px;
            font-family: var(--font-pixel);
            font-size: 0.7rem;
            border: 1px solid var(--bg-dark);
            margin-bottom: 12px;
        }

        .status-pill.alert {
            background: var(--bg-dark);
            color: #FF3B30;
        }

        .status-pill.check {
            background: var(--bg-dark);
            color: #4CD964;
        }

        .data-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 12px;
        }

        .data-table th {
            text-align: left;
            padding: 12px 16px;
            border-bottom: 2px solid var(--bg-dark);
            font-family: var(--font-pixel);
            font-size: 0.7rem;
            text-transform: uppercase;
        }

        .data-table td {
            padding: 12px 16px;
            border-bottom: 1px solid var(--bg-dark);
            font-size: 0.85rem;
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
            padding-bottom: 20px;
            border-bottom: 1px solid #333;
        }

        .doc-checklist {
            list-style: none;
            padding: 0;
            margin: 0;
        }

        .doc-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px 0;
            border-bottom: 1px solid #333;
        }

        .checkbox {
            width: 16px;
            height: 16px;
            border: 1px solid #E8E8E8;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .checkbox.checked::after {
            content: '■';
            font-size: 10px;
        }

        .tag {
            border: 1px solid var(--bg-dark);
            padding: 2px 6px;
            font-size: 0.65rem;
            text-transform: uppercase;
        }

        .tag.dark {
            border-color: #444;
            color: #AAA;
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

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: var(--bg-dark); }
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
                    <div class="nav-item" vid="30">
                        <span vid="31">ROUTE OPTIMIZER</span>
                        <span class="arrow-down" vid="32">↓</span>
                    </div>
                    <div class="nav-item active" vid="33">
                        <span vid="34">COMPLIANCE</span>
                        <span class="arrow-down" vid="35">←</span>
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
                    <h1 class="big-title" vid="45">TRADE<br vid="46"><span class="pixel-text" vid="47">COMPLIANCE</span></h1>
                </div>
                <div style="text-align: right;" vid="48">
                    <div class="label" vid="49">REGION</div>
                    <div vid="50">US-APAC CORRIDOR</div>
                </div>
            </header>

            <div class="content-section" vid="51">
                <div class="label" vid="52">REGULATORY REQUIREMENTS</div>
                <div class="grid-2" style="margin-top:16px;" vid="53">
                    <div class="compliance-card" vid="54">
                        <div class="status-pill check" vid="55">ACTIVE</div>
                        <h4 vid="56">Partner Gov Agencies (PGA)</h4>
                        <p style="font-size: 0.85rem; margin: 8px 0;" vid="57">Requires FDA notification for lithium-ion battery housing components. 48h lead time recommended.</p>
                        <div class="tag" vid="58">REF: 21 CFR 801</div>
                    </div>
                    <div class="compliance-card" vid="59">
                        <div class="status-pill alert" vid="60">WARNING</div>
                        <h4 vid="61">AD/CVD Scrutiny</h4>
                        <p style="font-size: 0.85rem; margin: 8px 0;" vid="62">Anti-dumping duties applicable to certain aluminum extrusions from CN region. Review alloy composition.</p>
                        <div class="tag" vid="63">REF: ITA-2023-01</div>
                    </div>
                </div>
            </div>

            <div class="content-section" vid="64">
                <div class="label" vid="65">RESTRICTED ITEMS LIST (BY HS SUBHEAD)</div>
                <table class="data-table" vid="66">
                    <thead vid="67">
                        <tr vid="68">
                            <th vid="69">HS CODE</th>
                            <th vid="70">DESCRIPTION</th>
                            <th vid="71">RESTRICTION TYPE</th>
                            <th vid="72">LICENSE</th>
                        </tr>
                    </thead>
                    <tbody vid="73">
                        <tr vid="74">
                            <td class="pixel-text" vid="75">8504.40.95</td>
                            <td vid="76">Static Converters (Dual Use)</td>
                            <td vid="77">ECCN Controlled</td>
                            <td vid="78"><span class="tag" vid="79">REQUIRED</span></td>
                        </tr>
                        <tr vid="80">
                            <td class="pixel-text" vid="81">8471.50.01</td>
                            <td vid="82">Encryption Processing Units</td>
                            <td vid="83">NSA Review Required</td>
                            <td vid="84"><span class="tag" vid="85">PENDING</span></td>
                        </tr>
                        <tr vid="86">
                            <td class="pixel-text" vid="87">2805.11.00</td>
                            <td vid="88">Lithium Concentrates</td>
                            <td vid="89">Strategic Material</td>
                            <td vid="90"><span class="tag" vid="91">EXEMPT</span></td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="content-section" style="border-bottom:none;" vid="92">
                <div class="label" vid="93">REQUIRED CERTIFICATIONS</div>
                <div class="grid-2" style="margin-top:16px;" vid="94">
                    <div style="border: 1px solid var(--bg-dark); padding: 16px;" vid="95">
                        <div class="label" vid="96">CERTIFICATE OF ORIGIN</div>
                        <div style="font-size: 0.9rem;" vid="97">Form A required for GSP eligibility. Must be signed by Chamber of Commerce.</div>
                    </div>
                    <div style="border: 1px solid var(--bg-dark); padding: 16px;" vid="98">
                        <div class="label" vid="99">SAFETY DATA SHEET (SDS)</div>
                        <div style="font-size: 0.9rem;" vid="100">Updated GHS version required for all hazardous battery shipments.</div>
                    </div>
                </div>
            </div>
        </main>

        
        <aside class="intelligence-panel" vid="101">
            <div class="ai-header" vid="102">
                <div class="label" style="color:var(--text-inv)" vid="103">COMPLIANCE CHECKLIST</div>
                <div class="pixel-text" style="font-size: 0.7rem; color: #4CD964;" vid="104">65% READY</div>
            </div>

            <div vid="105">
                <div class="label" style="color:#888; margin-bottom: 12px;" vid="106">DOCUMENT TRACKER</div>
                <ul class="doc-checklist" vid="107">
                    <li class="doc-item" vid="108">
                        <div class="checkbox checked" vid="109"></div>
                        <div vid="110">
                            <div style="font-size: 0.9rem;" vid="111">Commercial Invoice</div>
                            <div class="tag dark" vid="112">VERIFIED</div>
                        </div>
                    </li>
                    <li class="doc-item" vid="113">
                        <div class="checkbox checked" vid="114"></div>
                        <div vid="115">
                            <div style="font-size: 0.9rem;" vid="116">Packing List</div>
                            <div class="tag dark" vid="117">VERIFIED</div>
                        </div>
                    </li>
                    <li class="doc-item" vid="118">
                        <div class="checkbox" vid="119"></div>
                        <div vid="120">
                            <div style="font-size: 0.9rem;" vid="121">Certificate of Origin</div>
                            <div class="tag dark" style="color: #FF3B30; border-color: #500;" vid="122">MISSING</div>
                        </div>
                    </li>
                    <li class="doc-item" vid="123">
                        <div class="checkbox" vid="124"></div>
                        <div vid="125">
                            <div style="font-size: 0.9rem;" vid="126">TSCA Toxic Substance Control</div>
                            <div class="tag dark" vid="127">REQUIRED</div>
                        </div>
                    </li>
                </ul>
            </div>

            <div style="border: 1px solid #333; padding: 20px; margin-top: 12px;" vid="128">
                <div class="label" style="color:#888;" vid="129">AUDIT RISK LEVEL</div>
                <div class="pixel-text" style="font-size: 2.5rem; color: #E8E8E8; margin: 8px 0;" vid="130">LOW</div>
                <p style="font-size: 0.8rem; color: #888; margin: 0;" vid="131">Current entity rating 98/100. Previous 12 months: 0 infractions.</p>
            </div>

            <button class="btn-primary" vid="132">
                RUN FULL AUDIT →
            </button>
        </aside>
    </div>

</body></html>
```
