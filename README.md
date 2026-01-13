<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Flashcard Luyện Nghe (1-100)</title>
    <style>
        :root {
            --primary: #4f46e5;
            --primary-light: #818cf8;
            --success: #10b981;
            --danger: #ef4444;
            --warning: #f59e0b;
            --bg: #f3f4f6;
            --card-bg: #ffffff;
            --text-main: #1f2937;
            --text-sub: #6b7280;
        }

        * { box-sizing: border-box; }

        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background-color: var(--bg);
            color: var(--text-main);
            margin: 0;
            padding: 0;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        /* --- Header & Progress --- */
        header {
            width: 100%;
            background: white;
            padding: 15px 20px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .header-content {
            max-width: 600px;
            margin: 0 auto;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }

        h1 { margin: 0; font-size: 1.2rem; color: var(--primary); }

        .btn-settings {
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            padding: 5px;
            transition: transform 0.2s;
        }
        .btn-settings:hover { transform: rotate(45deg); }

        .progress-container {
            max-width: 600px;
            margin: 0 auto;
            background: #e5e7eb;
            height: 8px;
            border-radius: 4px;
            overflow: hidden;
        }

        .progress-bar {
            height: 100%;
            background: var(--success);
            width: 0%;
            transition: width 0.3s ease;
        }

        .stats-text {
            font-size: 0.85rem;
            color: var(--text-sub);
            text-align: center;
            margin-top: 5px;
        }

        /* --- Main Stage --- */
        .stage {
            flex: 1;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            width: 100%;
            padding: 20px;
            max-width: 500px;
        }

        .card-scene {
            width: 100%;
            height: 320px;
            perspective: 1000px;
            margin-bottom: 25px;
            cursor: pointer;
        }

        .card {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.6s cubic-bezier(0.4, 0.2, 0.2, 1);
            border-radius: 24px;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
        }

        .card.is-flipped { transform: rotateY(180deg); }

        /* Animation classes for swiping */
        .swipe-left { animation: flyLeft 0.5s forwards; }
        .swipe-right { animation: flyRight 0.5s forwards; }

        @keyframes flyLeft {
            to { transform: translateX(-120%) rotate(-10deg); opacity: 0; }
        }
        @keyframes flyRight {
            to { transform: translateX(120%) rotate(10deg); opacity: 0; }
        }

        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            background: white;
            border-radius: 24px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 30px;
            text-align: center;
            border: 1px solid rgba(0,0,0,0.05);
        }

        .card-back { transform: rotateY(180deg); background: #fdfeff; }

        /* Card Content Front (Listening Mode) */
        .listening-icon {
            font-size: 4rem;
            color: white;
            background: var(--primary);
            width: 100px;
            height: 100px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
            box-shadow: 0 4px 15px rgba(79, 70, 229, 0.3);
            transition: transform 0.2s;
            cursor: pointer;
        }
        .listening-icon:active { transform: scale(0.95); }
        
        .front-type {
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--text-sub);
            background: #f3f4f6;
            padding: 5px 15px;
            border-radius: 8px;
            margin-bottom: 20px;
        }

        /* Card Content Back */
        .word { font-size: 2.2rem; font-weight: 700; color: var(--primary); margin-bottom: 10px; line-height: 1.2; word-break: break-word; text-transform: capitalize;}
        .hint { color: var(--text-sub); font-size: 0.9rem; margin-top: auto; }
        
        .ipa { font-size: 1.1rem; color: var(--text-sub); font-family: 'Lucida Sans Unicode', sans-serif; margin-bottom: 5px; }
        .type { display: inline-block; background: #e0e7ff; color: var(--primary); padding: 4px 8px; border-radius: 6px; font-size: 0.8rem; font-weight: 600; margin-bottom: 15px; }
        .meaning { font-size: 1.6rem; font-weight: 600; color: var(--text-main); line-height: 1.3; }

        .audio-btn-small {
            position: absolute;
            top: 20px;
            right: 20px;
            background: var(--bg);
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            color: var(--primary);
            transition: all 0.2s;
        }
        .audio-btn-small:hover { background: #e0e7ff; transform: scale(1.1); }

        /* --- Controls --- */
        .controls {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            width: 100%;
        }

        .btn {
            padding: 16px;
            border: none;
            border-radius: 16px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.1s, box-shadow 0.2s;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            gap: 5px;
        }
        .btn:active { transform: scale(0.96); }

        .btn-unknown { background-color: #fee2e2; color: var(--danger); border: 2px solid transparent; }
        .btn-unknown:hover { border-color: var(--danger); }
        
        .btn-known { background-color: #d1fae5; color: var(--success); border: 2px solid transparent; }
        .btn-known:hover { border-color: var(--success); }

        .shortcut-hint { font-size: 0.7rem; opacity: 0.7; font-weight: normal; }

        /* --- Footer / Lists --- */
        .toolbar {
            margin-top: 30px;
            display: flex;
            gap: 10px;
        }
        .btn-tool {
            padding: 8px 16px;
            background: white;
            border: 1px solid #d1d5db;
            color: var(--text-sub);
            border-radius: 8px;
            font-size: 0.9rem;
            cursor: pointer;
        }
        .btn-tool:hover { background: #f9fafb; color: var(--text-main); }

        /* List Modal Style */
        .list-section {
            width: 100%;
            max-width: 600px;
            margin-top: 20px;
            background: white;
            border-radius: 16px;
            padding: 20px;
            display: none;
            margin-bottom: 40px;
        }
        .list-section.active { display: block; }
        
        .list-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 10px;
            max-height: 400px;
            overflow-y: auto;
        }
        .list-item {
            display: flex;
            justify-content: space-between;
            padding: 10px;
            border-bottom: 1px dashed #eee;
            font-size: 0.95rem;
        }
        .list-item span:first-child { font-weight: 600; color: var(--primary); text-transform: capitalize; }

        /* --- Start Overlay --- */
        .start-overlay {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: var(--bg);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 500;
        }
        .start-btn {
            padding: 20px 40px;
            font-size: 1.5rem;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(79, 70, 229, 0.4);
            transition: transform 0.2s;
        }
        .start-btn:active { transform: scale(0.95); }

        /* --- Settings Modal --- */
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.5);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 200;
        }
        .modal-overlay.active { display: flex; }
        
        .modal-content {
            background: white;
            padding: 25px;
            border-radius: 16px;
            width: 90%;
            max-width: 400px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.2);
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }
        .modal-header h3 { margin: 0; color: var(--primary); }
        .close-btn {
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: #999;
        }

        .setting-group label { display: block; margin-bottom: 8px; font-weight: 600; }
        .voice-select {
            width: 100%;
            padding: 10px;
            border: 1px solid #d1d5db;
            border-radius: 8px;
            font-size: 1rem;
            margin-bottom: 20px;
        }

        /* --- Empty State --- */
        .empty-state {
            text-align: center;
            display: none;
        }
        .empty-state h2 { color: var(--success); }

    </style>
</head>
<body>

    <!-- Start Overlay for Audio Permissions -->
    <div class="start-overlay" id="start-screen">
        <h1 style="margin-bottom: 30px; text-align: center;">Flashcard Luyện Nghe<br>(1 - 100)</h1>
        <button class="start-btn" onclick="startApp()">Bắt đầu học ▶</button>
        <p style="margin-top: 20px; color: #666;">(Nhấn để bật âm thanh)</p>
    </div>

    <header>
        <div class="header-content">
            <h1>Từ Vựng 1-100</h1>
            <div style="display: flex; align-items: center; gap: 10px;">
                <div style="font-size: 0.9rem; font-weight: bold; color: var(--primary);">
                    <span id="queue-count">0</span> cần học
                </div>
                <button class="btn-settings" onclick="openSettings()">⚙️</button>
            </div>
        </div>
        <div class="progress-container">
            <div class="progress-bar" id="progress-bar"></div>
        </div>
        <div class="stats-text">
            Đã thuộc: <span id="known-count">0</span> / <span id="total-count">0</span>
        </div>
    </header>

    <div class="stage">
        <!-- Card -->
        <div class="card-scene" onclick="flipCard()">
            <div class="card" id="flashcard">
                <!-- Front (Listening Only) -->
                <div class="card-face card-front">
                    <!-- Icon Loa Lớn -->
                    <div class="listening-icon" onclick="event.stopPropagation(); speakWord()">
                        🔊
                    </div>
                    <!-- Loại từ -->
                    <div class="front-type" id="front-type">(n)</div>
                    <div class="hint">Nghe và đoán từ<br><small>(Chạm để lật xem đáp án)</small></div>
                </div>

                <!-- Back (Answer) -->
                <div class="card-face card-back">
                    <button class="audio-btn-small" onclick="event.stopPropagation(); speakWord()">
                         <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"></path></svg>
                    </button>
                    <div class="word" id="back-word">...</div>
                    <div class="ipa" id="back-ipa">...</div>
                    <div class="type" id="back-type">...</div>
                    <div class="meaning" id="back-meaning">...</div>
                </div>
            </div>
        </div>

        <!-- Buttons -->
        <div class="controls">
            <button class="btn btn-unknown" onclick="handleResult(false)">
                ❌ Chưa thuộc
                <span class="shortcut-hint">(Mũi tên trái)</span>
            </button>
            <button class="btn btn-known" onclick="handleResult(true)">
                ✅ Đã thuộc
                <span class="shortcut-hint">(Mũi tên phải)</span>
            </button>
        </div>

        <!-- Toolbar -->
        <div class="toolbar">
            <button class="btn-tool" onclick="shuffleCards()">🔀 Đảo từ</button>
            <button class="btn-tool" onclick="toggleList()">📋 Xem danh sách</button>
            <button class="btn-tool" onclick="resetData()">🔄 Học lại từ đầu</button>
        </div>

        <!-- Empty State Msg -->
        <div class="empty-state" id="empty-state">
            <h2>🎉 Chúc mừng!</h2>
            <p>Bạn đã thuộc hết toàn bộ danh sách từ vựng này.</p>
            <button class="btn btn-known" onclick="resetData()">Bắt đầu lại</button>
        </div>
    </div>

    <!-- Hidden List Section -->
    <div class="list-section" id="list-section">
        <h3 style="color: var(--success); margin-top:0;">Danh sách đã thuộc</h3>
        <div class="list-grid" id="known-list-render"></div>
        <h3 style="color: var(--danger); margin-top:20px;">Danh sách cần học</h3>
        <div class="list-grid" id="queue-list-render"></div>
    </div>

    <!-- Settings Modal -->
    <div class="modal-overlay" id="settings-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3>Cài đặt âm thanh</h3>
                <button class="close-btn" onclick="closeSettings()">✕</button>
            </div>
            <div class="setting-group">
                <label for="voice-select">Chọn giọng đọc (Anh-Anh):</label>
                <select id="voice-select" class="voice-select" onchange="saveVoiceSetting()">
                    <option value="">Đang tải giọng...</option>
                </select>
                <small style="color: #666; display: block; margin-top: -10px; margin-bottom: 20px;">
                    Chỉ hiển thị các giọng British English (UK) có trên thiết bị của bạn.
                </small>
            </div>
            <button class="btn btn-known" onclick="closeSettings()" style="width:100%; padding: 10px;">Đóng</button>
        </div>
    </div>

    <script>
        // Data Source 1-100
        const rawData = [
            { id: 1, word: "Safe", type: "(adj)", ipa: "/seɪf/", meaning: "An toàn" },
            { id: 2, word: "View", type: "(v)", ipa: "/vjuː/", meaning: "nhìn, xem" },
            { id: 3, word: "Environment", type: "(n)", ipa: "/ɪnˈvaɪrənmənt/", meaning: "Môi trường" },
            { id: 4, word: "Rain", type: "(n)", ipa: "/reɪn/", meaning: "Mưa" },
            { id: 5, word: "Life", type: "(n)", ipa: "/laɪf/", meaning: "cuộc sống" },
            { id: 6, word: "Adventure", type: "(n)", ipa: "/ədˈventʃər/", meaning: "cuộc phiêu lưu" },
            { id: 7, word: "Earth", type: "(n)", ipa: "/ɜːθ/", meaning: "Trái Đất" },
            { id: 8, word: "Use", type: "(v)", ipa: "/juːz/", meaning: "Sử dụng" },
            { id: 9, word: "Pollution", type: "(n)", ipa: "/pəˈluːʃn/", meaning: "Sự ô nhiễm" },
            { id: 10, word: "Factory", type: "(n)", ipa: "/ˈfæktri/", meaning: "Nhà máy" },
            { id: 11, word: "Bottle", type: "(n)", ipa: "/ˈbɒtl/", meaning: "Chai, lọ" },
            { id: 12, word: "Punish", type: "(v)", ipa: "/ˈpʌnɪʃ/", meaning: "trừng phạt" },
            { id: 13, word: "Recycle", type: "(v)", ipa: "/ˌriːˈsaɪkl/", meaning: "Tái chế" },
            { id: 14, word: "Football", type: "(n)", ipa: "/ˈfʊtbɔːl/", meaning: "bóng đá, bóng bầu dục" },
            { id: 15, word: "Patient", type: "(adj)", ipa: "/ˈpeɪʃnt/", meaning: "kiên nhẫn" },
            { id: 16, word: "Fail", type: "(v)", ipa: "/feɪl/", meaning: "thất bại" },
            { id: 17, word: "Burn", type: "(v)", ipa: "/bɜːn/", meaning: "Đốt, cháy" },
            { id: 18, word: "Can", type: "(n)", ipa: "/kæn/", meaning: "Lon, hộp kim loại" },
            { id: 19, word: "Global", type: "(adj)", ipa: "/ˈɡləʊbl/", meaning: "Toàn cầu" },
            { id: 20, word: "Camera", type: "(n)", ipa: "/ˈkæmərə/", meaning: "máy ảnh" },
            { id: 21, word: "Recyclable", type: "(adj)", ipa: "/ˌriːˈsaɪkləbl/", meaning: "Có thể tái chế" },
            { id: 22, word: "Shout", type: "(v)", ipa: "/ʃaʊt/", meaning: "la hét" },
            { id: 23, word: "Several", type: "(adj)", ipa: "/ˈsevrəl/", meaning: "một vài" },
            { id: 24, word: "Experiment", type: "(n)", ipa: "/ɪkˈsperɪmənt/", meaning: "thí nghiệm" },
            { id: 25, word: "Bike", type: "(n)", ipa: "/baɪk/", meaning: "xe đạp" },
            { id: 26, word: "Turn off", type: "(phr. v)", ipa: "/tɜːn ɒf/", meaning: "Tắt (điện, nước)" },
            { id: 27, word: "Air", type: "(n)", ipa: "/eə(r)/", meaning: "Không khí" },
            { id: 28, word: "Chart", type: "(n)", ipa: "/tʃɑːt/", meaning: "biểu đồ" },
            { id: 29, word: "Waste", type: "(v)", ipa: "/weɪst/", meaning: "Lãng phí" },
            { id: 30, word: "Natural", type: "(adj)", ipa: "/ˈnætʃrəl/", meaning: "Thuộc về tự nhiên" },
            { id: 31, word: "Plant", type: "(n)", ipa: "/plɑːnt/", meaning: "Thực vật, cây cối" },
            { id: 32, word: "Content", type: "(adj)", ipa: "/kənˈtent/", meaning: "hài lòng, mãn nguyện" },
            { id: 33, word: "Golf", type: "(n)", ipa: "/ɡɒlf/", meaning: "môn đánh gôn" },
            { id: 34, word: "Habit", type: "(n)", ipa: "/ˈhæbɪt/", meaning: "thói quen" },
            { id: 35, word: "Behave", type: "(v)", ipa: "/bɪˈheɪv/", meaning: "cư xử, hành xử" },
            { id: 36, word: "Spread", type: "(v)", ipa: "/spred/", meaning: "lan truyền, trải ra" },
            { id: 37, word: "Clean", type: "(adj)", ipa: "/kliːn/", meaning: "Sạch sẽ" },
            { id: 38, word: "Waste", type: "(n)", ipa: "/weɪst/", meaning: "Rác thải, chất thải" },
            { id: 39, word: "Plant", type: "(v)", ipa: "/plɑːnt/", meaning: "Trồng (cây)" },
            { id: 40, word: "Breathe", type: "(v)", ipa: "/briːð/", meaning: "Hít thở" },
            { id: 41, word: "Gas", type: "(n)", ipa: "/ɡæs/", meaning: "Khí đốt" },
            { id: 42, word: "Fresh", type: "(adj)", ipa: "/freʃ/", meaning: "Trong lành, tươi" },
            { id: 43, word: "Harmful", type: "(adj)", ipa: "/ˈhɑːmfl/", meaning: "Có hại" },
            { id: 44, word: "Ever", type: "(adv)", ipa: "/ˈevə(r)/", meaning: "đã từng" },
            { id: 45, word: "Weight", type: "(n)", ipa: "/weɪt/", meaning: "cân nặng" },
            { id: 46, word: "Nature", type: "(n)", ipa: "/ˈneɪtʃə(r)/", meaning: "Thiên nhiên, tự nhiên" },
            { id: 47, word: "Drop", type: "(v)", ipa: "/drɒp/", meaning: "Làm rơi, vứt" },
            { id: 48, word: "Farm", type: "(n)", ipa: "/fɑːm/", meaning: "Nông trại" },
            { id: 49, word: "Shape", type: "(n)", ipa: "/ʃeɪp/", meaning: "hình dạng" },
            { id: 50, word: "Secret", type: "(n)", ipa: "/ˈsiːkrət/", meaning: "bí mật" },
            { id: 51, word: "Quickly", type: "(adv)", ipa: "/ˈkwɪkli/", meaning: "Một cách nhanh chóng" },
            { id: 52, word: "On", type: "(prep)", ipa: "/ɒn/", meaning: "Ở trên" },
            { id: 53, word: "Calm", type: "(adj)", ipa: "/kɑːm/", meaning: "bình tĩnh" },
            { id: 54, word: "During", type: "(prep)", ipa: "/ˈdjʊərɪŋ/", meaning: "trong suốt (thời gian)" },
            { id: 55, word: "Smoke", type: "(n)", ipa: "/sməʊk/", meaning: "Khói" },
            { id: 56, word: "Carefully", type: "(adv)", ipa: "/ˈkeəfəli/", meaning: "Một cách cẩn thận" },
            { id: 57, word: "Loud", type: "(adj)", ipa: "/laʊd/", meaning: "to, ồn ào" },
            { id: 58, word: "Reusable", type: "(adj)", ipa: "/ˌriːˈjuːzəbl/", meaning: "Có thể tái sử dụng" },
            { id: 59, word: "Create", type: "(v)", ipa: "/kriˈeɪt/", meaning: "tạo ra" },
            { id: 60, word: "Litter", type: "(n)", ipa: "/ˈlɪtə(r)/", meaning: "Rác vụn (vứt bừa bãi)" },
            { id: 61, word: "Appropriate", type: "(adj)", ipa: "/əˈprəʊpriət/", meaning: "thích hợp, phù hợp" },
            { id: 62, word: "Never", type: "(adv)", ipa: "/ˈnevə(r)/", meaning: "Không bao giờ" },
            { id: 63, word: "Noise", type: "(n)", ipa: "/nɔɪz/", meaning: "Tiếng ồn" },
            { id: 64, word: "Dirty", type: "(adj)", ipa: "/ˈdɜːti/", meaning: "Bẩn, dơ" },
            { id: 65, word: "Local", type: "(adj)", ipa: "/ˈləʊkl/", meaning: "Địa phương" },
            { id: 66, word: "Agree", type: "(v)", ipa: "/əˈɡriː/", meaning: "đồng ý" },
            { id: 67, word: "Reuse", type: "(v)", ipa: "/ˌriːˈjuːz/", meaning: "Tái sử dụng" },
            { id: 68, word: "Solar", type: "(adj)", ipa: "/ˈsəʊlə(r)/", meaning: "(Thuộc) mặt trời" },
            { id: 69, word: "Adult", type: "(n)", ipa: "/ˈædʌlt/", meaning: "người lớn, người trưởng thành" },
            { id: 70, word: "Bag", type: "(n)", ipa: "/bæɡ/", meaning: "Cái túi" },
            { id: 71, word: "Laugh", type: "(n)", ipa: "/lɑːf/", meaning: "tiếng cười" },
            { id: 72, word: "Concern", type: "(n)", ipa: "/kənˈsɜːn/", meaning: "mối quan tâm, sự lo lắng" },
            { id: 73, word: "Nervous", type: "(adj)", ipa: "/ˈnɜːvəs/", meaning: "lo lắng" },
            { id: 74, word: "Approach", type: "(v)", ipa: "/əˈprəʊtʃ/", meaning: "tiếp cận" },
            { id: 75, word: "Danger", type: "(n)", ipa: "/ˈdeɪndʒə(r)/", meaning: "Mối nguy hiểm" },
            { id: 76, word: "Typical", type: "(adj)", ipa: "/ˈtɪpɪkl/", meaning: "điển hình, tiêu biểu" },
            { id: 77, word: "Travel", type: "(v)", ipa: "/ˈtrævl/", meaning: "du lịch" },
            { id: 78, word: "Alcohol", type: "(n)", ipa: "/ˈælkəhɒl/", meaning: "cồn, rượu" },
            { id: 79, word: "Choose", type: "(v)", ipa: "/tʃuːz/", meaning: "lựa chọn" },
            { id: 80, word: "Paper", type: "(n)", ipa: "/ˈpeɪpə(r)/", meaning: "Giấy" },
            { id: 81, word: "River", type: "(n)", ipa: "/ˈrɪvə(r)/", meaning: "Con sông" },
            { id: 82, word: "Rubbish", type: "(n)", ipa: "/ˈrʌbɪʃ/", meaning: "Rác (tương tự trash)" },
            { id: 83, word: "Walk", type: "(v)", ipa: "/wɔːk/", meaning: "Đi bộ" },
            { id: 84, word: "Weather", type: "(n)", ipa: "/ˈweðə(r)/", meaning: "Thời tiết" },
            { id: 85, word: "Important", type: "(adj)", ipa: "/ɪmˈpɔːtnt/", meaning: "Quan trọng" },
            { id: 86, word: "Boat", type: "(n)", ipa: "/bəʊt/", meaning: "thuyền" },
            { id: 87, word: "Visit", type: "(v)", ipa: "/ˈvɪzɪt/", meaning: "thăm" },
            { id: 88, word: "Cycle", type: "(v)", ipa: "/ˈsaɪkl/", meaning: "Đạp xe" },
            { id: 89, word: "Among", type: "(prep)", ipa: "/əˈmʌŋ/", meaning: "ở giữa" },
            { id: 90, word: "Breakfast", type: "(n)", ipa: "/ˈbrekfəst/", meaning: "bữa sáng" },
            { id: 91, word: "Damage", type: "(n)", ipa: "/ˈdæmɪdʒ/", meaning: "Sự thiệt hại" },
            { id: 92, word: "Water", type: "(n)", ipa: "/ˈwɔːtə(r)/", meaning: "Nước" },
            { id: 93, word: "Duck", type: "(n)", ipa: "/dʌk/", meaning: "con vịt" },
            { id: 94, word: "Shower", type: "(v)", ipa: "/ˈʃaʊə(r)/", meaning: "Tắm (vòi sen)" },
            { id: 95, word: "Always", type: "(adv)", ipa: "/ˈɔːlweɪz/", meaning: "Luôn luôn" },
            { id: 96, word: "Beach", type: "(n)", ipa: "/biːtʃ/", meaning: "Bãi biển" },
            { id: 97, word: "Worse", type: "(adj)", ipa: "/wɜːs/", meaning: "tệ hơn" },
            { id: 98, word: "Grow", type: "(v)", ipa: "/ɡrəʊ/", meaning: "Trồng, phát triển" },
            { id: 99, word: "Avoid", type: "(v)", ipa: "/əˈvɔɪd/", meaning: "tránh, né tránh" },
            { id: 100, word: "Harm", type: "(v)", ipa: "/hɑːm/", meaning: "Gây hại" }
        ];

        // State variables
        let studyQueue = [];
        let knownList = [];
        let currentCard = null;
        let isAnimating = false;
        let voicesList = [];
        let britishVoices = [];
        let selectedVoiceURI = ''; // Store user's choice
        const STORAGE_KEY = 'vocab_app_listening_v6_1_100';

        // --- Initialization ---
        function init() {
            loadProgress();
            
            if (studyQueue.length === 0 && knownList.length === 0) {
                studyQueue = [...rawData];
            } else if (studyQueue.length === 0 && knownList.length < rawData.length) {
                const knownIds = knownList.map(k => k.id);
                studyQueue = rawData.filter(d => !knownIds.includes(d.id));
            }

            // Listen for voices
            if (window.speechSynthesis.onvoiceschanged !== undefined) {
                window.speechSynthesis.onvoiceschanged = () => {
                    loadVoices();
                };
            }
            // Try initial load
            loadVoices();

            renderStats();
            // We do NOT call loadNextCard here. The "Start" button calls it.
        }

        function startApp() {
            document.getElementById('start-screen').style.display = 'none';
            // Also resume audio context if needed, but for speech synthesis, this click is enough
            loadNextCard();
        }

        // --- Voice Logic ---
        function loadVoices() {
            voicesList = window.speechSynthesis.getVoices();
            if(voicesList.length === 0) return;

            // Filter ONLY British English
            // Criteria: lang is en-GB, or name contains UK/GB/United Kingdom
            britishVoices = voicesList.filter(voice => {
                const lang = voice.lang.replace('_', '-');
                const name = voice.name;
                return lang === 'en-GB' || 
                       lang.startsWith('en-GB') ||
                       name.includes('UK English') ||
                       name.includes('Great Britain') ||
                       name.includes('United Kingdom');
            });

            // Populate Dropdown
            const selectEl = document.getElementById('voice-select');
            selectEl.innerHTML = ''; // clear

            if (britishVoices.length === 0) {
                const opt = document.createElement('option');
                opt.text = "Không tìm thấy giọng Anh-Anh trên thiết bị này.";
                selectEl.add(opt);
                return;
            }

            britishVoices.forEach(voice => {
                const option = document.createElement('option');
                option.value = voice.voiceURI;
                let displayName = voice.name;
                if(voice.voiceURI === selectedVoiceURI) {
                    option.selected = true;
                }
                option.text = displayName;
                selectEl.appendChild(option);
            });

            // If no voice selected yet, pick the first one as default
            if (!selectedVoiceURI && britishVoices.length > 0) {
                selectedVoiceURI = britishVoices[0].voiceURI;
                saveVoiceSetting();
            }
        }

        function openSettings() {
            document.getElementById('settings-modal').classList.add('active');
            loadVoices(); // Refresh list to be sure
        }

        function closeSettings() {
            document.getElementById('settings-modal').classList.remove('active');
        }

        function saveVoiceSetting() {
            const selectEl = document.getElementById('voice-select');
            selectedVoiceURI = selectEl.value;
            saveProgress(); 
        }

        // --- Core Logic ---

        function loadNextCard() {
            const cardEl = document.getElementById('flashcard');
            const scene = document.querySelector('.card-scene');
            const controls = document.querySelector('.controls');
            const emptyState = document.getElementById('empty-state');
            
            cardEl.classList.remove('swipe-left', 'swipe-right', 'is-flipped');
            
            if (studyQueue.length === 0) {
                scene.style.display = 'none';
                controls.style.display = 'none';
                emptyState.style.display = 'block';
                return;
            }

            scene.style.display = 'block';
            controls.style.display = 'grid';
            emptyState.style.display = 'none';

            currentCard = studyQueue[0];
            
            document.getElementById('front-type').textContent = currentCard.type;
            document.getElementById('back-word').textContent = currentCard.word;
            document.getElementById('back-ipa').textContent = currentCard.ipa;
            document.getElementById('back-type').textContent = currentCard.type;
            document.getElementById('back-meaning').textContent = currentCard.meaning;
            
            isAnimating = false;

            // Auto play
            setTimeout(() => {
                speakWord();
            }, 600);
        }

        function flipCard() {
            if(isAnimating) return;
            document.getElementById('flashcard').classList.toggle('is-flipped');
        }

        function handleResult(isKnown) {
            if (!currentCard || isAnimating) return;
            isAnimating = true;

            const cardEl = document.getElementById('flashcard');
            
            if (isKnown) {
                cardEl.classList.add('swipe-right');
                studyQueue.shift(); 
                knownList.push(currentCard);
            } else {
                cardEl.classList.add('swipe-left');
                studyQueue.push(studyQueue.shift());
            }

            saveProgress();
            renderStats();

            setTimeout(() => {
                loadNextCard();
            }, 500);
        }

        function shuffleCards() {
            if (studyQueue.length < 2) return;
            for (let i = studyQueue.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [studyQueue[i], studyQueue[j]] = [studyQueue[j], studyQueue[i]];
            }
            const cardEl = document.getElementById('flashcard');
            cardEl.classList.remove('is-flipped');
            loadNextCard();
            saveProgress();
        }

        function resetData() {
            if (confirm('Bạn có chắc chắn muốn xóa lịch sử và học lại từ đầu?')) {
                localStorage.removeItem(STORAGE_KEY);
                studyQueue = [];
                knownList = [];
                location.reload();
            }
        }

        function speakWord() {
            if (!currentCard) return;
            const synth = window.speechSynthesis;
            synth.cancel();

            // Replace / with "or" for better reading if needed, though most single words are fine.
            let textToSpeak = currentCard.word.replace(/\//g, " or ");

            const utterThis = new SpeechSynthesisUtterance(textToSpeak);
            
            // Logic select voice with Fallback
            let voiceToUse = null;

            // 1. Try to find the exact saved voice
            if (selectedVoiceURI) {
                voiceToUse = britishVoices.find(v => v.voiceURI === selectedVoiceURI);
            }

            // 2. Fallback: If saved voice is invalid, try ANY British voice
            if (!voiceToUse && britishVoices.length > 0) {
                voiceToUse = britishVoices[0];
            }

            if (voiceToUse) {
                utterThis.voice = voiceToUse;
            } else {
                // 3. Last resort: Just set Lang code
                utterThis.lang = 'en-GB';
            }

            utterThis.rate = 0.8;
            synth.speak(utterThis);
        }

        // --- Data Persistence ---
        function saveProgress() {
            const data = { 
                studyQueue, 
                knownList,
                selectedVoiceURI 
            };
            localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
        }

        function loadProgress() {
            const data = localStorage.getItem(STORAGE_KEY);
            if (data) {
                const parsed = JSON.parse(data);
                studyQueue = parsed.studyQueue || [];
                knownList = parsed.knownList || [];
                selectedVoiceURI = parsed.selectedVoiceURI || '';
            }
        }

        // --- UI Updates ---
        function renderStats() {
            const total = rawData.length;
            const known = knownList.length;
            const percent = (known / total) * 100;

            document.getElementById('queue-count').textContent = studyQueue.length;
            document.getElementById('known-count').textContent = known;
            document.getElementById('total-count').textContent = total;
            document.getElementById('progress-bar').style.width = `${percent}%`;

            renderListContent();
        }

        function toggleList() {
            const listSection = document.getElementById('list-section');
            listSection.classList.toggle('active');
            if(listSection.classList.contains('active')){
                listSection.scrollIntoView({behavior: "smooth"});
            }
        }

        function renderListContent() {
            const createItem = (item) => `
                <div class="list-item">
                    <span>${item.word} <small style="color:#666; font-weight:normal">${item.ipa}</small></span>
                    <span>${item.meaning}</span>
                </div>
            `;

            document.getElementById('known-list-render').innerHTML = 
                knownList.length ? knownList.map(createItem).join('') : '<p style="text-align:center; color:#999">Chưa có từ nào.</p>';
            
            document.getElementById('queue-list-render').innerHTML = 
                studyQueue.length ? studyQueue.map(createItem).join('') : '<p style="text-align:center; color:#999">Đã học hết.</p>';
        }

        // --- Keyboard Shortcuts ---
        document.addEventListener('keydown', (e) => {
            if (document.getElementById('settings-modal').classList.contains('active')) {
                if(e.key === 'Escape') closeSettings();
                return;
            }
            // If start screen is visible, space starts app
            if (document.getElementById('start-screen').style.display !== 'none') {
                if (e.code === 'Space' || e.key === 'Enter') startApp();
                return;
            }

            if (studyQueue.length === 0) return;

            if (e.code === 'Space') {
                e.preventDefault(); 
                flipCard();
            } else if (e.code === 'ArrowLeft') {
                handleResult(false);
            } else if (e.code === 'ArrowRight') {
                handleResult(true);
            }
        });

        // Start App
        init();

    </script>
</body>
</html>
