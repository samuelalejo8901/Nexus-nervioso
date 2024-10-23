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
            background: linear-gradient(135deg, #FFD1DC, #FFB6C1, #FFC0CB, #FF69B4); /* colores del fondo*/
            background-size: 200% 200%;
            animation: gradientShift 5s ease infinite;
            position: relative;
        }

        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .relieve {
            width: 60%;
            height: 300px;
            padding: 20px;
            background-color: #FFD1DC;
            border-radius: 20px;
            box-shadow: 10px 10px 20px rgba(0, 0, 0, 0.2),
                        -10px -10px 20px rgba(255, 255, 255, 0.6),
                        inset 10px 10px 20px rgba(0, 0, 0, 0.2),
                        inset -10px -10px 20px rgba(255, 255, 255, 0.6); /* colores del fondo*/
            text-align: center;
            font-family: 'Arial', sans-serif;
            color: #333;
            font-size: 24px;
            position: relative;
        }

        .mensaje {
            padding: 10px;
            background-color: rgba(255, 255, 255, 0.8); /* Fondo translúcido */
            border-radius: 15px;
            box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.2),
                        -5px -5px 15px rgba(255, 255, 255, 0.5);
            font-size: 18px;
            font-weight: bold;
        }

        .mensaje-container {
    position: absolute;
    max-height: 1000px; /* Altura máxima para el cuadro de mensajes */
    width: 500px;
    overflow-y: auto; /* Desplazamiento vertical si hay más mensajes */
    padding: 10px;
    text-align: left; /* Alineación de texto a la izquierda */
}

        .mensaje-usuario {
            padding: 10px;
            background-color: #FFC0CB; /* Color claro para el usuario */
            border-radius: 10px;
            margin-bottom: 10px;
            display: inline-block;
            max-width: 80%;
            text-align: right;
            box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
            float: right; /* Alineación a la derecha */
            clear: both; /* Evita que los mensajes se apilen mal */
        }

        .mensaje-respuesta {
            padding: 10px;
            background-color: #FF69B4; /* Color más oscuro para la respuesta */
            border-radius: 10px;
            margin-bottom: 10px;
            display: inline-block;
            max-width: 80%;
            text-align: left;
            box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
            float: left; /* Alineación a la izquierda */
            clear: both; /* Evita que los mensajes se apilen mal */
        }

        /* Cuadro de texto y botón de envío */
        .input-container {
            position: absolute;
            bottom: 20px;
            width: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
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
    </div>

    <!-- Contenedor de cuadro de texto y botón de envío -->
    <div class="input-container">
        <input type="text" id="mensajeInput" placeholder="Escribe tu mensaje aquí...">
        <button onclick="enviarMensaje()">Enviar</button>
    </div>

    <script>
        // Objeto para almacenar estados de usuarios
        let estadosUsuarios = {};

        // Listas de información
        const lista_de_definicion = {
            "ansiedad": "Es una respuesta natural del cuerpo ante situaciones de estrés o peligro. \nSe caracteriza por sentimientos de preocupación, nerviosismo o miedo, y \np puede manifestarse tanto a nivel físico como emocional."
        };

        const lista_de_sintomas = {
            "ansiedad": [
                "Palpitaciones o aceleración del corazón",
                "Sudoración excesiva",
                "Tensión muscular",
                "Fatiga o debilidad",
                "Dificultad para respirar o sensación de ahogo",
                "Mareos o aturdimiento",
                "Náuseas o problemas gastrointestinales"
            ]
        };

        // Función para saludar al usuario
        function saludarUsuario(msg) {
            msg.reply('Hola soy tu asistente virtual y voy a ayudarte a entender mejor las enfermedades del sistema nervioso. \nIntroduce el nombre de la enfermedad:');
        }

        // Función para manejar mensajes
        function manejarMensaje(msg) {
            const usuarioId = msg.from;
            const mensaje = msg.body.trim().toLowerCase();

            // Ignorar los mensajes vacíos
            if (mensaje === '') return;

            // Inicializar estado si no existe
            if (!estadosUsuarios[usuarioId]) {
                estadosUsuarios[usuarioId] = 'inicio'; // Inicializa el estado del usuario
            }

            evaluarRespuesta(msg, estadosUsuarios[usuarioId], usuarioId); // Llama a la función y pasa el estado actual
        }

        // Función para evaluar la respuesta del usuario
        function evaluarRespuesta(msg, estado, usuarioId) {
            const enfermedadConsultada = msg.body.toLowerCase().trim(); // Obtener la enfermedad consultada y normalizarla

            switch (estado) {
                case 'inicio':
                    saludarUsuario(msg);
                    estadosUsuarios[usuarioId] = 'esperando_enfermedad'; // Cambia el estado
                    break;

                case 'esperando_enfermedad':
                    if (lista_de_definicion[enfermedadConsultada]) {
                        const definicion = lista_de_definicion[enfermedadConsultada];
                        const sintomas = lista_de_sintomas[enfermedadConsultada].join(', ');

                        msg.reply(`**Definición de ${enfermedadConsultada}:**\n${definicion}\n\nAhora que ya sabemos qué es la ${enfermedadConsultada}, veremos algunos síntomas:\n${sintomas}\n\n¿Entendiste? Responde "sí" o "no".`);
                        estadosUsuarios[usuarioId] = 'evaluando_entendimiento'; // Cambia el estado
                    } else {
                        msg.reply('Lo siento, no tengo información sobre esa enfermedad. Por favor, intenta con otra.');
                    }
                    break;

                case 'evaluando_entendimiento':
                    if (msg.body.toLowerCase() === 'sí') {
                        msg.reply('¡Genial! ¿Quieres un ejemplo de cómo se presenta la ansiedad en la vida real? Responde "sí" o "no".');
                        estadosUsuarios[usuarioId] = 'preguntando_ejemplo'; // Nueva etapa
                    } else if (msg.body.toLowerCase() === 'no') {
                        msg.reply('Entiendo. Aquí tienes un enlace para más información: [enlace]');
                        estadosUsuarios[usuarioId] = 'fin'; // Finaliza la interacción
                    } else {
                        msg.reply('No entiendo tu respuesta. Responde "sí" o "no".');
                    }
                    break;

                case 'preguntando_ejemplo':
                    if (msg.body.toLowerCase() === 'sí') {
                        msg.reply('Aquí tienes un ejemplo: María, de 28 años, siente ansiedad antes de las reuniones en su trabajo.');
                        estadosUsuarios[usuarioId] = 'evaluando_nuevo_ejemplo';
                    } else if (msg.body.toLowerCase() === 'no') {
                        msg.reply('Entiendo, si necesitas más información, no dudes en preguntar. Aquí tienes un video que puede ayudarte: [video]');
                        estadosUsuarios[usuarioId] = 'fin';
                    }
                    break;

                case 'evaluando_nuevo_ejemplo':
                    msg.reply('¿Te gustaría otro ejemplo? Responde "sí" o "no".');
                    estadosUsuarios[usuarioId] = 'decidiendo_ejemplo'; // Nueva etapa
                    break;

                case 'decidiendo_ejemplo':
                    if (msg.body.toLowerCase() === 'sí') {
                        msg.reply('Aquí tienes otro ejemplo: Pedro, de 34 años, siente ansiedad al hablar en público.');
                    } else if (msg.body.toLowerCase() === 'no') {
                        msg.reply('Entiendo, aquí tienes un enlace para más información: [enlace]');
                        estadosUsuarios[usuarioId] = 'fin';
                    }
                    break;

                case 'fin':
                    msg.reply('Gracias por usar el asistente. Si necesitas más ayuda, no dudes en preguntar.');
                    delete estadosUsuarios[usuarioId]; // Borra el estado del usuario
                    break;

                default:
                    msg.reply('No entiendo tu respuesta, por favor intenta nuevamente.');
                    break;
            }
        }

        // Función para enviar mensaje desde el input
        function enviarMensaje() {
            const input = document.getElementById('mensajeInput');
            const mensaje = input.value.trim();

            // Si hay un mensaje, lo enviamos
            if (mensaje) {
                agregarMensaje(mensaje, 'usuario');
                manejarMensaje({ body: mensaje, from: 'usuarioId', reply: (respuesta) => agregarMensaje(respuesta, 'respuesta') });
                input.value = ''; // Limpiar el campo de entrada
            }
        }

        // Función para agregar mensajes al contenedor
        function agregarMensaje(texto, tipo) {
            const contenedor = document.getElementById('mensajeContainer');
            const divMensaje = document.createElement('div');

            divMensaje.className = tipo === 'usuario' ? 'mensaje-usuario' : 'mensaje-respuesta';
            divMensaje.innerText = texto;

            contenedor.appendChild(divMensaje);
            contenedor.scrollTop = contenedor.scrollHeight; // Desplazarse al último mensaje
        }
    </script>
</body>
</html>
