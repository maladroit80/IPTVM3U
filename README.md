<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,viewport-fit=cover">
  <title>Lecteur IPTV</title>
  <link href="https://cdn.jsdelivr.net/npm/video.js@8.22.0/dist/video-js.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/video.js@8.22.0/dist/video.min.js"></script>
  <style>
    html, body { margin: 0; padding: 0; height: 100%; width: 100%; background: #000; overflow: hidden; }
    #player-container { position: fixed; inset: 0; width: 100%; height: 100%; display: flex; align-items: center; justify-content: center; }
    .video-js { width: 100% !important; height: 100% !important; }
    
    /* Style du Logo en haut à droite */
    .vjs-logo { 
      position: absolute; 
      z-index: 10; 
      top: 15px; 
      right: 15px; 
      pointer-events: none; 
    }
    .vjs-logo img { 
      max-height: 50px; 
      width: auto; 
      filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.5)); /* Pour que le logo ressorte mieux */
    }

    /* Style du Watermark en haut à gauche */
    .vjs-watermark { 
      position: absolute; 
      z-index: 10; 
      top: 15px; 
      left: 15px; 
      background: rgba(0,0,0,0.5); 
      color: white; 
      padding: 5px 12px; 
      border-radius: 4px; 
      font-family: sans-serif;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div id="player-container">
    <video id="video" class="video-js vjs-default-skin" controls autoplay playsinline preload="auto"></video>
  </div>

  <script>
    const params = new URLSearchParams(window.location.search);
    const url = params.get("url");

    // ON UTILISE UN PROXY POUR LE FLUX ET LES IMAGES SI BESOIN
    const finalUrl = url ? "https://api.allorigins.win/raw?url=" + encodeURIComponent(url) : "";

    const player = videojs('video', {
      autoplay: true,
      controls: true,
      fluid: false
    });

    // --- AJOUT DU LOGO ---
    const logo = document.createElement('div');
    logo.className = 'vjs-logo';
    // Utilisation d'un logo de test qui fonctionne partout (on pourra changer l'adresse après)
    logo.innerHTML = `<img src="https://raw.githubusercontent.com/maladroit80/IPTVM3U/master/logo.png" onerror="this.src='https://via.placeholder.com/150x50/000000/FFFFFF?text=TV+LIVE'" alt="Logo">`;
    player.el().appendChild(logo);

    // --- AJOUT DU WATERMARK ---
    const watermark = document.createElement('div');
    watermark.className = 'vjs-watermark';
    watermark.innerHTML = `DIRECT TV`;
    player.el().appendChild(watermark);

    // Chargement du flux
    if (url) {
        player.src({ src: finalUrl, type: 'application/x-mpegURL' });
    }

    player.on('error', function() {
       if(url) {
          // Deuxième chance avec un autre proxy si le premier échoue
          player.src({
            src: "https://api.codetabs.com/v1/proxy?quest=" + encodeURIComponent(url),
            type: 'application/x-mpegURL'
          });
       }
    });
  </script>
</body>
</html>
