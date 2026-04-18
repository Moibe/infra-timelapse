<script lang="ts">
</script>

<div class="app">
    <h1 class="title">Opciones de representación de Proyecto</h1>
    <p class="subtitle">Evoluciones del cuadro blanco actual</p>

    <div class="gallery">

        <!-- OPCIÓN 1: Folder Tab -->
        <div class="option-card">
            <h2>Opción 1: Folder Tab</h2>
            <p class="desc">Pestaña tipo carpeta en la esquina superior</p>
            <div class="preview">
                <div class="box-folder">
                    <div class="folder-tab">📁 PRJ</div>
                    <div class="folder-body">
                        <input class="box-label" type="text" value="api-server" readonly />
                    </div>
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 2: Window Title Bar -->
        <div class="option-card">
            <h2>Opción 2: Window Title Bar</h2>
            <p class="desc">Barra tipo ventana de app con 3 dots</p>
            <div class="preview">
                <div class="box-window">
                    <div class="window-bar">
                        <span class="win-dot red"></span>
                        <span class="win-dot yellow"></span>
                        <span class="win-dot green"></span>
                    </div>
                    <input class="box-label label-window" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 3: Left Accent Bar -->
        <div class="option-card">
            <h2>Opción 3: Barra Lateral Color</h2>
            <p class="desc">Acento vertical izquierdo para identificar tipo</p>
            <div class="preview">
                <div class="box-accent">
                    <div class="accent-bar"></div>
                    <input class="box-label label-accent" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 4: Stacked Cards -->
        <div class="option-card">
            <h2>Opción 4: Card Stack</h2>
            <p class="desc">Efecto de capas apiladas, sugiere "contiene cosas"</p>
            <div class="preview">
                <div class="box-stack-wrap">
                    <div class="stack-layer layer-3"></div>
                    <div class="stack-layer layer-2"></div>
                    <div class="box-stack">
                        <input class="box-label" type="text" value="api-server" readonly />
                        <span class="dot dot-left"></span>
                        <span class="dot dot-top"></span>
                        <span class="dot dot-right"></span>
                        <span class="dot dot-bottom"></span>
                        <span class="resize-handle"></span>
                    </div>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 5: Bracket / Container -->
        <div class="option-card">
            <h2>Opción 5: Container / Package</h2>
            <p class="desc">Bordes de corchete que sugieren contenedor</p>
            <div class="preview">
                <div class="box-container">
                    <div class="bracket-tl"></div>
                    <div class="bracket-tr"></div>
                    <div class="bracket-bl"></div>
                    <div class="bracket-br"></div>
                    <div class="container-badge">⚙️</div>
                    <input class="box-label label-container" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 6: Hexagon Badge + Box -->
        <div class="option-card">
            <h2>Opción 6: Badge Header</h2>
            <p class="desc">Badge de tipo con gradiente en header</p>
            <div class="preview">
                <div class="box-badge">
                    <div class="badge-header">
                        <span class="badge-icon">▲</span>
                        <span class="badge-text">PROJECT</span>
                    </div>
                    <input class="box-label label-badge" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 7: Rounded + Shadow Glow -->
        <div class="option-card">
            <h2>Opción 7: Glow Box</h2>
            <p class="desc">Box con sombra luminosa que indica "activo"</p>
            <div class="preview">
                <div class="box-glow">
                    <div class="glow-indicator"></div>
                    <input class="box-label" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 8: Box con inner grid -->
        <div class="option-card">
            <h2>Opción 8: Blueprint Box</h2>
            <p class="desc">Sutil patrón de grid interior tipo blueprint</p>
            <div class="preview">
                <div class="box-blueprint">
                    <input class="box-label" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 9: Chip / Tag style -->
        <div class="option-card">
            <h2>Opción 9: Chip Style</h2>
            <p class="desc">Estilo compacto tipo chip/tag de terminal</p>
            <div class="preview">
                <div class="box-chip">
                    <div class="chip-left">
                        <span class="chip-symbol">›_</span>
                    </div>
                    <div class="chip-right">
                        <input class="box-label label-chip" type="text" value="api-server" readonly />
                    </div>
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                </div>
            </div>
        </div>

    </div>

    <!-- Comparación lado a lado -->
    <h2 class="section-title">Comparación con box actual</h2>
    <div class="comparison">
        <div class="comp-item">
            <span class="comp-label">Actual</span>
            <div class="box-standard">
                <input class="box-label" type="text" value="api-server" readonly />
                <span class="dot dot-left"></span>
                <span class="dot dot-top"></span>
                <span class="dot dot-right"></span>
                <span class="dot dot-bottom"></span>
                <span class="resize-handle"></span>
            </div>
        </div>
        <div class="comp-arrow">→</div>
        <div class="comp-item">
            <span class="comp-label">Folder Tab</span>
            <div class="box-folder">
                <div class="folder-tab">📁 PRJ</div>
                <div class="folder-body">
                    <input class="box-label" type="text" value="api-server" readonly />
                </div>
                <span class="dot dot-left"></span>
                <span class="dot dot-top"></span>
                <span class="dot dot-right"></span>
                <span class="dot dot-bottom"></span>
                <span class="resize-handle"></span>
            </div>
        </div>
        <div class="comp-arrow">→</div>
        <div class="comp-item">
            <span class="comp-label">Window Bar</span>
            <div class="box-window">
                <div class="window-bar">
                    <span class="win-dot red"></span>
                    <span class="win-dot yellow"></span>
                    <span class="win-dot green"></span>
                </div>
                <input class="box-label label-window" type="text" value="api-server" readonly />
                <span class="dot dot-left"></span>
                <span class="dot dot-top"></span>
                <span class="dot dot-right"></span>
                <span class="dot dot-bottom"></span>
                <span class="resize-handle"></span>
            </div>
        </div>
        <div class="comp-arrow">→</div>
        <div class="comp-item">
            <span class="comp-label">Card Stack</span>
            <div class="box-stack-wrap">
                <div class="stack-layer layer-3"></div>
                <div class="stack-layer layer-2"></div>
                <div class="box-stack">
                    <input class="box-label" type="text" value="api-server" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    :global(*, *::before, *::after) {
        font-family: 'Roboto', sans-serif;
        font-weight: 700;
        box-sizing: border-box;
    }

    .app {
        width: 100%;
        min-height: 100vh;
        background: linear-gradient(135deg, #2d0a5f 0%, #5020a0 50%, #6b40b0 100%);
        padding: 40px;
    }

    .title {
        color: white;
        font-size: 24px;
        text-align: center;
        margin: 0 0 4px 0;
    }

    .subtitle {
        color: rgba(255,255,255,0.6);
        font-size: 13px;
        text-align: center;
        margin: 0 0 40px 0;
    }

    .gallery {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 30px;
        max-width: 1100px;
        margin: 0 auto 60px auto;
    }

    .option-card {
        background: rgba(255,255,255,0.06);
        border: 1px solid rgba(255,255,255,0.15);
        border-radius: 16px;
        padding: 20px;
        backdrop-filter: blur(8px);
    }

    .option-card h2 {
        color: white;
        font-size: 14px;
        margin: 0 0 4px 0;
    }

    .desc {
        color: rgba(255,255,255,0.5);
        font-size: 11px;
        margin: 0 0 24px 0;
    }

    .preview {
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 160px;
    }

    /* ===== ESTILOS COMPARTIDOS ===== */

    .box-label {
        position: absolute;
        left: 10px;
        right: 10px;
        top: 10px;
        width: auto;
        background: transparent;
        border: none;
        border-bottom: 1px solid rgba(0,0,0,0.2);
        text-align: center;
        font-size: 11px;
        color: #333;
        outline: none;
        cursor: text;
    }

    .box-label::placeholder {
        color: rgba(0,0,0,0.3);
    }

    .dot {
        position: absolute;
        width: 12px;
        height: 12px;
        border-radius: 50%;
        background: #6495ed;
        box-shadow: 0 0 6px rgba(100, 149, 237, 0.6);
        z-index: 10;
    }

    .dot-left   { left: -6px; top: 50%; margin-top: -6px; }
    .dot-top    { top: -6px; left: 50%; margin-left: -6px; }
    .dot-right  { right: -6px; top: 50%; margin-top: -6px; }
    .dot-bottom { bottom: -6px; left: 50%; margin-left: -6px; }

    .resize-handle {
        position: absolute;
        bottom: 0;
        right: 0;
        width: 16px;
        height: 16px;
        cursor: nwse-resize;
        border-bottom-right-radius: 12px;
        background: linear-gradient(135deg, transparent 50%, rgba(0,0,0,0.15) 50%);
    }

    /* ===== BOX ESTÁNDAR ===== */

    .box-standard {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
    }

    /* ===== OPCIÓN 1: FOLDER TAB ===== */

    .box-folder {
        position: relative;
        width: 130px;
        height: 125px;
    }

    .folder-tab {
        position: absolute;
        top: -16px;
        left: 0;
        width: 70px;
        height: 20px;
        background: white;
        border-radius: 8px 8px 0 0;
        font-size: 9px;
        color: #666;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 2px;
        box-shadow: 0 -2px 10px rgba(0,0,0,0.1);
        z-index: 2;
    }

    .folder-body {
        position: absolute;
        top: 0;
        left: 0;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 0 12px 12px 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
    }

    .folder-body .box-label {
        top: 14px;
    }

    .box-folder .dot-top { top: -22px; }
    .box-folder .resize-handle {
        position: absolute;
        bottom: 0;
        right: 0;
        width: 16px;
        height: 16px;
        cursor: nwse-resize;
        border-bottom-right-radius: 12px;
        background: linear-gradient(135deg, transparent 50%, rgba(0,0,0,0.15) 50%);
        z-index: 3;
    }

    /* ===== OPCIÓN 2: WINDOW TITLE BAR ===== */

    .box-window {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        overflow: hidden;
    }

    .window-bar {
        width: 100%;
        height: 20px;
        background: #f0f0f0;
        border-radius: 12px 12px 0 0;
        display: flex;
        align-items: center;
        padding-left: 8px;
        gap: 4px;
    }

    .win-dot {
        width: 7px;
        height: 7px;
        border-radius: 50%;
    }

    .win-dot.red    { background: #ff5f57; }
    .win-dot.yellow { background: #febc2e; }
    .win-dot.green  { background: #28c840; }

    .label-window { top: 28px; }

    /* ===== OPCIÓN 3: LEFT ACCENT BAR ===== */

    .box-accent {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        overflow: hidden;
    }

    .accent-bar {
        position: absolute;
        left: 0;
        top: 0;
        bottom: 0;
        width: 5px;
        background: linear-gradient(180deg, #6c5ce7, #a29bfe);
        border-radius: 12px 0 0 12px;
    }

    .label-accent { left: 18px; }

    /* ===== OPCIÓN 4: CARD STACK ===== */

    .box-stack-wrap {
        position: relative;
        width: 130px;
        height: 125px;
    }

    .stack-layer {
        position: absolute;
        background: white;
        border-radius: 12px;
        width: 130px;
        height: 125px;
    }

    .layer-3 {
        top: 8px;
        left: 8px;
        opacity: 0.3;
        box-shadow: 0 2px 8px rgba(0,0,0,0.15);
    }

    .layer-2 {
        top: 4px;
        left: 4px;
        opacity: 0.55;
        box-shadow: 0 3px 12px rgba(0,0,0,0.2);
    }

    .box-stack {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
    }

    /* ===== OPCIÓN 5: CONTAINER / BRACKET ===== */

    .box-container {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
    }

    .bracket-tl, .bracket-tr, .bracket-bl, .bracket-br {
        position: absolute;
        width: 18px;
        height: 18px;
        z-index: 2;
    }

    .bracket-tl {
        top: 4px; left: 4px;
        border-top: 2.5px solid #6c5ce7;
        border-left: 2.5px solid #6c5ce7;
        border-radius: 4px 0 0 0;
    }
    .bracket-tr {
        top: 4px; right: 4px;
        border-top: 2.5px solid #6c5ce7;
        border-right: 2.5px solid #6c5ce7;
        border-radius: 0 4px 0 0;
    }
    .bracket-bl {
        bottom: 4px; left: 4px;
        border-bottom: 2.5px solid #6c5ce7;
        border-left: 2.5px solid #6c5ce7;
        border-radius: 0 0 0 4px;
    }
    .bracket-br {
        bottom: 4px; right: 4px;
        border-bottom: 2.5px solid #6c5ce7;
        border-right: 2.5px solid #6c5ce7;
        border-radius: 0 0 4px 0;
    }

    .container-badge {
        position: absolute;
        top: 6px;
        right: 8px;
        font-size: 14px;
        z-index: 3;
    }

    .label-container { top: 14px; }

    /* ===== OPCIÓN 6: BADGE HEADER ===== */

    .box-badge {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        overflow: hidden;
    }

    .badge-header {
        width: 100%;
        height: 22px;
        background: linear-gradient(90deg, #6c5ce7, #a29bfe);
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 4px;
        border-radius: 12px 12px 0 0;
    }

    .badge-icon {
        font-size: 9px;
        color: white;
    }

    .badge-text {
        font-size: 8px;
        color: rgba(255,255,255,0.9);
        letter-spacing: 1.5px;
    }

    .label-badge { top: 32px; }

    /* ===== OPCIÓN 7: GLOW BOX ===== */

    .box-glow {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3), 0 0 20px rgba(108,92,231,0.25), 0 0 40px rgba(108,92,231,0.1);
    }

    .glow-indicator {
        position: absolute;
        top: 8px;
        right: 8px;
        width: 8px;
        height: 8px;
        border-radius: 50%;
        background: #28c840;
        box-shadow: 0 0 6px rgba(40,200,64,0.6);
        animation: pulse 2s ease-in-out infinite;
    }

    @keyframes pulse {
        0%, 100% { opacity: 1; box-shadow: 0 0 6px rgba(40,200,64,0.6); }
        50% { opacity: 0.6; box-shadow: 0 0 12px rgba(40,200,64,0.8); }
    }

    /* ===== OPCIÓN 8: BLUEPRINT BOX ===== */

    .box-blueprint {
        position: relative;
        width: 130px;
        height: 125px;
        background:
            linear-gradient(rgba(100,149,237,0.06) 1px, transparent 1px),
            linear-gradient(90deg, rgba(100,149,237,0.06) 1px, transparent 1px),
            white;
        background-size: 12px 12px;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        border: 1px solid rgba(100,149,237,0.2);
    }

    /* ===== OPCIÓN 9: CHIP STYLE ===== */

    .box-chip {
        position: relative;
        width: 140px;
        height: 50px;
        display: flex;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        overflow: hidden;
    }

    .chip-left {
        width: 36px;
        background: linear-gradient(180deg, #333, #1a1a1a);
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .chip-symbol {
        color: #28c840;
        font-size: 14px;
        font-family: monospace;
    }

    .chip-right {
        flex: 1;
        background: white;
        position: relative;
    }

    .label-chip {
        top: 50%;
        transform: translateY(-50%);
        border-bottom: none;
        font-size: 11px;
    }

    .box-chip .dot-left { left: -6px; }
    .box-chip .dot-right { right: -6px; }
    .box-chip .dot-top { top: -6px; }
    .box-chip .dot-bottom { bottom: -6px; }

    /* ===== COMPARACIÓN ===== */

    .section-title {
        color: white;
        font-size: 18px;
        text-align: center;
        margin-bottom: 30px;
    }

    .comparison {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 30px;
        padding-bottom: 60px;
        flex-wrap: wrap;
    }

    .comp-item {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 12px;
    }

    .comp-label {
        color: rgba(255,255,255,0.7);
        font-size: 11px;
    }

    .comp-arrow {
        color: rgba(255,255,255,0.4);
        font-size: 28px;
        margin-top: 16px;
    }
</style>
