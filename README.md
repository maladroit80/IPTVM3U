<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Player IPTV Stable</title>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <style>
        body { margin: 0; background: #000; display: flex; align-items: center; justify-content: center; height: 100vh; color: white; font-family: sans-serif; }
        video { width: 100%; height: 100%; max-width: 1200px; }
        #status { position: absolute; bottom: 20px; background: rgba(0,0,0,0.5); padding: 5px 10px; font-size: 12px; }
    </style>
</head>
<body>
    <video id="video" controls autoplay playsinline></video>
    <div id="status">Initialisation...</div>

    <script>
        const video = document.getElementById('video');
        const status = document.getElementById('status');
        const urlParams = new URLSearchParams(window.location.search);
        const streamUrl = urlParams.get('url');

        function loadStream(url, useProxy = false) {
            const finalUrl = useProxy ? "https://api.allorigins.win/raw?url=" + encodeURIComponent(url) : url;
            status.innerText = useProxy ? "Mode Proxy activé..." : "Connexion directe...";

            if (Hls.isSupported()) {
                const hls = new Hls({
                    enableWorker: true,
                    // Si on échoue en direct, on tentera avec le proxy
                    manifestLoadingMaxRetry: 1
                });
                
                hls.loadSource(finalUrl);
                hls.attachMedia(video);
                
                hls.on(Hls.Events.ERROR, (event, data) => {
                    if (data.fatal && !useProxy) {
                        console.log("Échec direct, tentative via Proxy...");
                        hls.destroy();
                        loadStream(url, true);
                    } else if (data.fatal) {
                        status.innerText = "Erreur fatale : " + data.details;
                    }
                });

                hls.on(Hls.Events.MANIFEST_PARSED, () => {
                    status.innerText = "Lecture en cours";
                    video.play();
                });
            }
        }

        if (streamUrl) {
            loadStream(streamUrl, false);
        } else {
            status.innerText = "Aucun flux détecté.";
        }
    </script>
</body>
</html>
