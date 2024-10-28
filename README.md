<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página con Cuadro de Mensajes y Envío</title>
    <style>
        /* Coloca aquí el CSS del fondo, cuadros de mensajes y otros estilos */
    </style>
</head>
<body>
    <div class="relieve">
        <div class="mensaje-container" id="mensajeContainer">
            <!-- Los mensajes enviados aparecerán aquí -->
        </div>
    </div>

    <div class="input-container">
        <input type="text" id="mensajeInput" placeholder="Escribe tu mensaje aquí...">
        <button onclick="enviarMensaje()">Enviar</button>
    </div>

    <script>
        let estadosUsuarios = {};

        const lista_de_definicion = {
            "Alzheimer": "Es un trastorno cerebral que destruye lentamente la memoria y la capacidad de pensar y, con el tiempo, la habilidad de llevar a cabo hasta las tareas más sencillas.\nLas personas con Alzheimer también experimentan cambios en la conducta y la personalidad.",
            "Parkinson": "Es una enfermedad neurodegenerativa que afecta principalmente al sistema nervioso central y se caracteriza por la pérdida progresiva de las neuronas que producen dopamina,\nun neurotransmisor crucial para el control de los movimientos.",
            "Epilepsia": "Es un trastorno neurológico que provoca convulsiones recurrentes debido a la actividad eléctrica anormal en el cerebro."
        };

        const lista_de_sintomas = {
            "Alzheimer": [
                "Pérdida de memoria",
                "Confusión y desorientación",
                "Dificultad para resolver problemas",
                "Cambios de humor y personalidad",
                "Pérdida de habilidades motoras y coordinación"
            ],
            "Parkinson": [
                "Temblor en reposo",
                "Rigidez muscular",
                "Lentitud en los movimientos",
                "Inestabilidad postural",
                "Trastornos del sueño",
                "Depresión, ansiedad y apatía"
            ],
            "Epilepsia": [
                "Convulsiones recurrentes",
                "Pérdida del conocimiento",
                "Confusión temporal",
                "Espasmos musculares incontrolables",
                "Sensaciones extrañas como hormigueo"
            ]
        };

        function saludarUsuario(msg) {
            msg.reply("Hola, soy tu asistente virtual y voy a ayudarte a entender mejor las enfermedades del sistema nervioso.\nIntroduce el nombre de la enfermedad:\nAlzheimer\nParkinson\nEpilepsia");
        }

        function manejarMensaje(msg) {
            const usuarioId = msg.from;
            const mensaje = msg.body.trim().toLowerCase();

            if (!estadosUsuarios[usuarioId]) {
                estadosUsuarios[usuarioId] = 'inicio';
            }

            evaluarRespuesta(msg, estadosUsuarios[usuarioId], usuarioId);
        }

        function evaluarRespuesta(msg, estado, usuarioId) {
            const enfermedadConsultada = msg.body.toLowerCase().trim();

            switch (estado) {
                case 'inicio':
                    saludarUsuario(msg);
                    estadosUsuarios[usuarioId] = 'esperando_enfermedad';
                    break;

                case 'esperando_enfermedad':
                    if (lista_de_definicion[enfermedadConsultada]) {
                        const definicion = lista_de_definicion[enfermedadConsultada];
                        const sintomas = lista_de_sintomas[enfermedadConsultada].join(', ');
                        msg.reply(`**Definición de ${enfermedadConsultada}:**\n${definicion}\n\nSíntomas:\n${sintomas}\n\n¿Entendiste? Responde "sí" o "no".`);
                        estadosUsuarios[usuarioId] = 'evaluando_entendimiento';
                    } else {
                        msg.reply("Lo siento, no tengo información sobre esa enfermedad. Intenta con otra.");
                    }
                    break;

                case 'evaluando_entendimiento':
                    if (msg.body.toLowerCase() === 'sí') {
                        msg.reply('¡Genial! ¿Quieres un ejemplo? Responde "sí" o "no".');
                        estadosUsuarios[usuarioId] = 'preguntando_ejemplo';
                    } else if (msg.body.toLowerCase() === 'no') {
                        msg.reply('Entiendo. Aquí tienes un enlace para más información: [enlace]');
                        estadosUsuarios[usuarioId] = 'fin';
                    } else {
                        msg.reply('Responde "sí" o "no".');
                    }
                    break;

                case 'preguntando_ejemplo':
                    if (msg.body.toLowerCase() === 'sí') {
                        msg.reply('Ejemplo: Marta, de 75 años, tiene Alzheimer. A menudo se pierde y no reconoce a su familia.');
                        estadosUsuarios[usuarioId] = 'evaluando_nuevo_ejemplo';
                    } else {
                        msg.reply('Si necesitas más información, no dudes en preguntar.');
                        estadosUsuarios[usuarioId] = 'fin';
                    }
                    break;

                case 'fin':
                    msg.reply('Gracias por usar el asistente. Si necesitas más ayuda, no dudes en preguntar.');
                    delete estadosUsuarios[usuarioId];
                    break;

                default:
                    msg.reply('No entiendo tu respuesta, por favor intenta nuevamente.');
                    break;
            }
        }

        function enviarMensaje() {
            const input = document.getElementById('mensajeInput');
            const mensaje = input.value.trim();
            if (mensaje) {
                agregarMensaje(mensaje, 'usuario');
                manejarMensaje({ body: mensaje, from: 'usuarioId', reply: (respuesta) => agregarMensaje(respuesta, 'respuesta') });
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
