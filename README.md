<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Mon Player Gratuit</title>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <style>
        body { margin: 0; background: #000; color: white; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; font-family: sans-serif; }
        video { width: 90%; max-width: 800px; border: 1px solid #333; border-radius: 10px; }
        .status { margin-top: 15px; font-size: 14px; color: #aaa; }
    </style>
</head>
<body>

    <video id="video" controls autoplay></video>
    <div class="status" id="status">Chargement du lecteur...</div>

    <script>
        const video = document.getElementById('video');
        const status = document.getElementById('status');
        
        // On récupère le lien M3U8 qui est après ?url=
        const urlParams = new URLSearchParams(window.location.search);
        const streamUrl = urlParams.get('url');

        if (streamUrl) {
            // Utilisation de corsproxy.io pour débloquer le flux NetPlus
            const finalUrl = "https://corsproxy.io/?" + encodeURIComponent(streamUrl);
            
            if (Hls.isSupported()) {
                const hls = new Hls();
                hls.loadSource(finalUrl);
                hls.attachMedia(video);
                hls.on(Hls.Events.MANIFEST_PARSED, () => {
                    status.innerText = "Lecture en cours...";
                    video.play();
                });
            } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
                video.src = finalUrl;
            }
        } else {
            status.innerText = "Erreur : Aucune URL de flux détectée.";
        }
    </script>
</body>
</html>
