<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,viewport-fit=cover">
  <title>Lecteur GratuitTV - Multi Audio</title>
  <meta name="referrer" content="no-referrer">
  <meta name="robots" content="noindex">

  <!-- Video.js -->
  <link href="https://cdn.jsdelivr.net/npm/video.js@8.22.0/dist/video-js.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/video.js@8.22.0/dist/video.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/videojs-http-streaming@2.16.0/dist/videojs-http-streaming.min.js"></script>

  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      width: 100%;
      background: #000;
      overflow: hidden;
      font-family: sans-serif;
    }

    #player-container {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      background: #000;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .video-js {
      width: 100% !important;
      height: 100% !important;
      max-height: 100dvh;
      background: #000;
      object-fit: contain;
    }

    /* Logo et watermark intégrés au player */
    .vjs-logo,
    .vjs-watermark {
      position: absolute;
      z-index: 10000;
      pointer-events: none;
    }

    .vjs-logo {
      top: 10px;
      right: 10px;
    }

    .vjs-logo img {
      max-height: 40px;
      width: auto;
    }

    .vjs-watermark {
      top: 15px;
      left: 15px;
      background: rgba(0,0,0,0.4);
      color: white;
      padding: 6px 10px;
      border-radius: 6px;
      font-size: 15px;
    }

    .vjs-watermark a {
      color: #0af;
      font-weight: bold;
      text-decoration: underline;
    }
  </style>
</head>
<body>
  <div id="player-container">
    <video id="video" class="video-js vjs-default-skin" controls autoplay playsinline preload="auto"
      poster="http://gratuittv.free.fr/player/ag-66-4.png"></video>
  </div>

  <script>
    const params = new URLSearchParams(window.location.search);
    const url = params.get("url");
    const fallbackVideo = "http://gratuittv.free.fr/video/Offtv.mp4";

    const player = videojs('video', {
      controls: true,
      autoplay: true,
      preload: 'auto',
      playsinline: true,
      fluid: false,
      controlBar: {
        children: [
          'playToggle',
          'volumePanel',
          'currentTimeDisplay',
          'timeDivider',
          'durationDisplay',
          'progressControl',
          'remainingTimeDisplay',
          'customControlSpacer',
          'fullscreenToggle',
          'subtitlesButton',
          'audioTrackButton'
        ]
      }
    });

    // --- Ajout du logo dans le player ---
    const logo = document.createElement('div');
    logo.className = 'vjs-logo';
    logo.innerHTML = `<img src="http://gratuittv.free.fr/image/logo.png" alt="Logo GratuitTV">`;
    player.el().appendChild(logo);

    // --- Ajout du watermark dans le player ---
    const watermark = document.createElement('div');
    watermark.className = 'vjs-watermark';
    watermark.innerHTML = `<a href="http://gratuittv.free.nf" target="_blank">GratuitTV</a>`;
    player.el().appendChild(watermark);

    // --- Charger le flux vidéo ---
    player.src({
      src: url || fallbackVideo,
      type: url ? 'application/x-mpegURL' : 'video/mp4'
    });

    // --- Ajustement dynamique de la hauteur visible ---
    function resizePlayer() {
      const container = document.getElementById('player-container');
      container.style.height = window.innerHeight + 'px';
    }

    window.addEventListener('resize', resizePlayer);
    window.addEventListener('orientationchange', resizePlayer);
    resizePlayer();

    // --- Gestion des erreurs ---
    player.on('error', () => {
      console.warn("Erreur flux HLS, fallback activé");
      player.src({ src: fallbackVideo, type: 'video/mp4' });
      player.play().catch(()=>{});
    });
  </script>
</body>
</html>
