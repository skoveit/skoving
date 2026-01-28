<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Notes</title>
    <style>
        :root {
            --primary-gradient: linear-gradient(135deg, #FF6B6B 0%, #556270 100%);
            --card-bg: rgba(255, 255, 255, 0.05);
            --hover-bg: rgba(255, 255, 255, 0.1);
            --text-main: #e0e0e0;
            --text-sub: #a0a0a0;
        }

        .notes-container {
            padding: 2rem 0;
            max-width: 1200px;
            margin: 0 auto;
        }

        .header-section {
            text-align: center;
            margin-bottom: 4rem;
            position: relative;
        }

        .main-title {
            font-size: 3.5rem;
            font-weight: 800;
            background: linear-gradient(to right, #4facfe 0%, #00f2fe 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 1rem;
            letter-spacing: -1px;
        }

        .sub-title {
            font-size: 1.2rem;
            color: var(--text-sub);
            max-width: 600px;
            margin: 0 auto;
            line-height: 1.6;
        }

        .category-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 2rem;
            padding: 1rem;
        }

        .category-card {
            background: var(--card-bg);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 24px;
            padding: 2rem;
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            backdrop-filter: blur(10px);
            position: relative;
            overflow: hidden;
            text-decoration: none;
            display: block;
        }

        .category-card:hover {
            transform: translateY(-10px);
            background: var(--hover-bg);
            border-color: rgba(79, 172, 254, 0.5);
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.4);
        }

        .card-icon {
            width: 60px;
            height: 60px;
            border-radius: 16px;
            background: rgba(255, 255, 255, 0.05);
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 1.5rem;
            font-size: 2rem;
            transition: transform 0.4s ease;
        }

        .category-card:hover .card-icon {
            transform: scale(1.1) rotate(5deg);
        }

        .card-title {
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--text-main);
            margin-bottom: 0.5rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .card-desc {
            font-size: 0.95rem;
            color: var(--text-sub);
            line-height: 1.5;
        }

        .card-arrow {
            position: absolute;
            bottom: 2rem;
            right: 2rem;
            opacity: 0;
            transform: translateX(-20px);
            transition: all 0.4s ease;
            color: #4facfe;
        }

        .category-card:hover .card-arrow {
            opacity: 1;
            transform: translateX(0);
        }

        /* Category Specific Colors */
        .cat-red .card-icon { color: #ff6b6b; background: rgba(255, 107, 107, 0.1); }
        .cat-web .card-icon { color: #4ecdc4; background: rgba(78, 205, 196, 0.1); }
        .cat-win .card-icon { color: #45b7d1; background: rgba(69, 183, 209, 0.1); }
        .cat-ad .card-icon { color: #96ceb4; background: rgba(150, 206, 180, 0.1); }

    </style>
</head>
<body>

    <div class="notes-container">
        
        <header class="header-section">
            <h1 class="main-title">Second Brain</h1>
            <p class="sub-title">A personal knowledge base containing field notes, techniques, and research on offensive security.</p>
        </header>

        <div class="category-grid">
            
            <!-- Active Directory -->
            <a href="active%20directory/Kerbros/" class="category-card cat-ad">
                <div class="card-icon">🏗️</div>
                <h3 class="card-title">Active Directory</h3>
                <p class="card-desc">Kerberos, LAPS, Certificate Services, and forest domination techniques.</p>
                <span class="card-arrow">➜</span>
            </a>

            <!-- Red Team -->
            <a href="red/windows/70%25/Enum/" class="category-card cat-red">
                <div class="card-icon">🎯</div>
                <h3 class="card-title">Red Team</h3>
                <p class="card-desc">Full lifecycle operations: from initial access and enumeration to lateral movement and persistence.</p>
                <span class="card-arrow">➜</span>
            </a>

            <!-- Web Security -->
            <a href="web/CSRF/" class="category-card cat-web">
                <div class="card-icon">🌐</div>
                <h3 class="card-title">Web Security</h3>
                <p class="card-desc">Client-side attacks, logic flaws, request smuggling, and modern protocol exploits.</p>
                <span class="card-arrow">➜</span>
            </a>

            <!-- Windows Internals -->
            <a href="windows%20internals/process/" class="category-card cat-win">
                <div class="card-icon">⚙️</div>
                <h3 class="card-title">Windows Internals</h3>
                <p class="card-desc">Deep dives into PE structure, memory management, DLL injection, and OS security mechanisms.</p>
                <span class="card-arrow">➜</span>
            </a>

        </div>

    </div>

</body>
</html>
