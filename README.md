<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <title>How Engines Work — Interactive Explainer</title>
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <style>
        :root{
            --bg:#0b1220;
            --panel:#0f1724;
            --accent:#ff8c42;
            --muted:#9aa8bf;
            --glass:rgba(255,255,255,0.04);
            --card:#0b1220;
        }
        html,body{height:100%;margin:0;font-family:Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
        body{background:linear-gradient(180deg,#09101a 0%, #07101a 100%);color:#e6eef8;display:flex;align-items:flex-start;justify-content:center;padding:24px;box-sizing:border-box;}
        .app{width:1200px;max-width:calc(100% - 48px);display:grid;grid-template-columns:1fr 360px;gap:20px;align-items:start;}
        header{grid-column:1/3;display:flex;align-items:center;justify-content:space-between;margin-bottom:8px;}
        header h1{font-size:20px;margin:0;color:var(--accent);}
        header p{margin:0;color:var(--muted);font-size:13px}
        .stage{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:12px;padding:18px;box-shadow:0 6px 18px rgba(2,6,23,0.6);min-height:640px;display:flex;flex-direction:column;}
        .canvas-wrap{flex:1;display:flex;align-items:center;justify-content:center;position:relative;overflow:hidden;border-radius:8px;background:linear-gradient(180deg,#08111a 0%, #07101a 100%);border:1px solid rgba(255,255,255,0.02);}
        .controls{display:flex;gap:8px;margin-top:12px;align-items:center;}
        .btn{background:var(--panel);border:1px solid rgba(255,255,255,0.04);color:var(--muted);padding:8px 10px;border-radius:8px;font-size:13px;cursor:pointer}
        .btn.primary{background:linear-gradient(90deg,var(--accent),#ffb788);color:#061018;border:none}
        .panel{background:var(--panel);padding:14px;border-radius:12px;border:1px solid rgba(255,255,255,0.03);height:640px;overflow:auto;box-shadow:0 6px 18px rgba(2,6,23,0.6);}
        .control-row{display:flex;align-items:center;gap:8px;margin-bottom:8px}
        label{font-size:12px;color:var(--muted);min-width:100px}
        input[type=range]{width:100%}
        .info{font-size:13px;color:var(--muted);line-height:1.45}
        .part{cursor:pointer;transition:transform .12s ease}
        .part:hover{transform:scale(1.03)}
        .highlight{filter:drop-shadow(0 6px 18px rgba(255,140,66,0.12));}
        .legend{display:flex;flex-wrap:wrap;gap:8px;margin-top:10px}
        .legend .item{background:var(--glass);padding:6px 8px;border-radius:8px;font-size:12px;color:var(--muted)}
        .status{margin-top:10px;font-size:13px;color:var(--muted)}
        /* Responsive */
        @media (max-width:1000px){
            .app{grid-template-columns:1fr;max-width:900px}
            header{flex-direction:column;align-items:flex-start;gap:8px}
            .panel{order:2;height:auto}
        }

        /* SVG layout helpers */
        svg{max-width:100%;height:auto;display:block}

        /* Basic animations */
        .spark{fill:rgba(255,200,80,0.95);filter:drop-shadow(0 6px 20px rgba(255,160,50,0.25))}
        .smoke{fill:rgba(200,200,210,0.06)}
        /* Keyframes for valve motion toggles are controlled in JS for smoother control */
    </style>
</head>
<body>
    <div class="app" id="app">
        <header>
            <div>
                <h1>Engine Visualizer — 4-Cycle Engine</h1>
                <p>Interactive explainer. Click parts to learn. Use controls to animate and change speed.</p>
            </div>
            <div>
                <small style="color:var(--muted)">Built for learning — simplified schematics, not engineering drawings.</small>
            </div>
        </header>

        <section class="stage" aria-label="Animation stage">
            <div class="canvas-wrap" id="canvasWrap">
                <!-- SVG illustrating a simple inline 4-stroke engine with one cylinder, piston, crank, valves -->
                <svg id="engineSVG" viewBox="0 0 900 560" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Engine schematic">
                    <defs>
                        <linearGradient id="metal" x1="0" x2="1">
                            <stop offset="0" stop-color="#b0b8c6"/>
                            <stop offset="1" stop-color="#6e7885"/>
                        </linearGradient>
                        <linearGradient id="glassGrad" x1="0" x2="0" y1="0" y2="1">
                            <stop offset="0" stop-color="rgba(255,255,255,0.06)"/>
                            <stop offset="1" stop-color="rgba(255,255,255,0.02)"/>
                        </linearGradient>
                        <filter id="softShadow" x="-50%" y="-50%" width="200%" height="200%">
                            <feDropShadow dx="0" dy="6" stdDeviation="8" flood-color="#000" flood-opacity="0.45"/>
                        </filter>
                    </defs>

                    <!-- Background guide -->
                    <rect x="10" y="10" width="880" height="540" rx="10" fill="url(#glassGrad)"/>

                    <!-- Engine block -->
                    <g id="engineBlock" transform="translate(60,40)">
                        <rect x="0" y="20" width="540" height="340" rx="8" fill="#0f1a24" stroke="#152232" stroke-width="2"/>
                        <!-- Cylinder chamber -->
                        <g id="cylinderGroup" transform="translate(220,40)">
                            <rect x="-70" y="0" width="220" height="260" rx="8" fill="#0c141a" stroke="#22313f" stroke-width="2"/>
                            <!-- Cylinder inner -->
                            <rect x="-56" y="16" width="192" height="200" rx="6" fill="#0b1220" stroke="#1a2430" stroke-width="1"/>
                            <!-- Spark plug -->
                            <g id="sparkPlug" class="part" data-part="sparkPlug" transform="translate(70,12)">
                                <rect x="0" y="0" width="18" height="34" rx="3" fill="#dfe7ef" stroke="#485767" stroke-width="1"/>
                                <rect x="4" y="6" width="10" height="12" rx="1" fill="#abb9c8"/>
                                <circle cx="9" cy="30" r="3" fill="#ffc28a"/>
                            </g>
                            <!-- Valves -->
                            <g id="valves" transform="translate(30,-10)">
                                <g id="intakeValve" class="part" data-part="intakeValve" transform="translate(0,0)">
                                    <rect x="0" y="0" width="8" height="40" rx="4" fill="#c1c9d3" stroke="#45566b" stroke-width="1"/>
                                    <circle cx="4" cy="44" r="12" fill="#aeb8c2" stroke="#2b3947" stroke-width="1"/>
                                </g>
                                <g id="exhaustValve" class="part" data-part="exhaustValve" transform="translate(60,0)">
                                    <rect x="0" y="0" width="8" height="40" rx="4" fill="#c1c9d3" stroke="#45566b" stroke-width="1"/>
                                    <circle cx="4" cy="44" r="12" fill="#aeb8c2" stroke="#2b3947" stroke-width="1"/>
                                </g>
                            </g>

                            <!-- Piston assembly -->
                            <g id="pistonGroup" transform="translate(38,40)">
                                <g id="piston" class="part" data-part="piston">
                                    <rect x="0" y="0" width="128" height="120" rx="8" fill="url(#metal)" stroke="#2c3b49" stroke-width="2"/>
                                    <rect x="6" y="6" width="116" height="18" rx="6" fill="#0b1016" opacity="0.15"/>
                                </g>
                                <!-- Piston pin and connecting rod -->
                                <g id="connRod" transform="translate(64,120)" >
                                    <rect x="-8" y="-8" width="16" height="160" rx="8" fill="#6f7986" stroke="#22313f" stroke-width="1" transform="rotate(0)"/>
                                    <circle cx="0" cy="152" r="18" fill="#cbd3db" stroke="#2d3a46" stroke-width="2"/>
                                </g>
                            </g>

                        </g>

                        <!-- Crankcase and crankshaft depiction -->
                        <g id="crankGroup" transform="translate(90,260)">
                            <rect x="0" y="60" width="420" height="40" rx="6" fill="#071018" stroke="#18222c" stroke-width="1"/>
                            <g id="crankshaft" transform="translate(220,44)">
                                <!-- Main journal -->
                                <circle cx="0" cy="0" r="28" fill="#2f3844" stroke="#12171b" stroke-width="2"/>
                                <!-- Crank throw -->
                                <g id="throw" transform="rotate(0)">
                                    <rect x="-10" y="0" width="20" height="90" rx="8" fill="#6a7682" stroke="#22313f" stroke-width="1"/>
                                    <circle cx="0" cy="90" r="24" fill="#bcd0db" stroke="#2d3a46" stroke-width="2"/>
                                </g>
                            </g>
                            <!-- Flywheel -->
                            <g id="flywheel" transform="translate(430,84)">
                                <circle cx="0" cy="0" r="44" fill="#1b2a36" stroke="#0a1014" stroke-width="2"/>
                                <circle cx="0" cy="0" r="28" fill="#24313c" stroke="#0d1418" stroke-width="1"/>
                            </g>
                        </g>

                    </g>

                    <!-- Overlays for smoke and spark (hidden initially) -->
                    <g id="sparkFlash" opacity="0">
                        <polygon points="614,90 626,60 640,86 660,70 652,100 674,112 640,118 632,144 608,120 608,92" class="spark" />
                    </g>

                    <g id="smokeGroup" opacity="0">
                        <g transform="translate(520,40) scale(1)">
                            <ellipse cx="0" cy="0" rx="10" ry="6" class="smoke"/>
                            <ellipse cx="18" cy="-10" rx="14" ry="8" class="smoke" opacity="0.12"/>
                            <ellipse cx="-12" cy="-16" rx="18" ry="9" class="smoke" opacity="0.10"/>
                        </g>
                    </g>

                    <!-- Labels -->
                    <g id="labels" transform="translate(20,20)" fill="#9aa8bf" font-size="12">
                        <text x="200" y="10">Cylinder</text>
                        <text x="560" y="220">Crankshaft & Flywheel</text>
                    </g>
                </svg>
            </div>

            <div class="controls" role="toolbar" aria-label="Controls">
                <button class="btn primary" id="playPauseBtn">Play</button>
                <button class="btn" id="stepBtn">Step</button>
                <div style="display:flex;flex-direction:column;width:260px">
                    <label style="font-size:12px;color:var(--muted);margin-bottom:6px">RPM: <span id="rpmLabel">800</span></label>
                    <input id="rpmRange" type="range" min="200" max="6000" step="10" value="800" />
                </div>
            </div>

        </section>

        <aside class="panel" aria-label="Information panel">
            <div style="display:flex;align-items:center;justify-content:space-between">
                <h3 style="margin:0;color:var(--accent);font-size:16px">Parts & Info</h3>
                <div style="font-size:12px;color:var(--muted)">Click parts on the diagram</div>
            </div>

            <div style="margin-top:12px" id="infoBox">
                <div class="info" id="introText">
                    Click any engine part to see details here. Use Play to run the 4-stroke animation: Intake, Compression, Power, Exhaust. Adjust RPM for animation speed. Step moves the crank by one step.
                </div>
            </div>

            <div class="legend" aria-hidden="false">
                <div class="item">Intake Valve</div>
                <div class="item">Exhaust Valve</div>
                <div class="item">Piston</div>
                <div class="item">Crankshaft</div>
                <div class="item">Spark Plug</div>
            </div>

            <div class="status" id="statusText" style="margin-top:12px">Status: idle</div>

            <hr style="border:none;height:1px;background:rgba(255,255,255,0.02);margin:12px 0" />

            <div style="font-size:13px;color:var(--muted)">
                Controls:
                <ul style="margin:8px 0 12px 16px;padding:0;color:var(--muted)">
                    <li>Play: animate continuously</li>
                    <li>Step: advance one cycle stage</li>
                    <li>RPM: adjust speed of crank rotation</li>
                </ul>
            </div>

        </aside>

    </div>

    <script>
        // Minimal helper utilities
        const $ = sel => document.querySelector(sel);
        const $$ = sel => Array.from(document.querySelectorAll(sel));

        // Elements
        const playPauseBtn = $('#playPauseBtn');
        const stepBtn = $('#stepBtn');
        const rpmRange = $('#rpmRange');
        const rpmLabel = $('#rpmLabel');
        const statusText = $('#statusText');
        const infoBox = $('#infoBox');
        const engineSVG = $('#engineSVG');

        // Parts mapping
        const parts = {
            piston: engineSVG.getElementById('piston'),
            connRod: engineSVG.getElementById('connRod'),
            crankThrow: engineSVG.getElementById('throw'),
            crankshaft: engineSVG.getElementById('crankshaft'),
            flywheel: engineSVG.getElementById('flywheel'),
            intakeValve: engineSVG.getElementById('intakeValve'),
            exhaustValve: engineSVG.getElementById('exhaustValve'),
            sparkPlug: engineSVG.getElementById('sparkPlug'),
            sparkFlash: engineSVG.getElementById('sparkFlash'),
            smokeGroup: engineSVG.getElementById('smokeGroup'),
            pistonGroup: engineSVG.getElementById('pistonGroup')
        };

        let running = false;
        let rpm = Number(rpmRange.value);
        let angle = 0; // crank angle in degrees
        let lastTime = null;
        let animationId = null;
        const twoPi = Math.PI * 2;

        // Engine geometry parameters for a simple kinematic model
        const stroke = 180; // piston travel in px (top to bottom)
        const rod = 220; // connecting rod length approx
        const crankRadius = 64; // crank throw radius

        // Attach click handlers for parts
        function partInfo(key){
            const info = {
                piston: {
                    title: 'Piston',
                    text: 'The piston moves up and down in the cylinder. It transmits force from the expanding gas to the connecting rod and crankshaft.'
                },
                connRod: {
                    title: 'Connecting Rod',
                    text: 'Connects the piston to the crankshaft. Converts reciprocating motion into rotary motion and vice versa.'
                },
                crankshaft: {
                    title: 'Crankshaft',
                    text: 'Converts the up-and-down motion of the pistons into rotational motion. The crankshaft is connected to the flywheel.'
                },
                intakeValve: {
                    title: 'Intake Valve',
                    text: 'Opens to allow the air-fuel mixture into the combustion chamber during the intake stroke.'
                },
                exhaustValve: {
                    title: 'Exhaust Valve',
                    text: 'Opens to allow exhaust gases to exit the cylinder after combustion during the exhaust stroke.'
                },
                sparkPlug: {
                    title: 'Spark Plug',
                    text: 'Ignites the compressed air-fuel mixture to produce the power stroke in gasoline engines.'
                }
            };
            return info[key] || {title: key, text: 'No detailed information available.'};
        }

        // Set up interactive part clicks
        Array.from(engineSVG.querySelectorAll('.part')).forEach(el => {
            el.style.cursor = 'pointer';
            const key = el.getAttribute('data-part');
            el.addEventListener('click', (e) => {
                e.stopPropagation();
                const info = partInfo(key);
                showInfo(info.title, info.text);
                highlightPart(el);
            });
        });

        // Display info in side panel
        function showInfo(title, text){
            infoBox.innerHTML = `
                <div style="margin-bottom:8px">
                    <strong style="color:var(--accent)">${title}</strong>
                </div>
                <div style="color:var(--muted);font-size:13px">${text}</div>
            `;
        }

        // Highlight function
        function highlightPart(el){
            // Remove highlight from others
            Array.from(engineSVG.querySelectorAll('.part')).forEach(p => p.classList.remove('highlight'));
            el.classList.add('highlight');
            setTimeout(()=> el.classList.remove('highlight'), 1800);
        }

        // Control handlers
        playPauseBtn.addEventListener('click', () => {
            running = !running;
            playPauseBtn.textContent = running ? 'Pause' : 'Play';
            statusText.textContent = running ? 'Status: running' : 'Status: paused';
            if(running){
                lastTime = performance.now();
                animate();
            } else {
                cancelAnimationFrame(animationId);
            }
        });

        stepBtn.addEventListener('click', () => {
            // advance a fixed angle step (simulate firing sequence)
            advanceAngle(45); // arbitrary step
            renderFrame();
        });

        rpmRange.addEventListener('input', (e) => {
            rpm = Number(e.target.value);
            rpmLabel.textContent = rpm;
        });

        // Basic kinematic functions
        function degToRad(d){ return d * Math.PI / 180; }
        function radToDeg(r){ return r * 180 / Math.PI; }

        // Given crank angle, compute piston vertical offset relative to top-dead-center
        function pistonPositionFromAngle(thetaDeg){
            // theta measured from top dead center clockwise
            const theta = degToRad(thetaDeg);
            const r = crankRadius; // crank radius
            const l = rod;
            // classic slider-crank: x = r*cos(theta) + sqrt(l^2 - (r*sin(theta))^2)
            const x = r * Math.cos(theta) + Math.sqrt(Math.max(0, l*l - (r*Math.sin(theta))**2));
            // Reference: top dead center when theta = 0 -> x = r + l
            const max = r + l;
            const tdc = max; // at theta=0
            // piston displacement from top: d = tdc - x
            const displacement = tdc - x;
            // convert to pixel travel scaled to stroke
            // stroke corresponds to 2*r
            const pixel = displacement / (2*r) * stroke;
            return pixel;
        }

        // Render function updates SVG transforms based on angle
        function renderFrame(){
            // Compute piston vertical position
            const pistonOffset = pistonPositionFromAngle(angle);
            // The pistonGroup initial transform is translate(38,40)
            const pistonY = 40 + pistonOffset;
            parts.pistonGroup.setAttribute('transform', `translate(38,${pistonY})`);
            // Connecting rod rotation: need angle between piston pin and crankpin
            // compute crankpin position relative to crank center (in global coordinates)
            // crank center at translate(90,260) + translate(220,44) => (310,304) offset from SVG origin plus group transforms...
            // For simplicity we calculate rotation based on angle only to give impression
            const throwAngle = angle; // degrees
            parts.crankThrow.setAttribute('transform', `rotate(${throwAngle})`);
            // Rotate flywheel and crankshaft overall
            parts.crankshaft.setAttribute('transform', `translate(220,44) rotate(${angle})`);
            parts.flywheel.setAttribute('transform', `translate(430,84) rotate(${angle})`);

            // Valve motion: simple sinusoidal opening timed to crank angle for 4-stroke cycle
            // Rough timing: Intake opens ~ 10-160 deg (crank), Exhaust opens ~ 200-350 deg
            const intakeOpen = valveOpenProfile(angle, 10, 160);
            const exhaustOpen = valveOpenProfile(angle, 200, 350);
            // Apply valve translations: move downward when open (positive y)
            const intakeY = -10 + intakeOpen * 36;
            const exhaustY = -10 + exhaustOpen * 36;
            parts.intakeValve.setAttribute('transform', `translate(0,${intakeY})`);
            parts.exhaustValve.setAttribute('transform', `translate(60,${exhaustY})`);

            // Spark: fire near 360 -> 0 boundary for power stroke (~ around 340..10)
            const sparkVal = sparkProfile(angle);
            parts.sparkFlash.setAttribute('opacity', sparkVal > 0 ? 1 : 0);
            parts.smokeGroup.setAttribute('opacity', sparkVal > 0 ? 1 : 0);
        }

        // Valve profile: returns 0..1 open amount
        function valveOpenProfile(angleDeg, start, end){
            // normalize angle into 0..360
            let a = ((angleDeg % 360) + 360) % 360;
            // treat window possibly wrapping
            if(start < end){
                if(a < start || a > end) return 0;
                // ramp in/out using smoothstep
                const len = end - start;
                const pos = (a - start) / len;
                return smoothstep(0,1,pos);
            } else {
                // wrap around case
                if(a < start && a > end) return 0;
                // map into 0..1
                let pos = (a >= start) ? (a - start) / ((360 - start) + end) : (a + (360 - start)) / ((360 - start) + end);
                return smoothstep(0,1,pos);
            }
        }

        function smoothstep(a,b,x){
            const t = Math.max(0, Math.min(1, (x - a) / (b - a)));
            return t * t * (3 - 2 * t);
        }

        function sparkProfile(angleDeg){
            // return strong near angle 350..10
            let a = ((angleDeg % 360) + 360) % 360;
            if(a > 350 || a < 10){
                // stronger near 0
                const dist = Math.min( Math.abs(a <= 180 ? a : 360 - a), 10);
                return (10 - dist) / 10;
            }
            return 0;
        }

        // Animation loop
        function animate(now){
            animationId = requestAnimationFrame(animate);
            if(!lastTime) lastTime = now;
            const dt = (now - lastTime) / 1000; // seconds
            lastTime = now;
            if(running){
                // Convert rpm to degrees per second: rpm/60 revs per sec * 360
                const degPerSec = (rpm / 60) * 360;
                angle = (angle + degPerSec * dt) % 360;
                renderFrame();
            }
        }

        // Utility to advance angle by degrees
        function advanceAngle(deg){
            angle = (angle + deg) % 360;
        }

        // Initialize
        renderFrame();
        rpmLabel.textContent = rpm;

        // Keyboard controls: space toggles play/pause, arrow keys step
        window.addEventListener('keydown', (e) => {
            if(e.code === 'Space'){ e.preventDefault(); playPauseBtn.click(); }
            if(e.code === 'ArrowRight'){ stepBtn.click(); }
        });

        // Basic auto-play sample: animate 1-second burst to show engine behavior on load
        let demoCount = 0;
        function runDemoPulse(){
            if(demoCount++ > 0) return;
            running = true;
            playPauseBtn.textContent = 'Pause';
            statusText.textContent = 'Status: running (demo)';
            lastTime = performance.now();
            animate();
            setTimeout(()=> {
                running = false;
                cancelAnimationFrame(animationId);
                playPauseBtn.textContent = 'Play';
                statusText.textContent = 'Status: paused';
            }, 1600);
        }
        // Run a short demo after load
        setTimeout(runDemoPulse, 600);

        // Handle window click to clear info highlight
        document.getElementById('canvasWrap').addEventListener('click', (e) => {
            // clicking empty area clears info
            if(e.target.closest('.part')) return;
            infoBox.innerHTML = '<div class="info" id="introText">Click any engine part to see details here. Use Play to run the 4-stroke animation.</div>';
        });

        // Expose some internal state for debugging (dev only)
        window.__engineViz = { getAngle:()=>angle, setAngle:(a)=>{ angle=a; renderFrame(); }, parts };

        // Accessibility: announce rpm changes in status text
        rpmRange.addEventListener('change', () => {
            statusText.textContent = 'RPM set to ' + rpm;
            setTimeout(()=> {
                statusText.textContent = running ? 'Status: running' : 'Status: paused';
            }, 1200);
        });

        // END of main script

        /* Extra educational notes (lightweight, not too long) */
        /*
            The visual model above simulates a single-cylinder 4-stroke engine:
            - Intake stroke: intake valve opens, piston moves down, drawing air-fuel mixture.
            - Compression stroke: both valves closed, piston moves up compressing mixture.
            - Power stroke: near top, spark plug ignites mixture producing expansion force that pushes piston down.
            - Exhaust stroke: exhaust valve opens, piston moves up, expelling combustion gases.

            The animation uses a simplified slider-crank kinematic model. Real engines have valve timing (camshafts),
            inertia, damping, combustion pressure curves, lubrication, and many more complexities not modeled here.
        */

    </script>

    <!--
        Note: This file is intentionally structured for clarity and interactivity.
        Clickable SVG parts are marked with class="part" and data-part attributes.
        The JS uses a simplified kinematic model for educational animation.
    -->

</body>
</html>
