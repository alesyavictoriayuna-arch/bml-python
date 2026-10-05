<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Game Platformer - Praktek Informatika</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #1a1a1a;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            font-family: Arial, sans-serif;
            color: white;
        }

        h1 {
            margin-bottom: 10px;
        }

        canvas {
            border: 4px solid #fff;
            background-color: #5c94fc; /* Warna langit Mario */
            box-shadow: 0 10px 20px rgba(0,0,0,0.5);
        }

        .instructions {
            margin-top: 15px;
            text-align: center;
        }
    </style>
</head>
<body>

    <h1>Game Platformer (Mini Mario)</h1>
    <canvas id="gameCanvas" width="800" height="400"></canvas>
    
    <div class="instructions">
        <p>Gunakan tombol <strong>A / D</strong> atau <strong>Panah Kiri / Kanan</strong> untuk bergerak.</p>
        <p>Gunakan tombol <strong>Spasi</strong> atau <strong>Panah Atas</strong> untuk melompat.</p>
    </div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        // Karakter Utama (Pemain)
        const player = {
            x: 50,
            y: 300,
            width: 30,
            height: 40,
            color: "#e74c3c", // Warna merah
            velocityX: 0,
            velocityY: 0,
            speed: 5,
            jumpStrength: -12,
            isGrounded: false
        };

        // Fisika Game
        const gravity = 0.6;

        // Platform (Tanah dan Rintangan)
        const platforms = [
            { x: 0, y: 360, width: 800, height: 40, color: "#2ecc71" },   // Tanah Utama
            { x: 200, y: 280, width: 120, height: 20, color: "#e67e22" }, // Rintangan 1
            { x: 400, y: 220, width: 120, height: 20, color: "#e67e22" }, // Rintangan 2
            { x: 600, y: 160, width: 120, height: 20, color: "#e67e22" }  // Rintangan 3
        ];

        // Item Tujuan (Koin/Bintang)
        const goal = {
            x: 650,
            y: 120,
            width: 20,
            height: 20,
            color: "#f1c40f"
        };

        // Kontrol Input Keyboard
        const keys = {
            right: false,
            left: false,
            up: false
        };

        document.addEventListener("keydown", (e) => {
            if (e.key === "ArrowRight" || e.key === "d" || e.key === "D") keys.right = true;
            if (e.key === "ArrowLeft" || e.key === "a" || e.key === "A") keys.left = true;
            if ((e.key === "ArrowUp" || e.key === " " || e.key === "w" || e.key === "W") && player.isGrounded) {
                player.velocityY = player.jumpStrength;
                player.isGrounded = false;
            }
        });

        document.addEventListener("keyup", (e) => {
            if (e.key === "ArrowRight" || e.key === "d" || e.key === "D") keys.right = false;
            if (e.key === "ArrowLeft" || e.key === "a" || e.key === "A") keys.left = false;
        });

        // Logika Perbarui Pergerakan & Deteksi Tabrakan
        function update() {
            // Gerakan Horizontal
            if (keys.right) player.velocityX = player.speed;
            else if (keys.left) player.velocityX = -player.speed;
            else player.velocityX = 0;

            player.x += player.velocityX;

            // Penerapan Gravitasi
            player.velocityY += gravity;
            player.y += player.velocityY;

            // Batas Layar Kiri/Kanan
            if (player.x < 0) player.x = 0;
            if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;

            // Deteksi Tabrakan dengan Platform
            player.isGrounded = false;
            platforms.forEach(platform => {
                if (
                    player.x < platform.x + platform.width &&
                    player.x + player.width > platform.x &&
                    player.y + player.height <= platform.y + player.velocityY &&
                    player.y + player.height + player.velocityY >= platform.y
                ) {
                    player.velocityY = 0;
                    player.y = platform.y - player.height;
                    player.isGrounded = true;
                }
            });

            // Cek Menang (Menyentuh Goal)
            if (
                player.x < goal.x + goal.width &&
                player.x + player.width > goal.x &&
                player.y < goal.y + goal.height &&
                player.y + player.height > goal.y
            ) {
                alert("Selamat! Kamu berhasil mencapai koin!");
                // Reset Posisi Pemain
                player.x = 50;
                player.y = 300;
            }
        }

        // Fungsi Menggambar Objek di Canvas
        function draw() {
            // Bersihkan Canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Gambar Platform
            platforms.forEach(platform => {
                ctx.fillStyle = platform.color;
                ctx.fillRect(platform.x, platform.y, platform.width, platform.height);
            });

            // Gambar Goal (Koin/Bintang)
            ctx.fillStyle = goal.color;
            ctx.fillRect(goal.x, goal.y, goal.width, goal.height);

            // Gambar Pemain
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        // Loop Utama Game
        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Jalankan Game
        gameLoop();
    </script>
</body>
</html>
