<!DOCTYPE html>
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
  </style>
</head>

<!-- 5. Use Tailwind's dark theme and font styles -->

<body class="bg-slate-900 text-slate-300 font-sans leading-relaxed">

  <!-- 6. Use Tailwind for centering, max-width, and padding -->
  <div class="max-w-4xl mx-auto px-4 py-16 md:py-24">

    <!-- Header Title -->
    <h1 class="fade-in-section text-3xl md:text-5xl font-bold text-center text-sky-400 tracking-tight">
      Dynamic Early Exit in LLM Inference
      <span class="text-2xl md:text-3xl font-medium text-sky-200 block mt-2">Method & Innovation (Hongyang Li)</span>
    </h1>

    <p class="fade-in-section text-center text-slate-400 mt-4 text-lg">
      Visual explanation website
    </p>

    <!-- 7. Redesigned separator line with vertical margin -->
    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- Key Terms -->
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">0. Key Terms</h2>

    <!-- 8. Redesigned .definition style -->
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      <b>Token</b> = basic unit (like a symbol) the model reads/writes.
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      <b>Chain-of-Thought (CoT)</b> = internal reasoning sequence (like a controller’s stepwise decision log).
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      <b>Transition Point</b> = moment model shifts its reasoning direction.
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      <b>Trial Answer</b> = a forced early answer to check if model is ready.
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      <b>Confidence</b> = estimated correctness of the trial answer (analogy: estimation certainty).
    </div>
    <div class="fade-in-section bg-slate-800 border-l-4 border-sky-500 p-5 rounded-r-lg shadow-md my-4 transition-all duration-300 hover:shadow-lg hover:bg-slate-700 hover:shadow-sky-500/10">
      <b>Early Exit</b> = stop reasoning early when confidence is high (similar to optimal stopping rule).
    </div>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- Problem -->
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">1. Problem: Overthinking in Reasoning Models</h2>

    <p class="fade-in-section text-lg text-slate-400">
      LLMs often think too long. After reaching the correct idea, they <b>continue reasoning</b>,
      which increases cost and sometimes leads to worse answers.
    </p>

    <!-- 9. SVG responsive optimization, colors updated -->
    <div class="fade-in-section text-center my-8">
      <svg width="100%" viewBox="0 0 680 110" class="max-w-2xl mx-auto">
        <!-- Rectangles: using slate-700/800 -->
        <rect x="10" y="30" width="120" height="40" rx="8" fill="#334155" stroke="#475569" />
        <rect x="150" y="30" width="120" height="40" rx="8" fill="#334155" stroke="#475569" />
        <!-- Highlighted Rect: using sky-500 -->
        <rect x="290" y="30" width="150" height="40" rx="8" fill="#0ea5e9" stroke="#38bdf8" />
        <rect x="460" y="30" width="120" height="40" rx="8" fill="#334155" stroke="#475569" />
        <rect x="600" y="30" width="120" height="40" rx="8" fill="#334155" stroke="#475569" />

        <!-- Text -->
        <text x="48" y="56" fill="white" font-size="15" font-family="Inter, sans-serif">Think</text>
        <text x="183" y="56" fill="white" font-size="15" font-family="Inter, sans-serif">Think</text>
        <text x="319" y="56" fill="black" font-size="15" font-family="Inter, sans-serif" font-weight="600">Correct Idea</text>
        <text x="488" y="56" fill="white" font-size="15" font-family="Inter, sans-serif">Overthink</text>
        <text x="630" y="56" fill="white" font-size="15" font-family="Inter, sans-serif">Wrong</text>

        <!-- Connectors: using sky-500 -->
        <line x1="130" y1="50" x2="150" y2="50" stroke="#0ea5e9" stroke-width="3" />
        <line x1="270" y1="50" x2="290" y2="50" stroke="#0ea5e9" stroke-width="3" />
        <line x1="440" y1="50" x2="460" y2="50" stroke="#0ea5e9" stroke-width="3" />
        <line x1="580" y1="50" x2="600" y2="50" stroke="#0ea5e9" stroke-width="3" />
      </svg>
    </div>

    <p class="fade-in-section text-base text-slate-400 text-center">Overthinking = unnecessary cost + accuracy drops.</p>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- 
      ****************************************
      * [NEW] 10. LLM Flowchart & Optimization *
      ****************************************
    -->
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">2. LLM Training & Inference Flow</h2>
    <p class="fade-in-section text-lg text-slate-400 mb-8">
      To understand DEER's innovation, we first need to see how a standard Large Language Model (LLM) works. It has two main phases: **Training** (learning knowledge) and **Inference** (using knowledge).
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

    <!-- DEER (Renumbered to 3) -->
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">3. Original DEER Method</h2>

    <!-- 10. Redesigned .flow and .box styles -->
    <div class="fade-in-section flow bg-slate-800 rounded-xl shadow-lg p-6 my-6 text-center">
      <div class="flex flex-wrap justify-center items-center">
        <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">CoT Running</span>
        <span class="text-sky-400 text-2xl mx-2">→</span>
        <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">Transition Point</span>
      </div>
      <div class="arrow text-sky-400 text-3xl font-light my-2">⬇</div>
      <div>
        <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">Induce Trial Answer</span>
      </div>
      <div class="arrow text-sky-400 text-3xl font-light my-2">⬇</div>
      <div>
        <span class="box inline-block bg-slate-700 border border-slate-600 rounded-lg px-4 py-2 m-2 text-base">Compute Confidence \(C\)</span>
      </div>
      <div class="arrow text-sky-400 text-3xl font-light my-2">⬇</div>
      <div>
        <span class="box2 inline-block bg-sky-600 text-white font-medium rounded-lg px-4 py-2 m-2 shadow-md text-base">If \(C > \lambda\) → Early Exit</span>
      </div>
    </div>

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

    <p class="fade-in-section text-base text-slate-400">Baselines: Shallow CoT, Self-Consistency, Length-based Stop, etc.</p>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- LIMITATION (Renumbered to 4) -->
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">4. Limitation: DEER Confidence Too Simple</h2>

    <p class="fade-in-section text-lg text-slate-400">DEER relies only on <b>token probabilities</b>, which ignores:</p>

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
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">5. My Innovation: Adaptive Multi-Source Confidence</h2>

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
      <span class="box2 inline-block bg-blue-700 text-white font-bold rounded-lg px-5 py-3 shadow-lg text-lg">Adaptive Confidence \(C^*\)</span>
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

    <h3 class="fade-in-section text-2xl font-semibold text-cyan-300 mt-10 mb-4">5.2 Expected Improvements (My Method)</h3>

    <div class="fade-in-section flow bg-slate-800 rounded-xl shadow-lg p-8 my-6 space-y-3 text-lg">
      <div class="flex items-center"><span class="text-green-400 mr-3">✔</span> Safer early exits (fewer wrong answers)</div>
      <div class="flex items-center"><span class="text-green-400 mr-3">✔</span> More stable exit decisions (lower variance)</div>
      <div class="flex items-center"><span class="text-green-400 mr-3">✔</span> Adaptation to different tasks (math/code/QA)</div>
      <div class="flex items-center"><span class="text-green-400 mr-3">✔</span> User-aware reasoning depth</div>
      <div class="flex items-center"><span class="text-green-400 mr-3">✔</span> Better accuracy–cost tradeoff</div>
      <div class="flex items-center"><span class="text-green-400 mr-3">✔</span> Aligns with control theory mindset: multi-signal decision-making</div>
    </div>

    <div class="fade-in-section h-px bg-slate-700 my-16"></div>

    <!-- RL (Renumbered to 6) -->
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">6. RL Extension (Optional)</h2>

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
    <h2 class="fade-in-section text-3xl font-bold text-cyan-400 mb-6">7. References & Code</h2>

    <div class="fade-in-section text-lg text-slate-400 space-y-2">
      <p>• <b>Paper: Dynamic Early Exit in Reasoning Models</b>, 2025</p>
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
        return;
      }
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
    });
  </script>

</body>

</html>
