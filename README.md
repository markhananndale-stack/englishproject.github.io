<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>English Presentation</title>
  <meta name="description" content="Embedded Canva presentation by Mark">
  <style>
    :root{
      --bg:#0f1724;
      --card:#0b1220;
      --muted:#9aa4b2;
      --accent:#0ea5e9;
    }

https://www.canva.com/design/DAGy8KwwdJs/udhNfbvthbFCh3SirkYvug/view
    html,body{height:100%;margin:0;font-family:Inter, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial; background:linear-gradient(180deg,var(--bg),#071024);color:#e6eef8}
    .wrap{
      max-width:1100px;margin:40px auto;padding:28px;border-radius:12px;background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));box-shadow:0 10px 30px rgba(2,6,23,0.6);
      display:grid;grid-template-columns:1fr;gap:20px;align-items:start
    }
    .header{display:flex;align-items:center;gap:16px}
    .logo{width:56px;height:56px;border-radius:10px;background:radial-gradient(circle at 30% 20%,#0ea5e9, #0369a1);display:flex;align-items:center;justify-content:center;font-weight:700}
    h1{font-size:1.15rem;margin:0}
    p.lead{margin:0;color:var(--muted)}
    .embed-card{position:relative;padding-top:56.25%;border-radius:8px;overflow:hidden;box-shadow:0 6px 20px rgba(2,6,23,0.5);background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.02))}
    .embed-card iframe{position:absolute;inset:0;width:100%;height:100%;border:0}
    .meta{display:flex;justify-content:space-between;gap:12px;align-items:center}
    .links{display:flex;gap:10px;align-items:center}
    a.btn{display:inline-block;padding:10px 14px;border-radius:8px;background:rgba(255,255,255,0.03);text-decoration:none;color:inherit;border:1px solid rgba(255,255,255,0.03);font-size:0.95rem}
    .footer{margin-top:6px;color:var(--muted);font-size:0.9rem}
    @media (max-width:640px){.wrap{margin:18px;padding:16px}.logo{width:48px;height:48px}}
  </style>
</head>
<body>
  <main class="wrap">
    <header class="header">
      <div class="logo">M</div>
      <div>
        <h1>Navy & Blue — Social Media Strategy Presentation</h1>
        <p class="lead">Embedded Canva design by Mark — responsive embed for sharing and preview.</p>
      </div>
    </header>

    <section class="embed-card" aria-label="Canva presentation embed">
      <iframe loading="lazy" src="https://www.canva.com/design/DAGy8KwwdJs/udhNfbvthbFCh3SirkYvug/view?embed" allowfullscreen allow="fullscreen" title="Navy and Blue Modern Professional Social Media Strategy Presentation by Mark"></iframe>
    </section>

    <div class="meta">
      <div class="links">
        <a class="btn" href="https://www.canva.com/design/DAGy8KwwdJs/udhNfbvthbFCh3SirkYvug/view?utm_content=DAGy8KwwdJs&utm_campaign=designshare&utm_medium=embeds&utm_source=link" target="_blank" rel="noopener noreferrer">Open on Canva</a>
        <a class="btn" href="#" onclick="navigator.clipboard && navigator.clipboard.writeText(window.location.href).then(()=>alert('Page URL copied'))">Copy this page URL</a>
      </div>
      <div class="footer">Tip: use the "Open on Canva" button to edit or download the design (requires Canva account).</div>
    </div>
  </main>

  <script>
    // Small enhancement: ensure iframe stays responsive if user opens in a constrained container
    (function(){
      var el=document.querySelector('.embed-card');
      function updatePadding(){
        var w=el.offsetWidth;
        // keep 16:9 but if very narrow switch to 4:3 for readability
        el.style.paddingTop = (w < 480 ? (75) : (56.25)) + '%';
      }
      updatePadding();
      window.addEventListener('resize', updatePadding);
    })();
  </script>
</body>
</html>
