# clip-video-ai
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ClipShort — Video to Shorts</title>
  <style>
    :root {
      --bg: #0b1020;
      --panel: rgba(18, 26, 46, 0.92);
      --input-bg: #0b1222;
      --text: #eef3ff;
      --muted: #9ba9c7;
      --accent: #7c5cff;
      --cyan: #22d3ee;
      --border: #263451;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100vh;
      background: radial-gradient(ellipse at top, #182345 0, var(--bg) 55%);
      color: var(--text);
      font: 15px/1.5 Inter, system-ui, "Segoe UI", sans-serif;
    }

    /* ---------- Layout ---------- */
    header {
      max-width: 1120px;
      margin: auto;
      padding: 28px 22px 14px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
    }

    main { max-width: 1120px; margin: 0 auto; padding: 18px 22px 48px; }

    .layout {
      display: grid;
      grid-template-columns: 1.15fr 0.85fr;
      gap: 18px;
    }

    footer { text-align: center; color: #6e7d9d; font-size: 12px; padding: 20px; }

    /* ---------- Header & hero ---------- */
    .logo { font-size: 24px; font-weight: 900; letter-spacing: -1px; }
    .logo span { color: var(--cyan); }
    .logo small { font-size: 11px; color: #8290af; }

    .tag {
      color: var(--muted);
      font-size: 12px;
      border: 1px solid var(--border);
      padding: 6px 10px;
      border-radius: 99px;
    }

    .hero { margin: 18px 0 26px; }
    .hero h1 {
      font-size: clamp(30px, 5vw, 48px);
      line-height: 1.05;
      letter-spacing: -1.7px;
      margin: 0 0 12px;
    }
    .hero h1 em {
      font-style: normal;
      background: linear-gradient(90deg, var(--cyan), #a78bfa);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
    }
    .hero p { color: var(--muted); max-width: 650px; margin: 0; }

    /* ---------- Panels ---------- */
    .panel {
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 20px;
      padding: 20px;
      box-shadow: 0 20px 60px rgba(0, 0, 0, 0.13);
    }
    .panel h2 { font-size: 17px; margin: 0 0 16px; }

    /* ---------- Upload ---------- */
    .drop {
      border: 1.5px dashed #46577d;
      background: #0e1629;
      border-radius: 16px;
      padding: 28px 18px;
      text-align: center;
      cursor: pointer;
      transition: 0.2s;
    }
    .drop:hover, .drop.drag { border-color: var(--cyan); background: #111e35; }
    .drop .icon { font-size: 28px; }
    .drop strong { display: block; font-size: 16px; margin: 8px 0; word-break: break-all; }
    .drop small { color: var(--muted); }
    input[type="file"] { display: none; }

    .video-wrap {
      margin-top: 16px;
      background: #060912;
      border-radius: 14px;
      overflow: hidden;
      min-height: 180px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: var(--muted);
    }
    .video-wrap video { display: block; max-width: 100%; max-height: 360px; }
    .empty { padding: 35px; text-align: center; }

    /* ---------- Form fields ---------- */
    .field { margin: 17px 0; }
    .field label {
      display: flex;
      justify-content: space-between;
      gap: 10px;
      font-size: 13px;
      color: #c7d2ea;
      margin-bottom: 8px;
    }
    .field input[type="number"],
    .field select {
      width: 100%;
      background: var(--input-bg);
      color: var(--text);
      border: 1px solid var(--border);
      padding: 11px 12px;
      border-radius: 10px;
      font: inherit;
    }
    .range-row {
      display: grid;
      grid-template-columns: 1fr 100px;
      gap: 10px;
      align-items: center;
    }
    .range-row input[type="range"] { width: 100%; accent-color: var(--accent); }
    .hint { font-size: 12px; color: var(--muted); margin-top: 6px; }
    .settings { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }

    /* ---------- Buttons ---------- */
    .btn {
      border: 0;
      border-radius: 12px;
      padding: 13px 16px;
      font: inherit;
      font-weight: 750;
      cursor: pointer;
      transition: 0.15s;
      color: var(--text);
    }
    .btn:disabled { opacity: 0.45; cursor: not-allowed; }
    .btn:hover:not(:disabled) { transform: translateY(-1px); filter: brightness(1.08); }
    .btn.primary {
      width: 100%;
      background: linear-gradient(100deg, var(--accent), #5b8cff);
      color: #fff;
      box-shadow: 0 8px 24px rgba(108, 92, 255, 0.21);
    }
    .btn.secondary { background: #202c47; border: 1px solid var(--border); }

    /* ---------- Progress & output ---------- */
    .progress {
      height: 8px;
      background: #0a1020;
      border-radius: 99px;
      overflow: hidden;
      margin: 14px 0 8px;
    }
    .bar {
      height: 100%;
      width: 0;
      background: linear-gradient(90deg, var(--accent), var(--cyan));
      transition: width 0.2s;
    }
    .status { font-size: 12px; color: var(--muted); min-height: 18px; }

    .notice {
      font-size: 12px;
      color: var(--muted);
      margin: 16px 0;
      padding: 12px;
      background: #0d1527;
      border-radius: 10px;
    }

    .output { margin-top: 18px; }
    .output video {
      width: 100%;
      max-height: 420px;
      object-fit: contain;
      background: #05070c;
      border-radius: 12px;
    }
    .output-actions { display: flex; gap: 10px; margin-top: 12px; }
    .output-actions .btn { flex: 1; text-align: center; text-decoration: none; }

    /* ---------- Responsive ---------- */
    @media (max-width: 800px) {
      .layout { grid-template-columns: 1fr; }
      header { padding-top: 20px; }
    }
    @media (max-width: 480px) {
      main { padding: 12px; }
      .panel { padding: 15px; }
      .range-row { grid-template-columns: 1fr 82px; }
    }
  </style>
</head>
<body>
  <header>
    <div class="logo">Clip<span>Short</span> <small>STUDIO</small></div>
    <div class="tag">VIDEO CLIPPER · 30 SEC</div>
  </header>

  <main>
    <section class="hero">
      <h1>Video panjang.<br><em>Shorts siap upload.</em></h1>
      <p>Potong momen terbaik dari video kamu, ubah ke format vertikal, lalu ekspor sebagai MP4.</p>
    </section>

    <div class="layout">
      <!-- ① Upload & trim -->
      <section class="panel">
        <h2>① Upload video</h2>

        <div class="drop" id="drop">
          <div class="icon">⬆</div>
          <strong id="dropTitle">Seret video ke sini</strong>
          <small>atau klik untuk memilih file · MP4, MOV, WebM</small>
          <input type="file" id="file" accept="video/*">
        </div>

        <div class="video-wrap" id="videoWrap">
          <div class="empty">Preview video akan tampil di sini</div>
        </div>

        <div class="field">
          <label>Durasi video <span id="durationText">—</span></label>
        </div>

        <div class="field">
          <label for="start">Mulai klip (detik)</label>
          <div class="range-row">
            <input id="startRange" type="range" min="0" max="0" step="0.1" value="0">
            <input id="start" type="number" min="0" step="0.1" value="0">
          </div>
        </div>

        <div class="field">
          <label for="clipLength">Durasi klip</label>
          <select id="clipLength">
            <option value="30" selected>30 detik (YouTube Shorts)</option>
            <option value="15">15 detik</option>
            <option value="20">20 detik</option>
            <option value="45">45 detik</option>
            <option value="60">60 detik</option>
          </select>
          <div class="hint">Durasi klip akan disesuaikan jika video sumber lebih pendek.</div>
        </div>
      </section>

      <!-- ② Export -->
      <section class="panel">
        <h2>② Pengaturan ekspor</h2>

        <div class="settings">
          <div class="field">
            <label for="format">Format</label>
            <select id="format">
              <option value="vertical" selected>Vertikal 9:16</option>
              <option value="original">Pertahankan rasio asli</option>
              <option value="square">Kotak 1:1</option>
            </select>
          </div>
          <div class="field">
            <label for="resolution">Resolusi</label>
            <select id="resolution">
              <option value="1080" selected>1080p</option>
              <option value="720">720p</option>
            </select>
          </div>
          <div class="field">
            <label for="fps">FPS</label>
            <select id="fps">
              <option value="30" selected>30 FPS</option>
              <option value="60">60 FPS</option>
            </select>
          </div>
          <div class="field">
            <label for="audio">Audio</label>
            <select id="audio">
              <option value="yes" selected>Sertakan audio</option>
              <option value="no">Tanpa audio</option>
            </select>
          </div>
        </div>

        <div class="notice">
          🔒 Video diproses langsung di browser perangkat kamu. File tidak diunggah ke server aplikasi.
          Proses pertama mungkin membutuhkan waktu untuk memuat mesin FFmpeg.
        </div>

        <button class="btn primary" id="make" disabled>✂️ Buat klip video</button>
        <div class="progress"><div class="bar" id="bar"></div></div>
        <div class="status" id="status">Upload video untuk mulai.</div>

        <div class="output" id="output" hidden>
          <h2>③ Hasil klip</h2>
          <video id="resultVideo" controls playsinline></video>
          <div class="output-actions">
            <a class="btn primary" id="download" download="clipshort.mp4">⬇ Download MP4</a>
            <button class="btn secondary" id="reset">Buat klip lain</button>
          </div>
        </div>
      </section>
    </div>
  </main>

  <footer>ClipShort Studio · Dibuat untuk workflow YouTube Shorts</footer>

  <script type="module">
    import { FFmpeg } from "https://cdn.jsdelivr.net/npm/@ffmpeg/ffmpeg@0.12.10/dist/esm/index.js";
    import { fetchFile, toBlobURL } from "https://cdn.jsdelivr.net/npm/@ffmpeg/util@0.12.1/dist/esm/index.js";

    const CORE_BASE = "https://cdn.jsdelivr.net/npm/@ffmpeg/core@0.12.6/dist/umd";
    const $ = (id) => document.getElementById(id);

    const els = {
      drop: $("drop"),
      dropTitle: $("dropTitle"),
      file: $("file"),
      videoWrap: $("videoWrap"),
      durationText: $("durationText"),
      startRange: $("startRange"),
      start: $("start"),
      clipLength: $("clipLength"),
      format: $("format"),
      resolution: $("resolution"),
      fps: $("fps"),
      audio: $("audio"),
      make: $("make"),
      bar: $("bar"),
      status: $("status"),
      output: $("output"),
      resultVideo: $("resultVideo"),
      download: $("download"),
      reset: $("reset"),
    };

    const state = {
      ffmpeg: new FFmpeg(),
      ffmpegReady: false,
      sourceFile: null,
      sourceURL: null,
      outputURL: null,
      duration: 0,
    };

    // ---------- Helpers ----------
    const setStatus = (text) => { els.status.textContent = text; };
    const setProgress = (pct) => {
      els.bar.style.width = Math.max(0, Math.min(100, pct)) + "%";
    };
    const even = (n) => Math.round(n / 2) * 2;

    function formatTime(seconds) {
      if (!Number.isFinite(seconds)) return "—";
      const m = Math.floor(seconds / 60);
      const s = (seconds % 60).toFixed(1).padStart(4, "0");
      return `${m}:${s}`;
    }

    function revoke(url) { if (url) URL.revokeObjectURL(url); }

    // ---------- Upload ----------
    els.drop.addEventListener("click", () => els.file.click());
    els.drop.addEventListener("dragover", (e) => {
      e.preventDefault();
      els.drop.classList.add("drag");
    });
    els.drop.addEventListener("dragleave", () => els.drop.classList.remove("drag"));
    els.drop.addEventListener("drop", (e) => {
      e.preventDefault();
      els.drop.classList.remove("drag");
      const file = e.dataTransfer.files[0];
      if (file) loadVideo(file);
    });
    els.file.addEventListener("change", () => {
      if (els.file.files[0]) loadVideo(els.file.files[0]);
    });

    function loadVideo(file) {
      if (!file.type.startsWith("video/")) {
        setStatus("File bukan video. Pilih file video.");
        return;
      }

      state.sourceFile = file;
      revoke(state.sourceURL);
      state.sourceURL = URL.createObjectURL(file);

      const video = document.createElement("video");
      video.src = state.sourceURL;
      video.controls = true;
      video.playsInline = true;
      els.videoWrap.replaceChildren(video);

      video.addEventListener("loadedmetadata", () => {
        state.duration = video.duration;
        els.durationText.textContent = formatTime(state.duration);

        const max = Math.max(0, state.duration - 0.1);
        els.startRange.max = max;
        els.start.max = max;
        els.startRange.value = 0;
        els.start.value = 0;

        els.make.disabled = false;
        setStatus("Video siap. Tentukan bagian yang ingin dipotong.");
      });

      els.dropTitle.textContent = file.name;
    }

    // ---------- Start-time inputs (kept in sync) ----------
    els.startRange.addEventListener("input", () => {
      els.start.value = els.startRange.value;
    });
    els.start.addEventListener("input", () => {
      const n = Math.max(0, Math.min(Number(els.start.value) || 0, state.duration));
      els.startRange.value = n;
    });

    // ---------- FFmpeg ----------
    async function ensureFFmpeg() {
      if (state.ffmpegReady) return;
      setStatus("Memuat FFmpeg… (pertama kali bisa agak lama)");

      state.ffmpeg.on("progress", ({ progress }) => setProgress(progress * 100));
      state.ffmpeg.on("log", ({ message }) => {
        if (message.includes("Error")) console.warn(message);
      });

      await state.ffmpeg.load({
        coreURL: await toBlobURL(`${CORE_BASE}/ffmpeg-core.js`, "text/javascript"),
        wasmURL: await toBlobURL(`${CORE_BASE}/ffmpeg-core.wasm`, "application/wasm"),
      });
      state.ffmpegReady = true;
    }

    function buildVideoFilter(format, res) {
      if (format === "vertical") {
        const h = even((res * 16) / 9);
        return `scale=${res}:${h}:force_original_aspect_ratio=increase,crop=${res}:${h},setsar=1`;
      }
      if (format === "square") {
        return `scale=${res}:${res}:force_original_aspect_ratio=increase,crop=${res}:${res},setsar=1`;
      }
      return `scale=${res}:-2:force_original_aspect_ratio=decrease,setsar=1`;
    }

    async function createClip() {
      const { ffmpeg, sourceFile, duration } = state;
      const ext = (sourceFile.name.split(".").pop() || "mp4").toLowerCase().replace(/[^a-z0-9]/g, "") || "mp4";
      const inputName = `input.${ext}`;
      const outputName = "clip.mp4";

      setStatus("Menyiapkan video…");
      await ffmpeg.writeFile(inputName, await fetchFile(sourceFile));

      const start = Math.max(0, Number(els.start.value) || 0);
      const length = Math.min(Number(els.clipLength.value), Math.max(0.1, duration - start));
      const vf = buildVideoFilter(els.format.value, Number(els.resolution.value));

      const args = [
        "-ss", String(start),
        "-i", inputName,
        "-t", String(length),
        "-vf", vf,
        "-r", els.fps.value,
        "-c:v", "libx264",
        "-preset", "ultrafast",
        "-crf", "23",
        "-pix_fmt", "yuv420p",
      ];
      if (els.audio.value === "yes") args.push("-c:a", "aac", "-b:a", "128k");
      else args.push("-an");
      args.push("-movflags", "+faststart", outputName);

      setStatus("Memotong dan mengonversi video…");
      await ffmpeg.exec(args);

      const data = await ffmpeg.readFile(outputName);

      // Free virtual-filesystem memory
      await ffmpeg.deleteFile(inputName).catch(() => {});
      await ffmpeg.deleteFile(outputName).catch(() => {});

      return new Blob([data.buffer], { type: "video/mp4" });
    }

    els.make.addEventListener("click", async () => {
      if (!state.sourceFile) return;

      els.make.disabled = true;
      els.output.hidden = true;
      setProgress(0);

      try {
        await ensureFFmpeg();
        const blob = await createClip();

        revoke(state.outputURL);
        state.outputURL = URL.createObjectURL(blob);

        els.resultVideo.src = state.outputURL;
        els.download.href = state.outputURL;
        els.download.download = `short-${Date.now()}.mp4`;
        els.output.hidden = false;

        setProgress(100);
        setStatus("Selesai! Klip siap diunduh.");
      } catch (err) {
        console.error(err);
        setStatus("Gagal memproses video. Coba MP4 yang lebih kecil atau browser terbaru.");
      } finally {
        els.make.disabled = false;
      }
    });

    els.reset.addEventListener("click", () => {
      els.output.hidden = true;
      els.resultVideo.removeAttribute("src");
      setProgress(0);
      setStatus("Atur titik mulai lalu buat klip lagi.");
    });

    window.addEventListener("beforeunload", () => {
      revoke(state.sourceURL);
      revoke(state.outputURL);
    });
  </script>
</body>
</html>
