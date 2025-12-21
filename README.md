<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Player IPTV Final</title>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <style>
        body { margin: 0; background: #000; display: flex; align-items: center; justify-content: center; height: 100vh; }
        video { width: 100%; height: 100%; }
    </style>
</head>
<body>
    <video id="video" controls autoplay></video>
    <script>
        const video = document.getElementById('video');
        const urlParams = new URLSearchParams(window.location.search);
        const streamUrl = urlParams.get('url');

        if (streamUrl) {
            // TEST PROXY 1 : AllOrigins (plus robuste pour les manifests)
            const finalUrl = "https://api.allorigins.win/raw?url=" + encodeURIComponent(streamUrl);

            if (Hls.isSupported()) {
                const hls = new Hls({
                    xhrSetup: function(xhr, url) {
                        // On essaie de forcer le header pour paraître légitime
                        xhr.withCredentials = false;
                    }
                });
                hls.loadSource(finalUrl);
                hls.attachMedia(video);
                hls.on(Hls.Events.MANIFEST_PARSED, () => video.play());
                
                // Si Proxy 1 échoue, on pourrait basculer sur Proxy 2 automatiquement
                hls.on(Hls.Events.ERROR, (event, data) => {
                    if (data.details === "manifestLoadError") {
                        console.log("Essai avec Proxy de secours...");
                        const backupUrl = "https://corsproxy.io/?" + encodeURIComponent(streamUrl);
                        hls.loadSource(backupUrl);
                    }
                });
            }
        }
    </script>
</body>
</html>
