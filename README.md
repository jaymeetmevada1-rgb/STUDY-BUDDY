<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>StudyBuddy — AI Question Helper</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#6ee7b7;--muted:#94a3b8;--glass:rgba(255,255,255,0.03)}
    *{box-sizing:border-box}
    body{margin:0;font-family:Inter,Segoe UI,Roboto,Arial;background:linear-gradient(180deg,#071029 0%,#061623 100%);color:#e6eef8;min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px}
    .app{width:100%;max-width:1000px}
    header{display:flex;align-items:center;gap:12px;margin-bottom:18px}
    .logo{width:48px;height:48px;border-radius:8px;background:linear-gradient(135deg,var(--accent),#60a5fa);display:flex;align-items:center;justify-content:center;font-weight:700;color:#042027}
    h1{margin:0;font-size:1.25rem}
    p.lead{margin:0;color:var(--muted);font-size:0.95rem}

    .card{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border-radius:12px;padding:18px;margin-top:8px;box-shadow:0 6px 18px rgba(2,6,23,0.7)}

    form.grid{display:grid;grid-template-columns:1fr 140px;gap:12px;align-items:end}
    @media(max-width:700px){form.grid{grid-template-columns:1fr} .right-col{display:flex;gap:8px}}

    label{font-size:0.85rem;color:var(--muted);display:block;margin-bottom:6px}
    select,input,textarea{width:100%;padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:var(--glass);color:inherit;font-size:0.95rem}
    textarea{min-height:140px;resize:vertical}

    .controls{display:flex;gap:8px;align-items:center}
    button{background:linear-gradient(90deg,var(--accent),#60a5fa);color:#042027;border:none;padding:10px 14px;border-radius:10px;cursor:pointer;font-weight:600}
    .btn-ghost{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted)}

    .cols{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-top:12px}
    @media(max-width:900px){.cols{grid-template-columns:1fr}}

    .output{background:linear-gradient(180deg,rgba(2,6,23,0.4),rgba(2,6,23,0.25));padding:14px;border-radius:10px;min-height:120px;white-space:pre-wrap}
    .meta{display:flex;gap:10px;align-items:center;margin-top:10px}
    .small{font-size:0.85rem;color:var(--muted)}
    .examples{display:flex;gap:8px;flex-wrap:wrap}
    .chip{background:rgba(255,255,255,0.03);padding:6px 8px;border-radius:999px;cursor:pointer;border:1px solid rgba(255,255,255,0.02)}

    footer{margin-top:14px;color:var(--muted);font-size:0.85rem}
  </style>
</head>
<body>
  <div class="app">
    <header>
      <div class="logo">SB</div>
      <div>
        <h1>StudyBuddy — AI Question Helper</h1>
        <p class="lead">Ask questions in Hindi, English, Maths, Physics, Chemistry, Biology and get step-by-step help. Provide an API key to use an external AI or use the local fallback for simple math.</p>
      </div>
    </header>

    <div class="card">
      <form id="askForm" class="grid" onsubmit="return false">
        <div>
          <label for="subject">Subject</label>
          <select id="subject">
            <option>Hindi</option>
            <option>English</option>
            <option>Maths</option>
            <option>Physics</option>
            <option>Chemistry</option>
            <option>Biology</option>
            <option>General</option>
          </select>

          <div style="display:flex;gap:8px;margin-top:10px">
            <div style="flex:1">
              <label for="language">Language</label>
              <select id="language">
                <option>English</option>
                <option>Hindi</option>
              </select>
            </div>
            <div style="width:140px">
              <label for="level">Level</label>
              <select id="level">
                <option>School</option>
                <option>College</option>
                <option>Competitive</option>
              </select>
            </div>
          </div>

          <label for="question" style="margin-top:10px">Your question</label>
          <textarea id="question" placeholder="Type the question here. Be as specific as possible."></textarea>

          <div class="meta">
            <label style="margin:0"><input type="checkbox" id="useExternal"> Use external AI API</label>
            <div style="flex:1"></div>
            <button id="askBtn">Get Answer</button>
          </div>

          <div id="externalFields" style="display:none;margin-top:10px">
            <label for="apiUrl">API URL (OpenAI-compatible)</label>
            <input id="apiUrl" placeholder="https://api.openai.com/v1/chat/completions" />
            <label for="apiKey" style="margin-top:8px">API Key</label>
            <input id="apiKey" placeholder="Paste API key here (kept in browser only)" />
            <label for="model" style="margin-top:8px">Model</label>
            <input id="model" value="gpt-4o-mini" />
            <p class="small">If you don't have an API key, leave unchecked to use the local fallback (math support only).</p>
          </div>
        </div>

        <div class="right-col">
          <div>
            <label>Answer</label>
            <div id="answer" class="output">Answers will appear here.</div>

            <div class="controls" style="margin-top:8px">
              <button id="copyBtn" class="btn-ghost">Copy</button>
              <button id="clearBtn" class="btn-ghost">Clear</button>
              <button id="examplesBtn" class="btn-ghost">Examples</button>
            </div>

            <div style="margin-top:10px">
              <div class="small">Quick examples:</div>
              <div class="examples" id="examples"></div>
            </div>
          </div>
        </div>
      </form>

      <div style="margin-top:14px">
        <strong>How to use (short):</strong>
        <ol style="color:var(--muted);padding-left:18px;margin:6px 0">
          <li>Enter subject, language and the detailed question.</li>
          <li>Check "Use external AI API" and paste an API URL and key to use a real model (OpenAI-compatible endpoints work).</li>
          <li>If no API provided, local fallback will attempt simple math evaluation and give study tips for others.</li>
        </ol>
      </div>

    </div>

    <footer>
      <div class="small">This is a client-side helper. For full AI answers, provide an API key for an OpenAI-compatible endpoint or a proxy to Google Gemini. Keys stay in your browser only.</div>
    </footer>
  </div>

  <script>
    const subjectEl = document.getElementById('subject');
    const languageEl = document.getElementById('language');
    const levelEl = document.getElementById('level');
    const questionEl = document.getElementById('question');
    const answerEl = document.getElementById('answer');
    const askBtn = document.getElementById('askBtn');
    const copyBtn = document.getElementById('copyBtn');
    const clearBtn = document.getElementById('clearBtn');
    const useExternal = document.getElementById('useExternal');
    const externalFields = document.getElementById('externalFields');
    const apiUrl = document.getElementById('apiUrl');
    const apiKey = document.getElementById('apiKey');
    const model = document.getElementById('model');
    const examplesEl = document.getElementById('examples');

    const examples = [
      'Solve: 2x+5=17',
      'Explain photosynthesis in simple Hindi',
      'What is the chemical equation for combustion of methane?',
      'Correct grammar: "He go to school"',
      'Find the derivative of x^2 + 3x'
    ];

    examples.forEach(text=>{
      const b = document.createElement('div'); b.className='chip'; b.textContent = text; b.onclick=()=>questionEl.value=text; examplesEl.appendChild(b);
    });

    useExternal.addEventListener('change',e=>{ externalFields.style.display = useExternal.checked ? 'block' : 'none'; });

    copyBtn.addEventListener('click',()=>{
      navigator.clipboard.writeText(answerEl.textContent).then(()=>{
        copyBtn.textContent='Copied'; setTimeout(()=>copyBtn.textContent='Copy',1200);
      });
    });
    clearBtn.addEventListener('click',()=>{ answerEl.textContent=''; });

    askBtn.addEventListener('click',async ()=>{
      const subj = subjectEl.value; const lang = languageEl.value; const lvl = levelEl.value; const q = questionEl.value.trim();
      if(!q){ answerEl.textContent='Please type a question first.'; return; }
      answerEl.textContent='Thinking...';

      if(useExternal.checked && apiUrl.value && apiKey.value){
        try{
          const prompt = `You are a helpful tutor. Subject: ${subj}. Level: ${lvl}. Language: ${lang}. Question: ${q}. Provide step-by-step answer, short summary, and final answer.`;
          const payload = {
            model: model.value || 'gpt-4o-mini',
            messages:[{role:'user',content:prompt}],
            max_tokens:800
          };
          const res = await fetch(apiUrl.value,{
            method:'POST',headers:{'Content-Type':'application/json','Authorization':'Bearer '+apiKey.value},body:JSON.stringify(payload)
          });
          if(!res.ok){ const txt = await res.text(); answerEl.textContent=`API error: ${res.status} ${res.statusText}\n${txt}`; return; }
          const data = await res.json();
          // Try common response shapes
          let out = '';
          if(data.choices && data.choices[0] && data.choices[0].message) out = data.choices[0].message.content;
          else if(data.output_text) out = data.output_text;
          else if(typeof data === 'string') out = data;
          else out = JSON.stringify(data, null, 2);
          answerEl.textContent = out;
        }catch(err){ answerEl.textContent = 'Request failed: '+err.message; }
        return;
      }

      // Local fallback
      const local = await localAnswer(subj,q,lang);
      answerEl.textContent = local;
    });

    async function localAnswer(subject, q, language){
      // Maths: try safe eval
      if(subject.toLowerCase().includes('math')){
        // extract expression from question (naive)
        const exprMatch = q.match(/[0-9a-zA-Z\s\+\-\*\/\^\(\)\.=x]+/i);
        let expr = q;
        // If equation like 2x+5=17, solve simple linear ax+b=c
        const eq = q.replace(/\s+/g,'');
        try{
          if(/[=]/.test(eq) && /x/.test(eq)){
            // solve ax+b=c simplest cases
            const sides = eq.split('=');
            const left = sides[0]; const right = sides[1];
            // bring to form ax+b
            // replace x with t and try to isolate
            // numeric approach: find coefficient by evaluating at two points
            const f = s=>{
              const r = s.replace(/x/g,'(1e3)');
              return safeEval(r);
            }
            // numeric solve for linear by coefficient estimation
            const a = (safeEval(left.replace(/x/g,'2')) - safeEval(left.replace(/x/g,'0')))/2;
            const b = safeEval(left.replace(/x/g,'0'));
            const c = safeEval(right);
            const x = (c - b)/a;
            return `Solved (approx): x = ${round(x,6)}\nSteps: treated LHS as ax+b and solved ax+b=c.`;
          }
        }catch(e){/* fallthrough */}

        // otherwise try evaluate single expression
        try{
          expr = q.replace(/\^/g,'**').replace(/[a-zA-Z]/g,'');
          const val = safeEval(expr);
          return `Result: ${val}\nExplanation: evaluated the mathematical expression.`;
        }catch(e){ return 'Local math fallback failed. Provide an external API key for complex problems.'; }
      }

      // For language and science subjects give study guidance and short answers
      const tips = {
        default: `Study tip: Break the problem into smaller parts, identify formulas/keywords, and attempt one step at a time. If you want a model-generated answer, paste an API URL and key in the "Use external AI API" section.`,
        physics: `Physics approach: List knowns, identify relevant equations, rearrange, substitute values, show units, and check limits.`,
        chemistry: `Chemistry approach: Identify reactants/products, balance equations, check oxidation states or stoichiometry, include conditions.`,
        biology: `Biology approach: Define the concept, give an example, explain steps/processes sequentially, and conclude with relevance.`,
        english: `English help: For grammar, provide the sentence, ask for corrections and explanation. For essays, request structure: intro, body, conclusion.`,
        hindi: `Hindi help: For translations or explanations, paste the sentence; specify if you want simple or advanced Hindi.`
      };

      const s = subject.toLowerCase();
      if(s.includes('phys')) return tips.physics;
      if(s.includes('chem')) return tips.chemistry;
      if(s.includes('bio')) return tips.biology;
      if(s.includes('eng')) return tips.english;
      if(s.includes('hin')) return tips.hindi;
      return tips.default;
    }

    function safeEval(expr){
      // allow only numbers and operators
      if(!/^[0-9+\-*/(). %*\*\*]*$/.test(expr)){
        throw new Error('Unsafe characters in expression');
      }
      // replace accidental multiple operators
      return Function('return '+expr)();
    }
    function round(n,d){ return Math.round(n*Math.pow(10,d))/Math.pow(10,d); }

    // Small UX: pressing Ctrl+Enter sends
    questionEl.addEventListener('keydown',e=>{ if((e.ctrlKey||e.metaKey)&&e.key==='Enter'){ askBtn.click(); } });

  </script>
</body>
</html>
