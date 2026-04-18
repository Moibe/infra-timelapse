<script lang="ts">
</script>

<div class="app">
    <h1 class="title">Opciones de representación de Base de Datos</h1>
    <p class="subtitle">Mismo look & feel que los boxes actuales</p>

    <div class="gallery">

        <!-- OPCIÓN 1: Cilindro CSS puro -->
        <div class="option-card">
            <h2>Opción 1: Cilindro CSS</h2>
            <p class="desc">Forma clásica de DB con elipses CSS</p>
            <div class="preview">
                <div class="db-cylinder">
                    <div class="cyl-top"></div>
                    <div class="cyl-body">
                        <input class="box-label" type="text" value="users_db" readonly />
                    </div>
                    <div class="cyl-bottom"></div>
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 2: Box estándar + emoji DB header -->
        <div class="option-card">
            <h2>Opción 2: Box + Emoji Header</h2>
            <p class="desc">Mismo rectángulo, emoji de DB como indicador</p>
            <div class="preview">
                <div class="box-standard">
                    <div class="box-type-badge">🗄️</div>
                    <input class="box-label" type="text" value="users_db" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 3: Box con barra de color superior -->
        <div class="option-card">
            <h2>Opción 3: Box + Color Header</h2>
            <p class="desc">Barra de color en la parte superior para diferenciar tipo</p>
            <div class="preview">
                <div class="box-color-header">
                    <div class="color-bar">
                        <span class="bar-icon">⛁</span>
                        <span class="bar-text">DATABASE</span>
                    </div>
                    <input class="box-label label-offset" type="text" value="users_db" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 4: Cilindro SVG dentro de box -->
        <div class="option-card">
            <h2>Opción 4: Box + SVG Cilindro</h2>
            <p class="desc">Icono SVG de cilindro dentro del box estándar</p>
            <div class="preview">
                <div class="box-standard">
                    <div class="svg-icon">
                        <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="#6495ed" stroke-width="1.5">
                            <ellipse cx="12" cy="5" rx="9" ry="3" />
                            <path d="M3 5v14c0 1.66 4.03 3 9 3s9-1.34 9-3V5" />
                            <path d="M3 12c0 1.66 4.03 3 9 3s9-1.34 9-3" />
                        </svg>
                    </div>
                    <input class="box-label label-lower" type="text" value="users_db" readonly />
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 5: Cilindro CSS con tinte azul -->
        <div class="option-card">
            <h2>Opción 5: Cilindro Tintado</h2>
            <p class="desc">Cilindro con tinte azul sutil para diferenciar</p>
            <div class="preview">
                <div class="db-cylinder tinted">
                    <div class="cyl-top"></div>
                    <div class="cyl-body">
                        <input class="box-label" type="text" value="users_db" readonly />
                    </div>
                    <div class="cyl-bottom"></div>
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                </div>
            </div>
        </div>

        <!-- OPCIÓN 6: Box con outline/borde distinto -->
        <div class="option-card">
            <h2>Opción 6: Box + Borde DB</h2>
            <p class="desc">Box con doble borde y icono pequeño</p>
            <div class="preview">
                <div class="box-double-border">
                    <div class="inner-border">
                        <div class="db-mini-icon">⛁</div>
                        <input class="box-label" type="text" value="users_db" readonly />
                    </div>
                    <span class="dot dot-left"></span>
                    <span class="dot dot-top"></span>
                    <span class="dot dot-right"></span>
                    <span class="dot dot-bottom"></span>
                    <span class="resize-handle"></span>
                </div>
            </div>
        </div>

    </div>

    <!-- Comparación lado a lado con box normal -->
    <h2 class="section-title">Comparación con box normal</h2>
    <div class="comparison">
        <div class="comp-item">
            <span class="comp-label">Box Normal</span>
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
            <span class="comp-label">DB (Opción 3)</span>
            <div class="box-color-header">
                <div class="color-bar">
                    <span class="bar-icon">⛁</span>
                    <span class="bar-text">DATABASE</span>
                </div>
                <input class="box-label label-offset" type="text" value="users_db" readonly />
                <span class="dot dot-left"></span>
                <span class="dot dot-top"></span>
                <span class="dot dot-right"></span>
                <span class="dot dot-bottom"></span>
                <span class="resize-handle"></span>
            </div>
        </div>
        <div class="comp-arrow">→</div>
        <div class="comp-item">
            <span class="comp-label">DB (Opción 1)</span>
            <div class="db-cylinder">
                <div class="cyl-top"></div>
                <div class="cyl-body">
                    <input class="box-label" type="text" value="users_db" readonly />
                </div>
                <div class="cyl-bottom"></div>
                <span class="dot dot-left"></span>
                <span class="dot dot-top"></span>
                <span class="dot dot-right"></span>
                <span class="dot dot-bottom"></span>
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

    /* ===== ESTILOS COMPARTIDOS (idénticos al proyecto) ===== */

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

    .label-offset {
        top: 30px;
    }

    .label-lower {
        top: 38px;
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

    /* ===== BOX ESTÁNDAR (réplica exacta) ===== */

    .box-standard {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
    }

    /* ===== OPCIÓN 1 & 5: CILINDRO CSS ===== */

    .db-cylinder {
        position: relative;
        width: 130px;
        height: 120px;
    }

    .cyl-top {
        position: absolute;
        top: 0;
        left: 0;
        width: 130px;
        height: 36px;
        background: white;
        border-radius: 50%;
        box-shadow: 0 2px 10px rgba(0,0,0,0.2);
        z-index: 2;
    }

    .cyl-body {
        position: absolute;
        top: 18px;
        left: 0;
        width: 130px;
        height: 84px;
        background: white;
        box-shadow: 4px 0 20px rgba(0,0,0,0.15), -4px 0 20px rgba(0,0,0,0.15);
        overflow: hidden;
    }

    .cyl-body .box-label {
        top: 24px;
    }

    .cyl-bottom {
        position: absolute;
        bottom: 0;
        left: 0;
        width: 130px;
        height: 36px;
        background: white;
        border-radius: 50%;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        z-index: 1;
    }

    .db-cylinder .dot-left   { left: -6px; top: 55%; margin-top: -6px; }
    .db-cylinder .dot-top    { top: -4px; left: 50%; margin-left: -6px; z-index: 3; }
    .db-cylinder .dot-right  { right: -6px; top: 55%; margin-top: -6px; }
    .db-cylinder .dot-bottom { bottom: -4px; left: 50%; margin-left: -6px; z-index: 3; }

    /* Opción 5: tinte azul */
    .db-cylinder.tinted .cyl-top {
        background: linear-gradient(180deg, #e8f0fe 0%, white 100%);
    }
    .db-cylinder.tinted .cyl-body {
        background: linear-gradient(180deg, white 0%, #f0f4ff 100%);
    }
    .db-cylinder.tinted .cyl-bottom {
        background: #f0f4ff;
    }

    /* ===== OPCIÓN 2: EMOJI BADGE ===== */

    .box-type-badge {
        position: absolute;
        top: 6px;
        left: 8px;
        font-size: 16px;
        line-height: 1;
        z-index: 2;
    }

    .box-standard .box-label {
        top: 10px;
    }

    /* ===== OPCIÓN 3: COLOR BAR HEADER ===== */

    .box-color-header {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        overflow: hidden;
    }

    .color-bar {
        width: 100%;
        height: 22px;
        background: linear-gradient(90deg, #4a6cf7, #6495ed);
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 4px;
        border-radius: 12px 12px 0 0;
    }

    .bar-icon {
        font-size: 10px;
        color: white;
    }

    .bar-text {
        font-size: 8px;
        color: rgba(255,255,255,0.9);
        letter-spacing: 1.5px;
    }

    /* ===== OPCIÓN 4: SVG ICON ===== */

    .svg-icon {
        position: absolute;
        top: 6px;
        left: 50%;
        transform: translateX(-50%);
        z-index: 2;
    }

    /* ===== OPCIÓN 6: DOBLE BORDE ===== */

    .box-double-border {
        position: relative;
        width: 130px;
        height: 125px;
        background: white;
        border-radius: 12px;
        box-shadow: 0 4px 20px rgba(0,0,0,0.3);
        border: 2px solid #6495ed;
    }

    .inner-border {
        position: absolute;
        top: 3px;
        left: 3px;
        right: 3px;
        bottom: 3px;
        border: 1px solid rgba(100,149,237,0.3);
        border-radius: 8px;
    }

    .db-mini-icon {
        position: absolute;
        top: 4px;
        right: 6px;
        font-size: 14px;
        color: #6495ed;
        line-height: 1;
    }

    .inner-border .box-label {
        top: 10px;
        left: 8px;
        right: 8px;
    }

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
