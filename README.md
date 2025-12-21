<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Player TV - GitHub</title>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <style>
        body { margin: 0; background: #000; color: white; display: flex; align-items: center; justify-content: center; height: 100vh; overflow: hidden; font-family: sans-serif; }
        video { width: 100%; height: 100%; max-width: 1200px; background: #000; }
        #error-msg { position: absolute; color: red; background: rgba(0,0,0,0.7); padding: 10px; display: none; }
    </style>
</head>
<body>

    <div id="error-msg">Erreur de chargement du flux</div>
    <video id="video" controls autoplay playsinline></video>

    <script>
        const video = document.getElementById('video');
        const errorMsg = document.getElementById('error-msg');
        
        // Extraction de l'URL du flux
        const urlParams = new URLSearchParams(window.location.search);
        const streamUrl = urlParams.get('url');

        if (streamUrl) {
            // Utilisation du proxy CodeTabs pour contourner les blocages CORS
            const finalUrl = "https://api.codetabs.com/v1/proxy?quest=" + encodeURIComponent(streamUrl);

            if (Hls.isSupported()) {
                const hls = new Hls({
                    enableWorker: true,
                    lowLatencyMode: true,
                    backBufferLength: 90
                });

                hls.loadSource(finalUrl);
                hls.attachMedia(video);

                hls.on(Hls.Events.MANIFEST_PARSED, () => {
                    video.play().catch(() => {
                        console.log("Lecture automatique bloquée, l'utilisateur doit cliquer sur Play");
                    });
                });

                hls.on(Hls.Events.ERROR, (event, data) => {
                    if (data.fatal) {
                        console.error("Erreur fatale HLS:", data.type);
                        errorMsg.style.display = "block";
                        errorMsg.innerText = "Erreur flux : " + data.details;
                    }
                });

            } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
                // Pour Safari (iPhone/Mac) qui lit le HLS nativement
                video.src = finalUrl;
            }
        } else {
            errorMsg.style.display = "block";
            errorMsg.innerText = "Aucune URL fournie. Ajoutez ?url=LIEN_M3U8";
        }
    </script>
</body>
</html>
