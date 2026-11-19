<script>
    import { onMount } from "svelte";
    import { profile } from "$lib/data.js";

    var canvas, ctx, container;
    var W = 500, H = 500, maxR = 200;
    var stars = [];
    var sim = 0;
    var rotX = 0, rotY = 0, tRotX = 0, tRotY = 0;
    var mxp = -9999, myp = -9999, hover = false;
    var raf = 0, visible = true, observer = null, reduced = false;

    function initGalaxy() {
        stars = [];
        maxR = Math.min(W, H) * 3;
        var n = Math.min(3600, Math.max(1200, Math.round((W * H) / 280)));
        for (var i = 0; i < n; i++) {
            var x, y, z, r, g, b, s;
            if (i < n * 0.18) {
                var rr = Math.pow(Math.random(), 1.7) * maxR * 0.28;
                var a = Math.random() * Math.PI * 2;
                var p = Math.pow(Math.random(), 0.6);
                x = Math.cos(a) * rr * p;
                z = Math.sin(a) * rr * p;
                y = (Math.random() - 0.5) * maxR * 0.14 * (1 - p * 0.5);
                r = 255; g = 250 - Math.random() * 25; b = 235 - Math.random() * 35;
                s = 0.9 + Math.random() * 2.2;
            } else {
                var arm = i % 3;
                var d = Math.pow(Math.random(), 0.72);
                var radius = d * maxR;
                var ang = (arm / 3) * Math.PI * 2 + d * 4.4 + (Math.random() - 0.5) * (0.25 + (1 - d) * 0.55);
                x = Math.cos(ang) * radius + (Math.random() - 0.5) * maxR * 0.09;
                z = Math.sin(ang) * radius + (Math.random() - 0.5) * maxR * 0.09;
                y = (Math.random() - 0.5) * maxR * (0.05 + 0.13 * (1 - d));
                if (d < 0.3) { r = 255; g = 215 + Math.random() * 35; b = 245 - Math.random() * 40; }
                else if (d < 0.65) { r = 195 + Math.random() * 45; g = 150 + Math.random() * 55; b = 255; }
                else { r = 140 + Math.random() * 55; g = 175 + Math.random() * 55; b = 255; }
                if (Math.random() > 0.93) { r = 235; g = 245; b = 255; }
                s = (Math.random() > 0.93 ? 1.4 : 0.45) + Math.random() * 1.5;
            }
            stars.push({ x: x, y: y, z: z, r: r, g: g, b: b, s: s, ph: Math.random() * Math.PI * 2, sp: 0.35 + Math.random() * 1.5 });
        }
    }

    function draw() {
        var bg = ctx.createRadialGradient(W / 2, H / 2, Math.min(W, H) * 0.04, W / 2, H / 2, Math.max(W, H) * 0.9);
        bg.addColorStop(0, "#0a1026");
        bg.addColorStop(0.45, "#050713");
        bg.addColorStop(1, "#010208");
        ctx.fillStyle = bg;
        ctx.fillRect(0, 0, W, H);

        var neb = ctx.createRadialGradient(W / 2, H / 2, 0, W / 2, H / 2, maxR * 1.5);
        neb.addColorStop(0, "rgba(130, 90, 190, 0.16)");
        neb.addColorStop(0.45, "rgba(70, 45, 150, 0.08)");
        neb.addColorStop(1, "rgba(0,0,0,0)");
        ctx.fillStyle = neb;
        ctx.fillRect(0, 0, W, H);

        var ry = sim * 0.085 + rotY;
        var rx = 1.0 + rotX;
        var cy = Math.cos(ry), sy = Math.sin(ry);
        var cxr = Math.cos(rx), sxr = Math.sin(rx);
        var fov = Math.min(W, H) * 0.35;
        var mx = W / 2, my = H / 2;
        var avoidR = Math.min(W, H) * 0.18

        ctx.globalCompositeOperation = "lighter";
        for (var i = 0; i < stars.length; i++) {
            var p = stars[i];
            var x1 = p.x * cy - p.z * sy;
            var z1 = p.x * sy + p.z * cy;
            var y1 = p.y * cxr - z1 * sxr;
            var z2 = p.y * sxr + z1 * cxr;
            var sc = fov / (fov + z2 + maxR * 0.5);
            if (sc <= 0) continue;
            var px = mx + x1 * sc;
            var py = my + y1 * sc;
            if (hover) {
                var dx = px - mxp, dy = py - myp;
                var d2 = dx * dx + dy * dy;
                if (d2 < avoidR * avoidR && d2 > 0.01) {
                    var d = Math.sqrt(d2);
                    var force = (1 - d / avoidR);
                    var push = force * force * avoidR * 0.30;
                    px += (dx / d) * push;
                    py += (dy / d) * push;
                }
            }
            if (px < -40 || px > W + 40 || py < -40 || py > H + 40) continue;
            var depth = Math.max(0, Math.min(1, (z2 + maxR) / (2.3 * maxR)));
            var tw = 0.6 + 0.4 * Math.sin(sim * p.sp + p.ph);
            ctx.globalAlpha = (0.12 + (1 - depth) * 0.88) * tw;
            ctx.fillStyle = "rgb(" + (p.r | 0) + "," + (p.g | 0) + "," + (p.b | 0) + ")";
            ctx.beginPath();
            ctx.arc(px, py, Math.max(0.2, p.s * sc * 2.8), 0, Math.PI * 2);
            ctx.fill();
        }
        ctx.globalCompositeOperation = "source-over";
        ctx.globalAlpha = 1;
    }

    function frame() {
        sim += 1 / 60;
        rotX += (tRotX - rotX) * 0.045;
        rotY += (tRotY - rotY) * 0.045;
        draw();
        raf = visible ? requestAnimationFrame(frame) : 0;
    }

    function start() { if (!raf && !reduced) raf = requestAnimationFrame(frame); }
    function stop() { cancelAnimationFrame(raf); raf = 0; }

    function onMove(e) {
        var r = container.getBoundingClientRect();
        mxp = e.clientX - r.left;
        myp = e.clientY - r.top;
        hover = true;
        tRotY = ((mxp / r.width) - 0.5) * 1.6;
        tRotX = ((myp / r.height) - 0.5) * 0.65;
    }
    function onLeave() { tRotY = 0; tRotX = 0; hover = false; mxp = -9999; myp = -9999; }

    function resize() {
        var r = container.getBoundingClientRect();
        if (r.width === W && r.height === H && stars.length) return;
        W = r.width; H = r.height;
        var dpr = Math.min(window.devicePixelRatio || 1, 2);
        canvas.width = Math.round(W * dpr);
        canvas.height = Math.round(H * dpr);
        ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
        initGalaxy();
    }

    function init() {
        canvas = document.getElementById("c");
        container = document.querySelector(".hero-container");
        ctx = canvas.getContext("2d");
        resize();
        window.addEventListener("resize", resize);
        if (reduced) { draw(); return; }
        container.addEventListener("pointermove", onMove);
        container.addEventListener("pointerleave", onLeave);
        observer = new IntersectionObserver(function (e) {
            visible = e[0].isIntersecting;
            visible ? start() : stop();
        });
        observer.observe(container);
        start();
    }

    function teardown() {
        stop();
        if (observer) observer.disconnect();
        window.removeEventListener("resize", resize);
        if (container) {
            container.removeEventListener("pointermove", onMove);
            container.removeEventListener("pointerleave", onLeave);
        }
    }

    onMount(function () {
        reduced = window.matchMedia("(prefers-reduced-motion: reduce)").matches;
        init();
        return teardown;
    });
</script>

<header class="hero-container" id="hero">
    <canvas id="c" aria-hidden="true"></canvas>
    <div class="hero-content">
        <p class="eyebrow">{profile.role} · {profile.location}</p>
        <h1>{profile.name}</h1>
        <p class="tagline">{profile.tagline}</p>
        <div class="cta">
            <a class="btn primary" href="#work">See my work</a>
            <a class="btn" href="#contact">Get in touch</a>
        </div>
    </div>
    <a class="scroll-hint" href="#about" aria-label="Scroll to about">
        <span></span>
    </a>
</header>

<style>
    .hero-container {
        background: var(--bg);
        position: relative;
        overflow: hidden;
        display: flex;
        /* min-height, not height: on short / landscape phones the hero grows to fit its
           content instead of clipping the buttons. */
        min-height: 100vh;
        min-height: 100svh;
    }

    canvas {
        position: absolute;
        inset: 0;
        width: 100%;
        height: 100%;
        /* Let vertical scrolling and pinch-zoom stay with the browser. */
        touch-action: pan-y pinch-zoom;
    }

    .hero-content {
        position: relative;
        flex: 1;
        min-width: 0;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        text-align: center;
        /* Top clears the fixed nav, bottom clears the scroll hint. */
        padding: calc(4.75rem + env(safe-area-inset-top)) var(--gutter-r) 5.25rem var(--gutter-l);
        pointer-events: none; /* let the pointer reach the canvas */
        z-index: 2;
    }

    .eyebrow {
        font-family: var(--mono);
        font-size: 0.85rem;
        letter-spacing: 0.08em;
        text-transform: uppercase;
        color: var(--accent);
        margin-bottom: 1rem;
        text-wrap: balance;
    }

    h1 {
        color: var(--fg);
        font-size: clamp(2.6rem, 9vw, 6rem);
        font-weight: 800;
        letter-spacing: -0.03em;
        line-height: 1;
        text-shadow: 0 0 40px rgba(0, 0, 0, 0.9);
    }

    .tagline {
        max-width: 38rem;
        margin-top: 1.25rem;
        font-size: clamp(1rem, 2.4vw, 1.2rem);
        line-height: 1.6;
        color: var(--muted);
        text-shadow: 0 0 20px rgba(0, 0, 0, 1);
        text-wrap: balance;
    }

    .cta {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 0.75rem;
        margin-top: 2rem;
        pointer-events: auto;
    }

    .scroll-hint {
        position: absolute;
        bottom: 2rem;
        left: 50%;
        transform: translateX(-50%);
        width: 26px;
        height: 42px;
        border: 2px solid var(--line-strong);
        border-radius: 14px;
        z-index: 2;
    }

    .scroll-hint span {
        position: absolute;
        top: 8px;
        left: 50%;
        width: 4px;
        height: 8px;
        margin-left: -2px;
        border-radius: 2px;
        background: var(--accent);
        animation: wheel 1.8s ease-in-out infinite;
    }

    /* Extend the hint's tap area to 44px without changing how it looks. */
    .scroll-hint::before {
        content: "";
        position: absolute;
        inset: -3px -11px;
    }

    /* Landscape phones: no room for the hint; tighten the vertical rhythm. */
    @media (max-height: 480px) {
        .hero-content {
            padding-top: calc(4rem + env(safe-area-inset-top));
            padding-bottom: 1.5rem;
        }

        .scroll-hint {
            display: none;
        }

        h1 {
            font-size: clamp(2.6rem, 7vw, 4rem);
        }

        .tagline {
            margin-top: 0.9rem;
        }

        .cta {
            margin-top: 1.4rem;
        }
    }

    @keyframes wheel {
        0% { opacity: 1; transform: translateY(0); }
        100% { opacity: 0; transform: translateY(14px); }
    }

    @media (prefers-reduced-motion: reduce) {
        .scroll-hint span { animation: none; }
    }
</style>
