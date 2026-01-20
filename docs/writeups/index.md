<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Home</title>
    <style>
        .landing-container {
            min-height: 80vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 2rem;
        }

        .content-box {
            text-align: center;
            max-width: 600px;
        }

        .icon-wrapper {
            width: 100px;
            height: 100px;
            margin: 0 auto 2rem;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 10px 40px rgba(102, 126, 234, 0.3);
            animation: float 3s ease-in-out infinite;
        }

        @keyframes float {
            0%, 100% {
                transform: translateY(0);
            }
            50% {
                transform: translateY(-10px);
            }
        }

        .icon-wrapper svg {
            width: 50px;
            height: 50px;
            stroke: white;
            fill: none;
            stroke-width: 2;
        }

        .welcome-text {
            font-size: 2.5rem;
            font-weight: 700;
            margin-bottom: 1rem;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .subtitle {
            font-size: 1.1rem;
            opacity: 0.7;
            margin-bottom: 2rem;
        }

        .explore-hint {
            font-size: 0.9rem;
            opacity: 0.5;
            animation: fadeIn 2s ease-in;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 0.5;
            }
        }

        @media (max-width: 768px) {
            .welcome-text {
                font-size: 2rem;
            }
            
            .icon-wrapper {
                width: 80px;
                height: 80px;
            }
        }
    </style>
</head>
<body>
    <div class="landing-container">
        <div class="content-box">
            <div class="icon-wrapper">
                <svg viewBox="0 0 24 24">
                    <path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/>
                </svg>
            </div>
            
            <h1 class="welcome-text">Welcome</h1>
            <p class="subtitle">Technical writeups and research documentation</p>
            <p class="explore-hint">← Browse writeups from the sidebar</p>
        </div>
    </div>
</body>
</html>