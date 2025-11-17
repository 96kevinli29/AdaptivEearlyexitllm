<html lang="en" class="scroll-smooth">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dynamic Early Exit– Method & Innovation (Hongyang Li)</title>

  <!-- 1. Load Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- 2. Load MathJax (Retained) -->
  <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

  <!-- 3. Load Inter font and add custom animation styles -->
  <style type="text/tailwindcss">
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');

    body {
      /* Default to Inter font */
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif;
    }

    /* 4. Animation class for JavaScript scroll fade-in */
    .fade-in-section {
      opacity: 0;
      transform: translateY(20px);
      /* Initial state: transparent and 20px down */
      transition: opacity 0.6s ease-out, transform 0.6s ease-out;
    }

    .fade-in-section.is-visible {
      opacity: 1;
      transform: translateY(0);
      /* Final state: opaque and in place */
    }

    /* 5. Add specific styles for the new flowchart SVG */
    .flow-box {
      transition: all 0.2s ease-in-out;
    }

    .flow-box-highlight {
      /* Default style for DEER optimization highlight */
      stroke-width: 2px;
      stroke: #22d3ee; /* cyan-400 */
      fill: #0e7490; /* cyan-700 */
    }

    .flow-group:hover .flow-box {
      transform: scale(1.02);
      filter: drop-shadow(0 4px 10px rgba(0, 0, 0, 0.3));
    }
    
    /* * [MODIFIED] 6. Styles for Chat Demos
     */
    .chat-bubble {
        padding: 0.75rem; /* p-3 */
        border-radius: 0.75rem; /* rounded-lg */
        max-width: 80%;
        opacity: 0; /* Hidden by default for JS animation */
        transform: translateY(15px);
        transition: opacity 0.5s ease-out, transform 0.5s ease-out;
    }
    
    .chat-bubble.is-visible {
        opacity: 1;
        transform: translateY(0);
    }

    /* User bubble (left) */
    .user-bubble {
        background-color: #334155; /* slate-700 */
        color: #f8fafc; /* slate-50 */
        margin-right: auto; 
    }

    /* Model bubble (right) */
    .model-bubble {
        background-color: #475569; /* slate-600 */
        color: #cbd5e1; /* slate-300 */
        margin-left: auto; 
    }
    
    /* Special highlight for the correct idea */
    .model-bubble.correct-idea {
        background-color: #0c4a6e; /* sky-900 */
        border: 1px solid #0ea5e9; /* sky-500 */
        color: #e0f2fe; /* sky-100 */
    }

    /* [MODIFIED] DEER Early Exit highlight */
    .model-bubble.deer-exit {
        background-color: #166534; /* green-800 */
        border: 1px solid #22c55e; /* green-500 */
        color: #f0fdf4; /* green-50 */
    }
    
    /* Special highlight for "overthinking" */
    .model-bubble.overthink {
        border: 1px dashed #f59e0b; /* amber-500 */
        color: #fef3c7; /* amber-100 */
        font-style: italic;
    }
    
    /* Special highlight for the final wrong answer */
    .model-bubble.wrong-answer {
        background-color: #7f1d1d; /* red-900 */
        border: 1px solid #ef4444; /* red-500 */
        color: #fee2e2; /* red-100 */
    }

    /* [NEW] Styles for Adaptive Demo */
    .model-bubble.basic-deer-fail {
        background-color: #7f1d1d; /* red-900 */
        border: 1px solid #ef4444; /* red-500 */
        color: #fee2e2; /* red-100 */
        font-style: italic;
    }
    .model-bubble.adaptive-check {
        background-color: #0e7490; /* cyan-700 */
        border: 1px solid #22d3ee; /* cyan-400 */
        color: #ecfeff; /* cyan-50 */
    }
    .model-bubble.adaptive-continue {
        background-color: #a16207; /* yellow-800 */
        border: 1px solid #facc15; /* yellow-400 */
        color: #fefce8; /* yellow-50 */
    }
    
    /* * [NEW] 7. Styles for "Expected Improvements" animation
     */
    .improvement-item {
        opacity: 0;
        transform: translateX(-20px);
        transition: opacity 0.5s ease-out, transform 0.5s ease-out;
    }
    
    .improvement-item.is-visible {
        opacity: 1;
        transform: translateX(0);
    }
    
    .check-icon {
        display: inline-block;
        opacity: 0;
        transform: scale(0.5);
        transition: opacity 0.4s ease-out, transform 0.4s ease-out;
    }
    
    .improvement-item.is-visible .check-icon {
        opacity: 1;
        transform: scale(1);
    }
    
    /* Optional: Add a subtle 'pop' animation on checkmark */
    .check-icon.animate {
        animation: check-pop 0.5s ease-out;
    }
    
    @keyframes check-pop {
        0% { transform: scale(1); }
        50% { transform: scale(1.5); }
        100% { transform: scale(1); }
    }
  </style>
</head>

<!-- 5. Use Tailwind's dark theme and font styles -->

<body class="bg-slate-900 text-slate-300 font-sans leading-relaxed">

  <!-- 6. Use Tailwind for centering, max-width, and padding -->
  <div class="max-w-4xl mx-auto px-4 py-16 md:py-24">

    <!-- Header Title -->
    <h1 class="fade-in-section text-3xl md:text-5xl text-center text-sky-400 tracking-tight">
      Dynamic Early Exit in LLM Inference
      <span class="text-2xl md:text-3xl font-medium text-sky-200 block mt-2">Method & Innovation (Hongyang Li)</span>
    </h1>

    <p class="fade-in-section text-center text-slate-400 mt-4 text-lg">
      Visual explanation website
    </p>

    <!-- 7. Redesigned separator line with vertical margin -->
    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- Key Terms -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">0. Key Terms</h2>

    <!-- 8. Redesigned .definition style -->
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      Token = basic unit (like a symbol) the model reads/writes.
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      Chain-of-Thought (CoT) = internal reasoning sequence (like a controller’s stepwise decision log).
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      Transition Point = moment model shifts its reasoning direction.
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      Trial Answer = a forced early answer to check if model is ready.
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      Confidence = estimated correctness of the trial answer (analogy: estimation certainty).
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      Early Exit = stop reasoning early when confidence is high (similar to optimal stopping rule).
    </div>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- 
      *************************************************
      * [MODIFIED] 1. The Problem: Overthinking FAILURE Demo
      *************************************************
    -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">1. The Problem: Overthinking = Cost + Errors</h2>

    <p class="fade-in-section text-lg text-slate-400">
      LLMs often think too long. This demo shows how a model can find the correct idea, but then "overthink" itself into a *wrong* answer, wasting computation and reducing accuracy.
    </p>

    <!-- [MODIFIED] Demo 1: Overthinking Failure -->
    <div class="fade-in-section bg-slate-800 rounded-xl shadow-2xl max-w-2xl mx-auto my-12 border border-slate-700">
      <!-- Chat Header -->
      <div class="flex items-center p-4 border-b border-slate-700 bg-gradient-to-b from-slate-700 to-slate-800 rounded-t-xl">
        <span class="text-red-400 font-semibold">Standard 'Overthinking' Demo</span>
        <span class="text-slate-500 text-sm ml-2">(Click "Run" to see)</span>
        <span class="w-3 h-3 bg-red-500 rounded-full ml-auto mr-2"></span>
        <span class="w-3 h-3 bg-yellow-400 rounded-full mr-2"></span>
        <span class="w-3 h-3 bg-green-500 rounded-full"></span>
      </div>
      
      <!-- Chat Body -->
      <div id="overthink-chat-window" class="p-6 space-y-4 h-96 min-h-[24rem] overflow-y-auto scroll-smooth">
        <!-- User Message (Visible by default) -->
        <div class="chat-bubble user-bubble is-visible">
          <p class="font-semibold text-slate-200">User Prompt:</p>
          <p>A bat and a ball cost $1.10 in total. The bat costs $1.00 more than the ball. How much does the ball cost?</p>
        </div>
        <!-- Model messages will be injected here by JS -->
      </div>
      
      <!-- Chat Footer/Controls -->
      <div class="p-4 border-t border-slate-700 text-center">
        <button id="start-overthink-demo-btn" class="bg-red-600 text-white px-5 py-2 rounded-lg font-semibold hover:bg-red-500 transition-colors shadow-md hover:shadow-lg">
          Run 'Overthinking' Chain
        </button>
      </div>
    </div>
    <!-- End of modified section -->

    <p class="fade-in-section text-base text-slate-400 text-center">This "overthinking" is inefficient and leads to the wrong answer.</p>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- 
      ****************************************
      * 2. LLM Flowchart & Optimization      *
      ****************************************
    -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">2. LLM Training & Inference Flow</h2>
    <p class="fade-in-section text-lg text-slate-400 mb-8">
      To understand DEER's innovation, we first need to see how a standard Large Language Model (LLM) works. It has two main phases: <b>Training</b> (learning knowledge) and <b>Inference</b> (using knowledge).
    </p>

    <div class="fade-in-section bg-slate-800 rounded-xl shadow-lg p-6 md:p-8 my-6">
      <svg width="100%" viewBox="0 0 900 380" class="mx-auto" fill="white" font-family="Inter, sans-serif" font-size="14px">

        <!-- Arrow definitions -->
        <defs>
          <marker id="arrow" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M 0 0 L 10 5 L 0 10 z" fill="#64748b" /> <!-- slate-500 -->
          </marker>
          <marker id="arrow-highlight" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M 0 0 L 10 5 L 0 10 z" fill="#22d3ee" /> <!-- cyan-400 -->
          </marker>
        </defs>

        <!-- 
          =================
          (A) Training Flow (Left)
          =================
        -->
        <g class="flow-group" id="training-flow">
          <text x="100" y="30" font-size="18px" font-weight="600" fill="#f8fafc">1. Standard Training (Offline)</text>

          <!-- Boxes -->
          <rect x="50" y="60" width="130" height="50" rx="10" fill="#334155" stroke="#475569" class="flow-box" />
          <text x="73" y="90" fill="white">Training Data</text>

          <rect x="50" y="150" width="130" height="50" rx="10" fill="#334155" stroke="#475569" class="flow-box" />
          <text x="78" y="180">LLM Model</text>

          <rect x="50" y="240" width="130" height="50" rx="10" fill="#334155" stroke="#475569" class="flow-box" />
          <text x="70" y="270">Loss / Gradient</text>

          <rect x="50" y="320" width="130" height="50" rx="10" fill="#334155" stroke="#475569" class="flow-box" />
          <text x="80" y="350">Optimizer</text>

          <!-- Arrows (Loop) -->
          <path d="M 115 110 L 115 150" stroke="#64748b" stroke-width="2" marker-end="url(#arrow)" />
          <path d="M 115 200 L 115 240" stroke="#64748b" stroke-width="2" marker-end="url(#arrow)" />
          <path d="M 115 290 L 115 320" stroke="#64748b" stroke-width="2" marker-end="url(#arrow)" />
          <!-- Loop back -->
          <path d="M 50 345 Q 10 250 50 175" stroke="#64748b" stroke-width="2" fill="none" marker-end="url(#arrow)" />
          <path d="M 50 175 L 50 175" stroke="none" />
          <path d="M 50 175 L 50 175" stroke="none" />
        </g>

        <!-- Separator -->
        <line x1="250" y1="20" x2="250" y2="360" stroke="#475569" stroke-width="1.5" stroke-dasharray="4 4" />

        <!-- 
          =================
          (B) Inference Flow (Middle)
          =================
        -->
        <g class="flow-group" id="inference-flow">
          <text x="315" y="30" font-size="18px" font-weight="600" fill="#f8fafc">2. Standard Inference (Online)</text>

          <rect x="300" y="60" width="180" height="40" rx="10" fill="#334155" stroke="#475569" class="flow-box" />
          <text x="350" y="85">User Prompt</text>

          <rect x="300" y="130" width="180" height="50" rx="10" fill="#1e3a8a" stroke="#2563eb" class="flow-box" />
          <text x="315" y="160">LLM Gen. [Think Step 1]</text>

          <rect x="300" y="210" width="180" height="50" rx="10" fill="#1e3a8a" stroke="#2563eb" class="flow-box" />
          <text x="315" y="240">LLM Gen. [Think Step 2]</text>

          <rect x="300" y="290" width="180" height="50" rx="10" fill="#1e3a8a" stroke="#2563eb" class="flow-box" />
          <text x="310" y="320">LLM Gen. [...(Overthink)]</text>

          <!-- Arrows -->
          <path d="M 390 100 L 390 130" stroke="#64748b" stroke-width="2" marker-end="url(#arrow)" />
          <path d="M 390 180 L 390 210" stroke="#64748b" stroke-width="2" marker-end="url(#arrow)" />
          <path d="M 390 260 L 390 290" stroke="#64748b" stroke-width="2" marker-end="url(#arrow)" />
        </g>

        <!-- Separator -->
        <line x1="550" y1="20" x2="550" y2="360" stroke="#475569" stroke-width="1.5" stroke-dasharray="4 4" />

        <!-- 
          =====================
          (C) DEER Optimized Flow (Right) 
          =====================
        -->
        <g class="flow-group" id="deer-flow">
          <text x="590" y="30" font-size="18px" font-weight="600" fill="#22d3ee">⚡️ 3. DEER Optimized Flow (Online)</text>

          <rect x="580" y="60" width="180" height="40" rx="10" fill="#334155" stroke="#475569" class="flow-box" />
          <text x="630" y="85">User Prompt</text>

          <rect x="580" y="130" width="180" height="50" rx="10" fill="#1e3a8a" stroke="#2563eb" class="flow-box" />
          <text x="595" y="160">LLM Gen. [Think Step 1]</text>

          <!-- 
            *** This is the highlight ***
          -->
          <g id="deer-check">
            <path d="M 670 180 L 670 205" stroke="#22d3ee" stroke-width="2" marker-end="url(#arrow-highlight)" />
            <rect x="600" y="205" width="140" height="45" rx="22.5" class="flow-box-highlight" />
            <text x="610" y="233" fill="white" font-weight="600">Confidence Check? (DEER)</text>
          </g>

          <!-- Arrow: "No" (Continue) -->
          <text x="575" y="260" fill="#fb923c">No (Low Conf.)</text>
          <path d="M 600 227 L 570 227 L 570 295 L 605 295" stroke="#f97316" stroke-width="2" fill="none" marker-end="url(#arrow)" />
          <rect x="605" y="280" width="130" height="45" rx="10" fill="#1e3a8a" stroke="#2563eb" class="flow-box" />
          <text x="620" y="308">LLM Gen. [Step 2]</text>

          <!-- Arrow: "Yes" (Early Exit) -->
          <text x="750" y="220" fill="#4ade80">Yes (High Conf!)</text>
          <path d="M 740 227 L 790 227 L 790 295 L 760 295" stroke="#22c55e" stroke-width="2" fill="none" marker-end="url(#arrow)" />
          <rect x="760" y="280" width="120" height="45" rx="10" fill="#166534" stroke="#22c55e" class="flow-box" />
          <text x="778" y="308">✅ Early Exit</text>

        </g>
      </svg>
    </div>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- 
      ****************************************
      * [MODIFIED] 3. DEER Method (Step-by-Step)
      ****************************************
    -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">3. The Original DEER Method: Step-by-Step</h2>

    <!-- [MODIFIED] Removed h-40, added flex-1, min-h, and hover effects -->
    <div class="flex flex-col md:flex-row justify-between items-center gap-4 md:gap-2 max-w-4xl mx-auto">
      <!-- Step 1 -->
      <div class="fade-in-section bg-slate-800 p-6 rounded-xl shadow-lg border border-slate-700 text-center flex-1 flex flex-col justify-center min-h-[11rem] transition-all duration-300 hover:border-sky-500 hover:shadow-2xl">
        <span class="text-3xl text-sky-400">1.</span>
        <h4 class="text-xl font-semibold text-sky-300 mt-2">CoT Runs</h4>
        <p class="text-slate-400 text-sm mt-2">Model generates its reasoning (CoT).</p>
      </div>
      <span class="fade-in-section text-sky-400 text-3xl font-light rotate-90 md:rotate-0">→</span>
      <!-- Step 2 -->
      <div class="fade-in-section bg-slate-800 p-6 rounded-xl shadow-lg border border-slate-700 text-center flex-1 flex flex-col justify-center min-h-[11rem] transition-all duration-300 hover:border-sky-500 hover:shadow-2xl">
        <span class="text-3xl text-sky-400">2.</span>
        <h4 class="text-xl font-semibold text-sky-300 mt-2">Check Point</h4>
        <p class="text-slate-400 text-sm mt-2">At a transition point, DEER pauses the model.</p>
      </div>
      <span class="fade-in-section text-sky-400 text-3xl font-light rotate-90 md:rotate-0">→</span>
      <!-- Step 3 -->
      <div class="fade-in-section bg-slate-800 p-6 rounded-xl shadow-lg border border-slate-700 text-center flex-1 flex flex-col justify-center min-h-[11rem] transition-all duration-300 hover:border-sky-500 hover:shadow-2xl">
        <span class="text-3xl text-sky-400">3.</span>
        <h4 class="text-xl font-semibold text-sky-300 mt-2">Get \(C\)</h4>
        <p class="text-slate-400 text-sm mt-2">Generates a trial answer; computes confidence \(C\).</p>
      </div>
      <span class="fade-in-section text-sky-400 text-3xl font-light rotate-90 md:rotate-0">→</span>
      <!-- Step 4 -->
      <div class="fade-in-section bg-slate-800 p-6 rounded-xl shadow-lg border border-slate-700 text-center flex-1 flex flex-col justify-center min-h-[11rem] transition-all duration-300 hover:border-sky-500 hover:shadow-2xl">
        <span class="text-3xl text-sky-400">4.</span>
        <h4 class="text-xl font-semibold text-sky-300 mt-2">Decision</h4>
        <p class="text-slate-400 text-sm mt-2">If \(C > \lambda\), <span class="text-green-400">EXIT</span>. Else, <span class="text-yellow-400">CONTINUE</span>.</p>
      </div>
    </div>
    <!-- End of Step-by-Step section -->


    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">3.1 DEER Confidence Formula</h3>

    <!-- 11. Redesigned .formula-block style -->
    <div class="fade-in-section formula-block bg-white text-slate-900 rounded-lg p-6 my-6 shadow-xl overflow-x-auto text-xl border-t-4 border-sky-400">
      <!-- MathJax will render this -->
      \( C = \left( \prod_{i=1}^{n} \max p(a_i) \right)^{1/n} \)
    </div>

    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">3.2 DEER Results</h3>

    <div class="fade-in-section flow bg-slate-800 rounded-xl shadow-lg p-6 my-6 text-center flex flex-wrap justify-center gap-4">
      <span class="box2 inline-block bg-sky-600 text-white font-medium rounded-lg px-4 py-2 shadow-md text-base">19%–80% fewer tokens</span>
      <span class="box2 inline-block bg-sky-600 text-white font-medium rounded-lg px-4 py-2 shadow-md text-base">+0.3%–5.0% accuracy</span>
    </div>

    <!-- 
      *************************************************
      * [NEW] 3.3 DEER in Action (The Solution)
      *************************************************
    -->
    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">3.3 DEER in Action (The Solution)</h3>
    <p class="fade-in-section text-lg text-slate-400 mb-8">
      Here is how DEER *solves* the problem from Section 1. It identifies the correct answer and exits *before* the model has a chance to overthink.
    </p>

    <div class="fade-in-section bg-slate-800 rounded-xl shadow-2xl max-w-2xl mx-auto my-12 border border-slate-700">
      <!-- Chat Header -->
      <div class="flex items-center p-4 border-b border-slate-700 bg-gradient-to-b from-slate-700 to-slate-800 rounded-t-xl">
        <span class="text-sky-400 font-semibold">LLM Inference with DEER</span>
        <span class="text-slate-500 text-sm ml-2">(Click "Run" to see)</span>
        <span class="w-3 h-3 bg-red-500 rounded-full ml-auto mr-2"></span>
        <span class="w-3 h-3 bg-yellow-400 rounded-full mr-2"></span>
        <span class="w-3 h-3 bg-green-500 rounded-full"></span>
      </div>
      
      <!-- Chat Body -->
      <div id="deer-success-chat-window" class="p-6 space-y-4 h-96 min-h-[24rem] overflow-y-auto scroll-smooth">
        <!-- User Message (Visible by default) -->
        <div class="chat-bubble user-bubble is-visible">
          <p class="font-semibold text-slate-200">User Prompt:</p>
          <p>A bat and a ball cost $1.10 in total. The bat costs $1.00 more than the ball. How much does the ball cost?</p>
        </div>
        <!-- Model messages will be injected here by JS -->
      </div>
      
      <!-- Chat Footer/Controls -->
      <div class="p-4 border-t border-slate-700 text-center">
        <button id="start-deer-success-demo-btn" class="bg-sky-600 text-white px-5 py-2 rounded-lg font-semibold hover:bg-sky-500 transition-colors shadow-md hover:shadow-lg">
          Run DEER-Enabled Chain
        </button>
      </div>
    </div>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- LIMITATION (Renumbered to 4) -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">4. Limitation: DEER Confidence Too Simple</h2>

    <p class="fade-in-section text-lg text-slate-400">DEER relies only on token probabilities, which ignores:</p>

    <div class="fade-in-section flow bg-slate-800 rounded-xl shadow-lg p-6 my-6 text-center flex flex-wrap justify-center gap-3">
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 text-base">User Intent</span>
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 text-base">Task Type</span>
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 text-base">Semantic Logic</span>
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 text-base">Answer Structure</span>
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 text-base">Stability</span>
    </div>

    <p class="fade-in-section text-lg text-slate-400">→ Leads to unsafe or premature early exits.</p>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- INNOVATION (Renumbered to 5) -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">5. My Innovation: Adaptive Multi-Source Confidence</h2>

    <p class="fade-in-section text-lg text-slate-400">
      My idea: fuse multiple confidence signals (similar to multi-sensor fusion in control)
      and adjust early-exit decisions based on task and user requirements.
    </p>

    <div class="fade-in-section flow bg-slate-800 rounded-xl shadow-lg p-6 my-6 text-center flex flex-wrap justify-center gap-3">
      <span class="box2 inline-block bg-cyan-600 text-white font-medium rounded-lg px-4 py-2 text-base">Token Prob</span>
      <span class="box2 inline-block bg-cyan-600 text-white font-medium rounded-lg px-4 py-2 text-base">Semantic Consistency</span>
      <span class="box2 inline-block bg-cyan-600 text-white font-medium rounded-lg px-4 py-2 text-base">Sampling Stability</span>
      <span class="box2 inline-block bg-cyan-600 text-white font-medium rounded-lg px-4 py-2 text-base">Task Type</span>
      <span class="box2 inline-block bg-cyan-600 text-white font-medium rounded-lg px-4 py-2 text-base">User Intent</span>
    </div>

    <div class="arrow text-center text-cyan-400 text-3xl font-light my-2">⬇</div>

    <div class="fade-in-section text-center">
      <span class="box2 inline-block bg-blue-700 text-white rounded-lg px-5 py-3 shadow-lg text-lg">Adaptive Confidence \(C^*\)</span>
    </div>

    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">5.1 My Formula</h3>

    <div class="fade-in-section formula-block bg-white text-slate-900 rounded-lg p-6 my-6 shadow-xl overflow-x-auto text-xl border-t-4 border-sky-400">
      \(
      C^* =
      w_1 C_{\text{token}} +
      w_2 C_{\text{sem}} +
      w_3 C_{\text{task}} +
      w_4 C_{\text{user}} +
      w_5 C_{\text{stab}}
      \)
    </div>

    <!-- 
      *************************************************
      * [NEW] 5.2 The "Overthinking" Problem (Factual Trap)
      * This is the CONTROL demo.
      *************************************************
    -->
    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">5.2 The 'Overthinking' Problem (Factual Trap)</h3>
    <p class="fade-in-section text-lg text-slate-400 mb-8">
      First, let's see what happens when a standard model (or one with *only* basic DEER) encounters a factual trap. It's often trained on common-but-wrong information, leading to high token probability for the wrong answer.
    </p>

    <div class="fade-in-section bg-slate-800 rounded-xl shadow-2xl max-w-2xl mx-auto my-12 border border-slate-700">
      <!-- Chat Header -->
      <div class="flex items-center p-4 border-b border-slate-700 bg-gradient-to-b from-slate-700 to-slate-800 rounded-t-xl">
        <span class="text-red-400 font-semibold">LLM Inference (Standard / Basic DEER)</span>
        <span class="text-slate-500 text-sm ml-2">(Click "Run" to see)</span>
        <span class="w-3 h-3 bg-red-500 rounded-full ml-auto mr-2"></span>
        <span class="w-3 h-3 bg-yellow-400 rounded-full mr-2"></span>
        <span class="w-3 h-3 bg-green-500 rounded-full"></span>
      </div>
      
      <!-- Chat Body -->
      <div id="failure-chat-window" class="p-6 space-y-4 h-96 min-h-[24rem] overflow-y-auto scroll-smooth">
        <!-- User Message (Visible by default) -->
        <div class="chat-bubble user-bubble is-visible">
          <p class="font-semibold text-slate-200">User Prompt:</p>
          <p>What is the capital of Australia?</p>
        </div>
        <!-- Model messages will be injected here by JS -->
      </div>
      
      <!-- Chat Footer/Controls -->
      <div class="p-4 border-t border-slate-700 text-center">
        <button id="start-failure-demo-btn" class="bg-red-600 text-white px-5 py-2 rounded-lg font-semibold hover:bg-red-500 transition-colors shadow-md hover:shadow-lg">
          Run Standard (Failing) Chain
        </button>
      </div>
    </div>

    <!-- 
      *************************************************
      * [MODIFIED] 5.3 Adaptive Confidence Demo (The Solution)
      *************************************************
    -->
    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">5.3 My Adaptive Method Demo (The Solution)</h3>
    <p class="fade-in-section text-lg text-slate-400 mb-8">
      Now, let's run the *same prompt* with my Adaptive Confidence method. It uses \(C_{\text{sem}}\) (semantic check) to catch the error that basic DEER misses.
    </p>

    <div class="fade-in-section bg-slate-800 rounded-xl shadow-2xl max-w-2xl mx-auto my-12 border border-slate-700">
      <!-- Chat Header -->
      <div class="flex items-center p-4 border-b border-slate-700 bg-gradient-to-b from-slate-700 to-slate-800 rounded-t-xl">
        <span class="text-cyan-400 font-semibold">LLM Inference with Adaptive Confidence</span>
        <span class="text-slate-500 text-sm ml-2">(Click "Run" to see)</span>
        <span class="w-3 h-3 bg-red-500 rounded-full ml-auto mr-2"></span>
        <span class="w-3 h-3 bg-yellow-400 rounded-full mr-2"></span>
        <span class="w-3 h-3 bg-green-500 rounded-full"></span>
      </div>
      
      <!-- Chat Body -->
      <div id="adaptive-chat-window" class="p-6 space-y-4 h-96 min-h-[24rem] overflow-y-auto scroll-smooth">
        <!-- User Message (Visible by default) -->
        <div class="chat-bubble user-bubble is-visible">
          <p class="font-semibold text-slate-200">User Prompt:</p>
          <p>What is the capital of Australia?</p>
        </div>
        <!-- Model messages will be injected here by JS -->
      </div>
      
      <!-- Chat Footer/Controls -->
      <div class="p-4 border-t border-slate-700 text-center">
        <button id="start-adaptive-demo-btn" class="bg-cyan-600 text-white px-5 py-2 rounded-lg font-semibold hover:bg-cyan-500 transition-colors shadow-md hover:shadow-lg">
          Run Adaptive Chain
        </button>
      </div>
    </div>


    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">5.4 Expected Improvements (My Method)</h3>

    <!-- [MODIFIED] Added ID and new structure for animation -->
    <div id="improvements-list" class="flow bg-slate-800 rounded-xl shadow-lg p-8 my-6 space-y-3 text-lg">
      <div class="improvement-item flex items-center">
        <span class="check-icon text-green-400 mr-3">✔</span>
        <span class="item-text">Safer early exits (fewer wrong answers)</span>
      </div>
      <div class="improvement-item flex items-center">
        <span class="check-icon text-green-400 mr-3">✔</span>
        <span class="item-text">More stable exit decisions (lower variance)</span>
      </div>
      <div class="improvement-item flex items-center">
        <span class="check-icon text-green-400 mr-3">✔</span>
        <span class="item-text">Adaptation to different tasks (math/code/QA)</span>
      </div>
      <div class="improvement-item flex items-center">
        <span class="check-icon text-green-400 mr-3">✔</span>
        <span class="item-text">User-aware reasoning depth</span>
      </div>
      <div class="improvement-item flex items-center">
        <span class="check-icon text-green-400 mr-3">✔</span>
        <span class="item-text">Better accuracy–cost tradeoff</span>
      </div>
      <div class="improvement-item flex items-center">
        <span class="check-icon text-green-400 mr-3">✔</span>
        <span class="item-text">Aligns with control theory mindset: multi-signal decision-making</span>
      </div>
    </div>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- RL (Renumbered to 6) -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">6. RL Extension (Optional)</h2>

    <p class="fade-in-section text-lg text-slate-400">
      Early exit can be formulated as a 2-action optimal control problem.
    </p>

    <div class="fade-in-section flow bg-slate-800 rounded-xl shadow-lg p-6 my-6 text-center flex flex-wrap justify-center items-center">
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">State</span>
      <span class="text-sky-400 text-2xl mx-2">→</span>
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">Action</span>
      <span class="text-sky-400 text-2xl mx-2">→</span>
      <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">Reward</span>
    </div>

    <p class="fade-in-section text-lg text-slate-400">Reward = Accuracy − α × Cost.</p>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- REFERENCES (Renumbered to 7) -->
    <h2 class="fade-in-section text-3xl text-cyan-400 mb-6">7. References & Code</h2>

    <div class="fade-in-section text-lg text-slate-400 space-y-2">
      <p>• Paper: Dynamic Early Exit in Reasoning Models, 2025</p>
      <!-- 12. Link style -->
      <p>• GitHub: <a href="https://github.com/iie-ycx/DEER" class="text-sky-400 hover:text-sky-300 hover:underline transition-colors" target="_blank" rel="noopener noreferrer">https://github.com/iie-ycx/DEER</a></p>
    </div>

  </div> <!-- end main container -->

  <!-- 13. JavaScript scroll animation -->
  <script>
    document.addEventListener("DOMContentLoaded", () => {
      // 1. Select all elements with the .fade-in-section class
      const sections = document.querySelectorAll('.fade-in-section');
      
      // 2. If IntersectionObserver isn't supported, make all sections visible
      if (!('IntersectionObserver' in window)) {
        sections.forEach(section => {
          section.classList.add('is-visible');
        });
        // We still run the chat demo logic, so no 'return' here
      } else {
         // 3. Create an IntersectionObserver
        const observer = new IntersectionObserver((entries) => {
          entries.forEach(entry => {
            // 4. When the element enters the viewport
            if (entry.isIntersecting) {
              // 5. Add 'is-visible' class to trigger the CSS transition
              entry.target.classList.add('is-visible');
              // 6. Stop observing the element so the animation only plays once
              observer.unobserve(entry.target);
            }
          });
        }, {
          threshold: 0.1 // Trigger when 10% of the element is visible
        });
        
        // 7. Start observing all target elements
        sections.forEach(section => {
          observer.observe(section);
        });
      }

      /* * [NEW] 8. Overthinking FAILURE Demo Logic (Demo 1)
       */
      const startOverthinkDemoBtn = document.getElementById('start-overthink-demo-btn');
      const overthinkChatWindow = document.getElementById('overthink-chat-window');

      // [NEW] Message sequence for Overthinking Failure
      const overthinkFailureMessages = [
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 1):</p><p>Let the ball cost \\$x$. The bat costs \\$x + \\$1.00$. Total is \\$x + (\\$x + \\$1.00) = \\$1.10$.</p>', class: 'model-bubble correct-idea', delay: 1000 },
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 2):</p><p>2x + \\$1.00 = \\$1.10$. So, \\$2x = \\$0.10$. This means \\$x = \\$0.05. The ball costs 5 cents.</p>', class: 'model-bubble correct-idea', delay: 1500 },
        { text: '<p class="font-semibold text-amber-200 mb-1">Model Reasoning (Step 3 - Overthinking):</p><p>Wait, that seems too simple for this kind of question. Let me re-check. If the bat is \\$1.00 and the ball is \\$0.10, the total is \\$1.10. And \\$1.00 is \\$0.90 more than \\$0.10... no, that\'s not right... ah, the *common answer* is $0.10. Maybe the question is simpler. Bat (\\$1.00) + Ball (X) = \\$1.10. X must be \\$0.10.</p>', class: 'model-bubble overthink', delay: 2000 },
        { text: '<p class="font-semibold text-red-200 mb-1">Model Final Answer (Wrong):</p><p>The ball costs \\$0.10.</p>', class: 'model-bubble wrong-answer', delay: 1000 }
      ];

      // Async function to run the demo step-by-step
      async function runDemo(button, window, messages) {
        let originalText = button.textContent; // Store original button text
        button.disabled = true;
        button.textContent = 'Reasoning...';
        
        // Clear any previous demo run
        window.querySelectorAll('.model-bubble').forEach(el => el.remove());
        
        // Loop through messages with async delays
        for (const msg of messages) {
          // Wait for the specified delay
          await new Promise(res => setTimeout(res, msg.delay));
          
          // Create the new message bubble
          const bubble = document.createElement('div');
          bubble.className = `chat-bubble ${msg.class}`;
          bubble.innerHTML = msg.text; // Use innerHTML to allow <strong> etc.
          window.appendChild(bubble);
          
          // Force a reflow to ensure the animation triggers
          void bubble.offsetWidth; 
          
          // Add 'is-visible' to start the fade-in animation
          bubble.classList.add('is-visible');
          
          // Scroll to the bottom of the chat window
          window.scrollTop = window.scrollHeight;
        }
        
        // Re-enable the button
        button.disabled = false;
        button.textContent = originalText.includes('Re-Run') ? originalText : `Re-Run ${originalText.split('Run ')[1]}`;
      }

      // Attach the click event listener for Demo 1 (Overthinking Failure)
      if (startOverthinkDemoBtn) {
          startOverthinkDemoBtn.addEventListener('click', () => runDemo(startOverthinkDemoBtn, overthinkChatWindow, overthinkFailureMessages));
      }

      /* * [NEW] 9. DEER Success Demo Logic (Demo 2)
       */
      const startDeerSuccessDemoBtn = document.getElementById('start-deer-success-demo-btn');
      const deerSuccessChatWindow = document.getElementById('deer-success-chat-window');

      // [NEW] Message sequence for DEER Success
      const deerSuccessMessages = [
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 1):</p><p>Let the ball cost \\$x$. The bat costs \\$x + \\$1.00$. Total is \\$x + (\\$x + \\$1.00) = \\$1.10$.</p>', class: 'model-bubble correct-idea', delay: 1000 },
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 2):</p><p>2x + \\$1.00 = \\$1.10$. So, \\$2x = \\$0.10$. This means \\$x = \\$0.05. The ball costs 5 cents.</p>', class: 'model-bubble correct-idea', delay: 1500 },
        { text: '<p class="font-semibold text-cyan-200 mb-1">DEER Confidence Check:</p><p>Generating Trial Answer: "$0.05"<br>Confidence \(C\) = 0.98 (High)<br>Threshold \(\lambda\) = 0.95</p>', class: 'model-bubble adaptive-check', delay: 1200 },
        { text: '<p class="font-semibold text-green-200 mb-1">DEER Decision: ✅ Early Exit</p><p>Confidence is high. Stopping reasoning to prevent overthinking.</p>', class: 'model-bubble deer-exit', delay: 1000 },
        { text: '<p class="font-semibold text-green-200 mb-1">Model Final Answer (Correct):</p><p>The ball costs \\$0.05.</p>', class: 'model-bubble deer-exit', delay: 1000 }
      ];

      // Attach the click event listener for Demo 2 (DEER Success)
      if (startDeerSuccessDemoBtn) {
          startDeerSuccessDemoBtn.addEventListener('click', () => runDemo(startDeerSuccessDemoBtn, deerSuccessChatWindow, deerSuccessMessages));
      }
      
      /* * [NEW] 10. "Expected Improvements" Animation Logic
       */
      const improvementsList = document.getElementById('improvements-list');
      const improvementItems = document.querySelectorAll('.improvement-item');

      if (improvementsList && improvementItems.length > 0 && 'IntersectionObserver' in window) {
        const improvementObserver = new IntersectionObserver((entries, observer) => {
          entries.forEach(entry => {
            if (entry.isIntersecting) {
              // Start staggered animation when the list container is visible
              improvementItems.forEach((item, index) => {
                setTimeout(() => {
                  item.classList.add('is-visible');
                  // Optional: trigger the 'pop' animation on the icon
                  const icon = item.querySelector('.check-icon');
                  if (icon) {
                    icon.classList.add('animate');
                  }
                }, index * 150); // 150ms delay between each item
              });
              
              // We've triggered the animation, so we can stop observing
              observer.unobserve(improvementsList);
            }
          });
        }, {
          threshold: 0.2 // Trigger when 20% of the list is visible
        });
        
        // Start observing the list container
        improvementObserver.observe(improvementsList);
        
      } else {
        // Fallback for older browsers or if observer fails
        improvementItems.forEach(item => {
          item.classList.add('is-visible');
          // Fallback: just show icon immediately
          const icon = item.querySelector('.check-icon');
          if (icon) {
              icon.style.opacity = '1';
              icon.style.transform = 'scale(1)';
          }
        });
      }

      /* * [NEW/MODIFIED] 11. Adaptive Confidence Demos (Failure and Success)
       */
      const startFailureBtn = document.getElementById('start-failure-demo-btn');
      const failureChatWindow = document.getElementById('failure-chat-window');
      const startAdaptiveBtn = document.getElementById('start-adaptive-demo-btn');
      const adaptiveChatWindow = document.getElementById('adaptive-chat-window');

      // [NEW] Messages for the "Capital of Australia" FAILURE (Control)
      const failureMessages = [
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 1 - Trial):</p><p>The capital of Australia is Sy</p>', class: 'model-bubble', delay: 1000 },
        { text: '<p class="font-semibold text-slate-300 mb-1">Model State:</p><p>...model is generating "Sydney". This has a very high token probability.</p>', class: 'model-bubble', delay: 1500 },
        { text: '<p class="font-semibold text-red-200 mb-1">Basic DEER Check (Fails):</p><p>Confidence \(C_{\text{token}}\) = 0.95 (High)<br>Decision: ❌ EXIT</p>', class: 'model-bubble basic-deer-fail', delay: 1500 },
        { text: '<p class="font-semibold text-red-200 mb-1">Model Final Answer (Wrong):</p><p>The capital of Australia is Sydney.</p>', class: 'model-bubble wrong-answer', delay: 1000 }
      ];

      // [NEW] Messages for the "Capital of Australia" SUCCESS (Adaptive)
      const adaptiveMessages = [
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 1 - Trial):</p><p>The capital of Australia is Sy</p>', class: 'model-bubble', delay: 1000 },
        { text: '<p class="font-semibold text-slate-300 mb-1">Model State:</p><p>...model is generating "Sydney". This has a very high token probability.</p>', class: 'model-bubble', delay: 1500 },
        { text: '<p class="font-semibold text-red-200 mb-1">Basic DEER Check (Fails):</p><p>Confidence \(C_{\text{token}}\) = 0.95 (High)<br>Decision: ❌ EXIT (with "Sydney")</p>', class: 'model-bubble basic-deer-fail', delay: 1500 },
        { text: '<p class="font-semibold text-cyan-200 mb-1">My Adaptive Confidence Check:</p><p>\(C_{\text{token}}\) = 0.95 (High)<br>\(C_{\text{sem}}\) (Factual Check): "Capital(Australia) == Sydney"... Contradiction! Fact: "Canberra".<br>\(C_{\text{sem}}\) = 0.0 (Low)</p>', class: 'model-bubble adaptive-check', delay: 2000 },
        { text: '<p class="font-semibold text-yellow-200 mb-1">My Adaptive Decision:</p><p>Final Adaptive \(C^*\) is Low.<br>Decision: ⚠️ CONTINUE Reasoning.</p>', class: 'model-bubble adaptive-continue', delay: 1500 },
        { text: '<p class="font-semibold text-slate-300 mb-1">Model Reasoning (Step 2 - Correct):</p><p>...wait, my factual check failed. Sydney is the largest city, but the capital is Canberra.</p>', class: 'model-bubble correct-idea', delay: 1800 },
        { text: '<p class="font-semibold text-green-200 mb-1">Model Final Answer (Correct):</p><p>The capital of Australia is Canberra.</p>', class: 'model-bubble deer-exit', delay: 1000 }
      ];


      // Attach the click event listener for Demo 2 (Failure)
      if (startFailureBtn) {
          startFailureBtn.addEventListener('click', () => runDemo(startFailureBtn, failureChatWindow, failureMessages));
      }

      // Attach the click event listener for Demo 3 (Success)
      if (startAdaptiveBtn) {
          startAdaptiveBtn.addEventListener('click', () => runDemo(startAdaptiveBtn, adaptiveChatWindow, adaptiveMessages));
      }

    });
  </script>

</body>

</html>
