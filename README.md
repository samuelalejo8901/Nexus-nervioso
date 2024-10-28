<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página con Cuadro de Mensajes y Envío</title>
    <style>
        body {
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #FFD1DC, #FFB6C1, #FFC0CB, #FF69B4);
            background-size: 200% 200%;
            animation: gradientShift 5s ease infinite;
        }

        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .relieve {
    width: 60%;
    height: 60vh;
    padding: 20px;
    background: linear-gradient(135deg, #a8e6a3, #8ecf81, #6bbf5d, #47a743);
    border-radius: 20px;
    box-shadow: 10px 10px 20px rgba(0, 0, 0, 0.2),
                -10px -10px 20px rgba(255, 255, 255, 0.6),
                inset 10px 10px 20px rgba(0, 0, 0, 0.2),
                inset -10px -10px 20px rgba(255, 255, 255, 0.6);
    text-align: center;
    font-family: 'Arial', sans-serif;
    color: #333;
    font-size: 24px;
    overflow-y: hidden;
    display: flex;
    flex-direction: column;
    position: relative;
    }

        .mensaje-container {
            flex-grow: 1;
            width: 100%;
            padding: 10px;
            overflow-y: auto;
            text-align: left;
        }

        .mensaje-usuario, .mensaje-respuesta {
            padding: 10px;
            border-radius: 10px;
            margin-bottom: 10px;
            display: inline-block;
            max-width: 80%;
            box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
        }

        .mensaje-usuario {
            background-color: #FFC0CB;
            text-align: right;
            float: right;
            clear: both;
        }

        .mensaje-respuesta {
            background-color: #FF69B4;
            text-align: left;
            float: left;
            clear: both;
        }

        .input-container {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-top: 10px;
            position: relative;
        }

        .input-container input[type="text"] {
            width: 80%;
            padding: 10px;
            font-size: 18px;
            border: 2px solid #FFB6C1;
            border-radius: 10px;
            box-shadow: inset 5px 5px 10px rgba(0, 0, 0, 0.1),
                        inset -5px -5px 10px rgba(255, 255, 255, 0.5);
            outline: none;
        }

        .input-container button {
            padding: 10px 20px;
            margin-left: 10px;
            font-size: 18px;
            background-color: #FF69B4;
            color: white;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.2),
                        -5px -5px 10px rgba(255, 255, 255, 0.5);
            transition: background-color 0.3s;
        }

        .input-container button:hover {
            background-color: #FF1493;
        }
    </style>
</head>
<body>
    <div class="relieve">
        <div class="mensaje-container" id="mensajeContainer">
            <!-- Los mensajes enviados aparecerán aquí -->
        </div>

        <div class="input-container">
            <input type="text" id="mensajeInput" placeholder="Escribe tu mensaje aquí...">
            <button onclick="enviarMensaje()">Enviar</button>
        </div>
    </div>

    <script>
        let estadosUsuarios = {};

        const lista_de_definicion = {
            "alzheimer": "Es un trastorno cerebral que destruye lentamente la memoria y la capacidad de pensar y, con el tiempo, la habilidad de llevar a cabo hasta las tareas más sencillas.\nLas personas con Alzheimer también experimentan cambios en la conducta y la personalidad.",
            "parkinson": "Es una enfermedad neurodegenerativa que afecta principalmente al sistema nervioso central y se caracteriza por la pérdida progresiva de las neuronas que producen dopamina,\nun neurotransmisor crucial para el control de los movimientos.",
            "epilepsia": "Es un trastorno neurológico que provoca convulsiones recurrentes debido a la actividad eléctrica anormal en el cerebro."
        };

        const lista_de_sintomas = {
            "alzheimer": [
                "Pérdida de memoria",
                "Confusión y desorientación",
                "Dificultad para resolver problemas",
                "Cambios de humor y personalidad",
                "Pérdida de habilidades motoras y coordinación"
            ],
            "parkinson": [
                "Temblor en reposo",
                "Rigidez muscular",
                "Lentitud en los movimientos",
                "Inestabilidad postural",
                "Trastornos del sueño",
                "Depresión, ansiedad y apatía"
            ],
            "epilepsia": [
                "Convulsiones recurrentes",
                "Pérdida del conocimiento",
                "Confusión temporal",
                "Espasmos musculares incontrolables",
                "Sensaciones extrañas como hormigueo"
            ]
        };

        function saludarUsuario() {
            agregarMensaje("Hola, soy tu asistente virtual y voy a ayudarte a entender mejor las enfermedades del sistema nervioso.\nIntroduce el nombre de la enfermedad:\nAlzheimer\nParkinson\nEpilepsia", 'respuesta');
        }

        function manejarMensaje(msg) {
            const usuarioId = 'usuarioId';
            const mensaje = msg.trim().toLowerCase();

            if (!estadosUsuarios[usuarioId]) {
                estadosUsuarios[usuarioId] = 'inicio';
            }

            evaluarRespuesta(mensaje, estadosUsuarios[usuarioId], usuarioId);
        }

        function evaluarRespuesta(mensaje, estado, usuarioId) {
    mensaje = mensaje.toLowerCase().trim(); // Convierte el mensaje a minúsculas para asegurar la comparación

    switch (estado) {
        case 'inicio':
            saludarUsuario();
            estadosUsuarios[usuarioId] = 'esperando_enfermedad';
            break;

        case 'esperando_enfermedad':
            if (lista_de_definicion[mensaje]) {
                const definicion = lista_de_definicion[mensaje];
                const sintomas = lista_de_sintomas[mensaje].join(', ');
                agregarMensaje(`**Definición de ${mensaje.charAt(0).toUpperCase() + mensaje.slice(1)}:**\n${definicion}\n\n**Síntomas:**\n${sintomas}\n\n¿Entendiste? Responde "sí" o "no".`, 'respuesta');
                estadosUsuarios[usuarioId] = 'evaluando_entendimiento';
            } else {
                agregarMensaje("Lo siento, no tengo información sobre esa enfermedad. Intenta con otra.", 'respuesta');
            }
            break;

        case 'evaluando_entendimiento':
            if (["si", "sí"].includes(mensaje)) {  // Verifica variaciones de "sí"
                agregarMensaje('¡Genial! ¿Quieres un ejemplo? Responde "sí" o "no".', 'respuesta');
                estadosUsuarios[usuarioId] = 'evaluando_final';
            } else if (mensaje === 'no') {
                agregarMensaje('De acuerdo. Si tienes más preguntas, aquí estoy.', 'respuesta');
                estadosUsuarios[usuarioId] = 'fin';
            } else {
                agregarMensaje('Por favor, responde "sí" o "no".', 'respuesta');
            }
            break;

        case 'evaluando_final':
            agregarMensaje("Gracias por tu tiempo. ¡Espero haberte ayudado!", 'respuesta');
            estadosUsuarios[usuarioId] = 'fin';
            break;
    }
        }

        function enviarMensaje() {
            const input = document.getElementById('mensajeInput');
            const mensaje = input.value.trim();
            if (mensaje) {
                agregarMensaje(mensaje, 'usuario');
                manejarMensaje(mensaje);
                input.value = '';
            }
        }

        function agregarMensaje(texto, tipo) {
            const contenedor = document.getElementById('mensajeContainer');
            const divMensaje = document.createElement('div');
            divMensaje.className = tipo === 'usuario' ? 'mensaje-usuario' : 'mensaje-respuesta';
            divMensaje.innerText = texto;
            contenedor.appendChild(divMensaje);
            contenedor.scrollTop = contenedor.scrollHeight;
        }
    </script>
</body>
</html>
