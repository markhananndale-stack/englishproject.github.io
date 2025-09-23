<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <title>Engine Simulator — 3 to 12 Cylinders</title>
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <style>
        :root{--bg:#0f1724;--panel:#0b1220;--accent:#ffb86b;--muted:#9aa7c7}
        html,body{height:100%;margin:0;font-family:Inter,Segoe UI,Roboto,Arial,sans-serif;background:var(--bg);color:#e6eef8}
        .app{display:flex;flex-direction:column;gap:12px;padding:14px;max-width:1100px;margin:0 auto}
        header{display:flex;align-items:center;gap:14px}
        h1{font-size:18px;margin:0}
        .controls{display:flex;gap:12px;flex-wrap:wrap;align-items:center}
        .control{background:var(--panel);padding:8px;border-radius:8px;font-size:13px;color:var(--muted);display:flex;gap:8px;align-items:center}
        input[type=range]{width:220px}
        button{background:transparent;border:1px solid #203045;color:var(--accent);padding:8px 12px;border-radius:8px;cursor:pointer}
        button.play{border-color:var(--accent);color:#071020}
        main{display:flex;gap:12px}
        canvas{background:linear-gradient(180deg,#071124 0%, #07162a 100%);border-radius:8px;box-shadow:0 10px 30px rgba(0,0,0,0.6)}
        .info{width:320px;background:linear-gradient(180deg,#07142855, #02102433);padding:12px;border-radius:8px;color:var(--muted);font-size:13px}
        .legend{display:flex;flex-wrap:wrap;gap:8px;margin-top:8px}
        .chip{background:#081426;padding:6px 8px;border-radius:6px;color:#9fb3d8;font-size:12px}
        .phase{display:flex;gap:6px;align-items:center;margin-top:8px}
        .dot{width:12px;height:12px;border-radius:50%}
        .phase div{font-size:13px}
        .phase .intake{background:#5fb3ff}
        .phase .compression{background:#9fb3d8}
        .phase .power{background:#ff9671}
        .phase .exhaust{background:#8a8fa6}
        footer{color:#64758f;font-size:12px;margin-top:6px}
        .small{font-size:12px;color:#93a7c4}
    </style>
</head>
<body>
    <div class="app">
        <header>
            <h1>Engine Simulator — choose 3 to 12 cylinders</h1>
            <div class="controls">
                <div class="control">
                    Cylinders
                    <input id="cylCount" type="range" min="3" max="12" value="6" />
                    <strong id="cylLabel">6</strong>
                </div>
                <div class="control">
                    Speed
                    <input id="speed" type="range" min="0" max="4" step="0.01" value="1" />
                    <strong id="speedLabel">1x</strong>
                </div>
                <button id="toggle" class="play">Start</button>
                <div class="control small">Four-stroke cycle (720°): Intake → Compression → Power → Exhaust</div>
            </div>
        </header>

        <main>
            <canvas id="scene" width="820" height="420"></canvas>

            <aside class="info">
                <div class="small">Simulation explains pistons, connecting rods and firing phases. Use the slider to change the number of cylinders and speed. Each cylinder's cycle spans 720° of crank rotation; firing order is evenly distributed.</div>

                <div class="legend">
                    <div class="chip">Inline layout</div>
                    <div class="chip">Four-stroke</div>
                    <div class="chip">Adjustable speed</div>
                    <div class="chip">3–12 cylinders</div>
                </div>

                <div style="margin-top:10px;">
                    <strong>Selected cylinder:</strong>
                    <div id="inspect" class="small" style="margin-top:6px">Hover a piston to inspect its stroke and instantaneous pressure hint.</div>
                </div>

                <div style="margin-top:10px;">
                    <strong>Phases</strong>
                    <div class="phase"><div class="dot intake"></div><div>Intake</div></div>
                    <div class="phase"><div class="dot compression"></div><div>Compression</div></div>
                    <div class="phase"><div class="dot power"></div><div>Power</div></div>
                    <div class="phase"><div class="dot exhaust"></div><div>Exhaust</div></div>
                </div>

                <footer>Tip: Increase cylinders to observe overlapping power strokes and smoother torque.</footer>
            </aside>
        </main>
    </div>

    <script>
    // Simple inline engine simulation for index.html
    // - 4-stroke cycles, evenly spaced firing (phase offset = 720° / N)
    // - Piston vertical motion approximated using kinematic crank-slider formula for a realistic look
    (() => {
        const canvas = document.getElementById('scene');
        const ctx = canvas.getContext('2d');
        const cylSlider = document.getElementById('cylCount');
        const cylLabel = document.getElementById('cylLabel');
        const speedSlider = document.getElementById('speed');
        const speedLabel = document.getElementById('speedLabel');
        const toggle = document.getElementById('toggle');
        const inspect = document.getElementById('inspect');

        let running = false;
        let last = 0;
        let crankAngle = 0; // degrees
        let rpm = 30; // base speed -> scaled by speed slider
        let cylinders = parseInt(cylSlider.value,10);

        cylSlider.addEventListener('input', () => {
            cylinders = parseInt(cylSlider.value,10);
            cylLabel.textContent = cylinders;
            // keep crankAngle continuous; phase offsets will change
        });

        speedSlider.addEventListener('input', () => {
            speedLabel.textContent = parseFloat(speedSlider.value).toFixed(2) + 'x';
        });

        toggle.addEventListener('click', () => {
            running = !running;
            toggle.textContent = running ? 'Pause' : 'Start';
            toggle.classList.toggle('play', !running);
            last = performance.now();
            if (running) requestAnimationFrame(loop);
        });

        canvas.addEventListener('mousemove', (e) => {
            const rect = canvas.getBoundingClientRect();
            const mx = e.clientX - rect.left;
            const my = e.clientY - rect.top;
            hoveredIndex = hitTestCylinder(mx,my);
            updateInspectText();
        });
        canvas.addEventListener('mouseleave', () => { hoveredIndex = -1; updateInspectText(); });

        let hoveredIndex = -1;

        function degreesToRad(d){ return d*Math.PI/180; }

        function hitTestCylinder(mx,my){
            // identify which piston rectangle the mouse is over
            const w = canvas.width, h = canvas.height;
            const margin = 60;
            const areaW = w - margin*2;
            const spacing = areaW / cylinders;
            for(let i=0;i<cylinders;i++){
                const x = margin + i*spacing + spacing/2;
                const pistX = x - 28, pistW = 56;
                const pistY = 48, pistH = 140;
                if (mx >= pistX && mx <= pistX + pistW && my >= pistY && my <= pistY + pistH) return i;
            }
            return -1;
        }

        function updateInspectText(){
            if (hoveredIndex<0){
                inspect.textContent = 'Hover a piston to inspect its stroke and instantaneous pressure hint.';
                return;
            }
            const phaseInfo = cylinderPhaseInfo(hoveredIndex);
            inspect.innerHTML = `Cylinder ${hoveredIndex+1}: ${phaseInfo.phase} stroke
            • Angle: ${phaseInfo.angleDeg.toFixed(0)}°
            • Relative power hint: ${phaseInfo.powerHint}`;
        }

        function cylinderPhaseInfo(i){
            // compute angle for cylinder i in degrees within 0..720
            const phaseOffset = 720/cylinders * i;
            const angle = (crankAngle + phaseOffset) % 720;
            const phase = (angle>=0 && angle<180) ? 'Intake' :
                                        (angle<360) ? 'Compression' :
                                        (angle<540) ? 'Power' : 'Exhaust';
            // crude power hint: peak at center of power stroke (angle=450)
            const powerCenter = 450;
            const dist = Math.abs(((angle - powerCenter)+3600)%720 - powerCenter);
            const powerHint = phase==='Power' ? `${Math.max(0, (1 - dist/90)).toFixed(2)} (relative)` : '0.00';
            return { angleDeg: angle, phase, powerHint };
        }

        function loop(now){
            const dt = (now - last)/1000 || 0;
            last = now;
            // advance crank angle: base rpm scaled by speed slider (0..4)
            const speedMult = parseFloat(speedSlider.value);
            const degPerSec = rpm * 6 * speedMult; // rpm * 360 deg /60 = rpm*6
            crankAngle = (crankAngle + degPerSec * dt) % 720; // keep in 0..720 for cycles
            render();
            if (running) requestAnimationFrame(loop);
        }

        function render(){
            const w = canvas.width, h = canvas.height;
            ctx.clearRect(0,0,w,h);

            // background gradient
            const g = ctx.createLinearGradient(0,0,0,h);
            g.addColorStop(0,'#061126'); g.addColorStop(1,'#041426');
            ctx.fillStyle = g;
            ctx.fillRect(0,0,w,h);

            // drawing layout
            const margin = 60;
            const areaW = w - margin*2;
            const spacing = areaW / cylinders;
            const crankY = h - 70;
            const crankRadius = 22;
            const conrod = 80;
            const stroke = 80; // total piston travel (top to bottom)
            const crankRadiusSim = stroke/2;

            // draw crankshaft center
            ctx.strokeStyle = '#0f9fff22';
            ctx.lineWidth = 2;
            ctx.beginPath(); ctx.arc(w/2, crankY, crankRadius+8, 0, Math.PI*2); ctx.stroke();

            // for each cylinder, compute piston position
            for(let i=0;i<cylinders;i++){
                const cx = margin + i*spacing + spacing/2;
                // phase offset along 720 degrees
                const phaseOffset = 720/cylinders * i;
                const thetaDeg = (crankAngle + phaseOffset) % 720;
                // convert to crank rotation in radians (two revolutions => 720°)
                const crankRad = degreesToRad(thetaDeg/2); // map 720deg to 360deg crank rotation
                // kinematic piston displacement using crank-slider:
                // s = r*cos(theta) + sqrt(l^2 - (r*sin(theta))^2)
                const r = crankRadiusSim;
                const l = conrod;
                const s = r*Math.cos(crankRad) + Math.sqrt(Math.max(0, l*l - (r*Math.sin(crankRad))**2));
                // normalize s to 0..1 for rendering
                const sMin = r + (l - Math.sqrt(l*l - (r*r))); // approximate min
                const sMax = r + l;
                const norm = (s - sMin) / (sMax - sMin);
                const pistonTop = 40 + norm*(stroke); // top y

                // draw cylinder wall
                ctx.fillStyle = '#07162a';
                ctx.strokeStyle = '#18334a';
                ctx.lineWidth = 2;
                ctx.fillRect(cx-28, 20, 56, 160);
                ctx.strokeRect(cx-28, 20, 56, 160);

                // color by phase
                const phaseAngle = thetaDeg;
                let color = '#9fb3d8';
                if (phaseAngle>=0 && phaseAngle<180) color = '#5fb3ff';        // intake
                else if (phaseAngle<360) color = '#9fb3d8';                    // compression
                else if (phaseAngle<540) color = '#ff9671';                    // power
                else color = '#8a8fa6';                                        // exhaust

                // draw piston
                ctx.fillStyle = color;
                ctx.fillRect(cx-26, pistonTop, 52, 28);
                ctx.strokeStyle = '#07212b';
                ctx.strokeRect(cx-26, pistonTop, 52, 28);

                // connecting rod
                const rodX = cx;
                const rodTopY = pistonTop;
                // crank pin position relative to crank center (we stagger crank pins around center by phase offset too)
                const crankPinAngle = degreesToRad(thetaDeg/2);
                const pinX = w/2 + Math.cos(crankPinAngle)*(crankRadius + 10);
                const pinY = crankY + Math.sin(crankPinAngle)*(crankRadius + 4);

                ctx.beginPath();
                ctx.lineWidth = 6;
                ctx.strokeStyle = '#c9dceeff';
                ctx.moveTo(rodX, rodTopY+14);
                ctx.lineTo(pinX, pinY);
                ctx.stroke();

                // piston rod top (small)
                ctx.beginPath();
                ctx.lineWidth = 2;
                ctx.strokeStyle = '#04121a';
                ctx.moveTo(cx-12, pistonTop+28);
                ctx.lineTo(cx-12, pistonTop+34);
                ctx.moveTo(cx+12, pistonTop+28);
                ctx.lineTo(cx+12, pistonTop+34);
                ctx.stroke();

                // draw crank pin
                ctx.beginPath();
                ctx.fillStyle = '#202f3a';
                ctx.arc(pinX, pinY, 8, 0, Math.PI*2);
                ctx.fill();

                // label cylinder number
                ctx.fillStyle = '#b8d4ff';
                ctx.font = '12px sans-serif';
                ctx.textAlign = 'center';
                ctx.fillText((i+1), cx, 18);

                // highlight hovered
                if (i === hoveredIndex){
                    ctx.strokeStyle = 'rgba(255,255,255,0.14)';
                    ctx.lineWidth = 3;
                    ctx.strokeRect(cx-30, 18, 60, 164);
                }

                // tiny spark / explosion indicator during power stroke peak
                if (phaseAngle >= 360 && phaseAngle < 540){
                    // intensity based on proximity to mid-power (450°)
                    const dist = Math.abs(450 - phaseAngle);
                    const intensity = Math.max(0, 1 - dist/90);
                    if (intensity > 0.08){
                        ctx.beginPath();
                        ctx.fillStyle = `rgba(255,140,60,${0.25 + 0.75*intensity})`;
                        ctx.arc(cx, pistonTop - 6, 10 + 12*intensity, 0, Math.PI*2);
                        ctx.fill();
                    }
                }
            }

            // Draw crankshaft bar and center indicator
            ctx.fillStyle = '#11202a';
            ctx.fillRect(0, crankY-8, w, 16);
            ctx.beginPath();
            ctx.fillStyle = '#0b6fa6';
            ctx.arc(w/2, crankY, crankRadius+2, 0, Math.PI*2);
            ctx.fill();

            // overlay info
            ctx.fillStyle = '#9fb3d8';
            ctx.font = '12px sans-serif';
            ctx.textAlign = 'left';
            ctx.fillText(`Cylinders: ${cylinders}`, 12, h-12);
            ctx.fillText(`Crank angle: ${crankAngle.toFixed(1)}°`, 120, h-12);
            ctx.fillText(`Speed: ${speedSlider.value}x`, 300, h-12);
        }

        // initial render
        render();

        // start paused by default
    })();
    </script>
</body>
</html>
