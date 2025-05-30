<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Flappy Cuadrado</title>
    <style>
        body { text-align: center; background-color: lightblue; margin: 0; overflow: hidden; }
        canvas { background-color: white; display: block; margin: auto; }
        h1 { font-family: Arial, sans-serif; }
        #botonSaltar {
            display: none;
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            padding: 15px 30px;
            font-size: 20px;
            background-color: green;
            color: white;
            border: none;
            border-radius: 10px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Flappy Cuadrado</h1>
    <canvas id="gameCanvas"></canvas>
    <p>Presiona ESPACIO o toca la pantalla para saltar</p>
    <button id="botonSaltar">Saltar</button>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        // Ajustar tamaño del canvas al tamaño de la pantalla
        canvas.width = window.innerWidth * 0.9;
        canvas.height = window.innerHeight * 0.8;

        let jugador = { x: 50, y: canvas.height / 2, width: 30, height: 30, velocidadY: 0, gravedad: 0.6, salto: -10 };
        let obstaculos = [];
        let espacio = 180;
        let velocidadObstaculos = 3;
        let intervaloObstaculos = 100;
        let frames = 0;
        let puntos = 0;
        let juegoActivo = true;

        function crearObstaculo() {
            let altura = Math.floor(Math.random() * (canvas.height / 2)) + 50;
            obstaculos.push({ x: canvas.width, y: 0, width: 50, height: altura, contado: false });
            obstaculos.push({ x: canvas.width, y: altura + espacio, width: 50, height: canvas.height, contado: false });
        }

        function actualizarJuego() {
            if (!juegoActivo) return;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Movimiento del jugador
            jugador.velocidadY += jugador.gravedad;
            jugador.y += jugador.velocidadY;

            // Dibujar jugador
            ctx.fillStyle = "green";
            ctx.fillRect(jugador.x, jugador.y, jugador.width, jugador.height);

            // Dibujar y mover obstáculos
            ctx.fillStyle = "red";
            obstaculos.forEach((obst, i) => {
                obst.x -= velocidadObstaculos;
                ctx.fillRect(obst.x, obst.y, obst.width, obst.height);

                // Detectar colisión
                if (
                    jugador.x < obst.x + obst.width &&
                    jugador.x + jugador.width > obst.x &&
                    jugador.y < obst.y + obst.height &&
                    jugador.y + jugador.height > obst.y
                ) {
                    gameOver();
                }

                // Sumar puntos cuando pasa la columna
                if (!obst.contado && obst.x + obst.width < jugador.x) {
                    obst.contado = true;
                    if (i % 2 === 0) {
                        puntos++;
                    }
                }
            });

            // Dibujar puntuación
            ctx.fillStyle = "black";
            ctx.font = "20px Arial";
            ctx.fillText("Puntos: " + puntos, 20, 30);

            // Eliminar obstáculos que salieron de la pantalla
            if (obstaculos.length > 0 && obstaculos[0].x < -50) {
                obstaculos.splice(0, 2);
            }

            // Crear nuevos obstáculos cada cierto tiempo
            if (frames % intervaloObstaculos === 0) {
                crearObstaculo();
            }

            // Verificar si el jugador toca el suelo o el techo
            if (jugador.y >= canvas.height || jugador.y <= 0) {
                gameOver();
            }

            // Verificar si el jugador ha ganado
            if (puntos >= 10) {
                ganar();
            }

            frames++;
            requestAnimationFrame(actualizarJuego);
        }

        function gameOver() {
            juegoActivo = false;
            ctx.fillStyle = "black";
            ctx.font = "30px Arial";
            ctx.fillText("¡Perdiste! Recarga la página", canvas.width / 4, canvas.height / 2);
        }

        function ganar() {
            juegoActivo = false;
            ctx.fillStyle = "purple";
            ctx.font = "30px Arial";
            ctx.fillText("Mi primer juego para Cami
