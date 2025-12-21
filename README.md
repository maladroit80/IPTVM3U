<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Player TV</title>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <style>
        body { margin: 0; background: #000; color: white; display: flex; align-items: center; justify-content: center; height: 100vh; overflow: hidden; }
        video { width: 100%; height: 100%; max-width: 1000px; }
    </style>
</head>
<body>
    <video id="video" controls autoplay></video>
    <script>
        const video = document.getElementById('video');
        const urlParams = new URLSearchParams(window.location.search);
        const streamUrl = urlParams.get('url');
        if (streamUrl) {
            const finalUrl = "https://corsproxy.io/?" + encodeURIComponent(streamUrl);
            if (Hls.isSupported()) {
                const hls = new Hls();
                hls.loadSource(finalUrl);
                hls.attachMedia(video);
                hls.on(Hls.Events.MANIFEST_PARSED, () => video.play());
            } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
                video.src = finalUrl;
            }
        }
    </script>
</body>
</html>
