[index.html](https://github.com/user-attachments/files/27082247/index.html)
# gewohnheitstracker<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gewohnheitstracker</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }

        :root {
            --primary: #6366f1;
            --primary-dark: #4f46e5;
            --gradient: linear-gradient(135deg, #6366f1, #8b5cf6);
            --bg: #f0f2f8;
            --card: white;
            --text: #1a1a2e;
            --muted: #9ca3af;
            --border: #e5e7eb;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            background: linear-gradient(160deg, #eef0fb 0%, #f3f0fc 45%, #fbf0f8 100%);
            background-attachment: fixed;
            color: var(--text);
            min-height: 100vh;
            padding-bottom: 60px;
        }

        header {
            background: var(--gradient);
            color: white;
            padding: 32px 20px 22px;
            text-align: center;
        }
        header h1 { font-size: 1.5rem; font-weight: 800; letter-spacing: -0.3px; }
        header .date { font-size: 0.88rem; opacity: 0.8; margin-top: 5px; font-weight: 500; }

        /* Tabs */
        .tabs {
            display: flex;
            background: white;
            border-bottom: 1px solid var(--border);
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .tab-btn {
            flex: 1; padding: 14px;
            border: none; background: none;
            font-size: 0.9rem; font-weight: 600;
            color: var(--muted); cursor: pointer;
            border-bottom: 3px solid transparent;
            transition: all 0.2s;
        }
        .tab-btn.active { color: var(--primary); border-bottom-color: var(--primary); }

        .tab-content { display: none; }
        .tab-content.active { display: block; }

        .container { max-width: 480px; margin: 0 auto; padding: 20px; }

        .section-title {
            font-size: 0.7rem; font-weight: 800;
            color: var(--muted); text-transform: uppercase;
            letter-spacing: 1.5px; margin: 30px 0 12px 6px;
        }

        /* Habit Card */
        @keyframes fadeSlideIn {
            from { opacity: 0; transform: translateY(14px); }
            to   { opacity: 1; transform: translateY(0); }
        }
        .habit-card {
            background: rgba(255,255,255,0.85);
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
            border-radius: 20px;
            padding: 20px 22px;
            margin-bottom: 12px;
            box-shadow:
                0 1px 2px rgba(0,0,0,0.03),
                0 4px 12px rgba(99,102,241,0.07),
                0 16px 32px rgba(0,0,0,0.05);
            border: 1px solid rgba(255,255,255,0.7);
            transition: transform 0.15s ease, box-shadow 0.15s ease;
            animation: fadeSlideIn 0.4s cubic-bezier(0.22, 1, 0.36, 1) both;
        }
        .card-main { display: flex; align-items: center; gap: 16px; }
        .habit-icon {
            font-size: 2.1rem;
            width: 58px; height: 58px;
            border-radius: 16px;
            display: flex; align-items: center; justify-content: center;
            flex-shrink: 0;
            background: #f3f4f6;
        }
        .habit-info { flex: 1; min-width: 0; }
        .habit-label { font-weight: 700; font-size: 0.97rem; display: flex; align-items: center; gap: 7px; flex-wrap: wrap; }
        .habit-sub { color: var(--muted); font-size: 0.8rem; margin-top: 3px; }

        /* Streak badge */
        .streak-badge {
            background: linear-gradient(135deg, #f6d365, #fda085);
            color: white; font-size: 0.68rem; font-weight: 700;
            padding: 2px 8px; border-radius: 20px;
            white-space: nowrap;
        }

        /* Toggle */
        .toggle { position: relative; width: 52px; height: 30px; flex-shrink: 0; }
        .toggle input { opacity: 0; width: 0; height: 0; position: absolute; }
        .toggle-slider {
            position: absolute; cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background: #ddd; border-radius: 30px; transition: 0.3s;
        }
        .toggle-slider:before {
            content: ''; position: absolute;
            width: 22px; height: 22px;
            left: 4px; bottom: 4px;
            background: white; border-radius: 50%;
            transition: 0.3s; box-shadow: 0 1px 4px rgba(0,0,0,0.2);
        }
        .toggle input:checked + .toggle-slider { background: var(--gradient); }
        .toggle input:checked + .toggle-slider:before { transform: translateX(22px); }

        /* Number input */
        .number-input { display: flex; align-items: center; gap: 8px; flex-shrink: 0; }
        .number-input button {
            width: 34px; height: 34px; border: none;
            border-radius: 50%; background: var(--primary);
            color: white; font-size: 1.2rem; cursor: pointer;
            display: flex; align-items: center; justify-content: center;
            touch-action: manipulation; flex-shrink: 0;
        }
        .number-input button:active { background: var(--primary-dark); }
        .number-input input {
            width: 56px; text-align: center;
            border: 2px solid var(--border); border-radius: 10px;
            padding: 6px 4px; font-size: 1rem; font-weight: 700; color: var(--text);
        }

        /* Training types */
        .training-extra {
            display: none; margin-top: 12px;
            padding-top: 12px; border-top: 1px solid #f0f0f0;
        }
        .training-extra p { font-size: 0.8rem; color: var(--muted); margin-bottom: 8px; }
        .training-tags { display: flex; gap: 8px; flex-wrap: wrap; }
        .training-tags button {
            padding: 6px 14px; border: 2px solid var(--border);
            border-radius: 20px; background: white;
            cursor: pointer; font-size: 0.85rem; font-weight: 500;
            transition: all 0.2s; touch-action: manipulation;
        }
        .training-tags button.active {
            background: var(--primary); border-color: var(--primary); color: white;
        }

        /* Delete */
        .habit-delete {
            background: none; border: none;
            color: #ccc; font-size: 1rem;
            cursor: pointer; padding: 4px; flex-shrink: 0;
            touch-action: manipulation;
        }
        .habit-delete:active { color: #e74c3c; }

        /* Add habit */
        .add-habit-btn {
            width: 100%; padding: 14px;
            background: white; border: 2px dashed var(--border);
            border-radius: 16px; color: var(--muted);
            font-size: 0.95rem; font-weight: 600;
            cursor: pointer; margin-top: 8px;
            touch-action: manipulation;
        }
        .add-habit-btn:active { background: #f8f8f8; }

        /* Save button */
        .save-btn {
            width: 100%; padding: 17px;
            background: var(--gradient); color: white; border: none;
            border-radius: 16px; font-size: 1.05rem; font-weight: 700;
            cursor: pointer; margin-top: 16px;
            box-shadow: 0 4px 15px rgba(102,126,234,0.35);
            touch-action: manipulation;
        }
        .save-btn:active { opacity: 0.9; transform: scale(0.99); }
        .saved-hint {
            text-align: center; color: var(--primary);
            font-size: 0.82rem; margin-top: 10px; min-height: 18px;
        }

        /* Stats */
        .stat-card {
            background: white; border-radius: 16px;
            padding: 16px 20px; margin-bottom: 10px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.07);
        }
        .stat-header { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; }
        .stat-header .s-icon { font-size: 1.4rem; }
        .stat-header .s-label { font-weight: 600; font-size: 0.95rem; flex: 1; }
        .stat-header .s-value { font-size: 1rem; font-weight: 700; color: var(--primary); }

        .week-dots { display: flex; gap: 4px; }
        .day-dot-wrap { flex: 1; text-align: center; }
        .day-label { font-size: 0.62rem; color: var(--muted); margin-bottom: 4px; }
        .day-dot {
            width: 28px; height: 28px; border-radius: 50%;
            margin: 0 auto; background: #f0f0f0;
            display: flex; align-items: center; justify-content: center;
            font-size: 0.72rem; color: white;
        }
        .day-dot.done { background: var(--primary); }

        .week-bars { display: flex; align-items: flex-end; gap: 4px; margin-top: 8px; }
        .bar-wrap { flex: 1; display: flex; flex-direction: column; align-items: center; gap: 3px; }
        .bar-bg {
            width: 100%; background: #f0f0f0; border-radius: 4px;
            height: 44px; display: flex; align-items: flex-end; overflow: hidden;
        }
        .bar-fill {
            width: 100%; background: var(--primary);
            border-radius: 4px 4px 0 0; min-height: 0;
        }
        .bar-label { font-size: 0.62rem; color: var(--muted); }

        .stat-meta { display: flex; gap: 8px; margin-top: 12px; }
        .stat-meta-item { text-align: center; flex: 1; background: #f8f8f8; border-radius: 10px; padding: 8px 4px; }
        .stat-meta-item .val { font-size: 1rem; font-weight: 700; color: var(--text); }
        .stat-meta-item .lbl { font-size: 0.68rem; color: var(--muted); margin-top: 2px; }

        /* Modal */
        .modal-overlay {
            display: none; position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.45); z-index: 500;
            align-items: flex-end; justify-content: center;
        }
        .modal-overlay.open { display: flex; }
        .modal {
            background: white; border-radius: 24px 24px 0 0;
            padding: 24px 20px 40px; width: 100%; max-width: 480px;
        }
        .modal h2 { font-size: 1.1rem; margin-bottom: 20px; }

        .form-group { margin-bottom: 14px; }
        .form-group label {
            display: block; font-size: 0.78rem; font-weight: 700;
            color: var(--muted); margin-bottom: 6px;
            text-transform: uppercase; letter-spacing: 0.8px;
        }
        .form-group input, .form-group select {
            width: 100%; padding: 12px 14px;
            border: 2px solid var(--border); border-radius: 12px;
            font-size: 0.95rem; color: var(--text); outline: none;
            font-family: inherit;
        }
        .form-group input:focus { border-color: var(--primary); }
        .form-row { display: flex; gap: 12px; }
        .form-row .form-group { flex: 1; }

        .type-btns { display: flex; gap: 8px; }
        .type-btn {
            flex: 1; padding: 12px; border: 2px solid var(--border);
            border-radius: 12px; background: white; font-size: 0.88rem;
            font-weight: 600; cursor: pointer; text-align: center;
            touch-action: manipulation; font-family: inherit;
        }
        .type-btn.active { background: var(--primary); border-color: var(--primary); color: white; }

        .number-options { display: none; }
        .number-options.show { display: block; }

        .modal-actions { display: flex; gap: 10px; margin-top: 20px; }
        .btn-cancel {
            flex: 1; padding: 14px; border: 2px solid var(--border);
            border-radius: 12px; background: white; font-size: 0.95rem;
            font-weight: 600; cursor: pointer; font-family: inherit;
        }
        .btn-add {
            flex: 2; padding: 14px; background: var(--gradient);
            color: white; border: none; border-radius: 12px;
            font-size: 0.95rem; font-weight: 700; cursor: pointer;
            font-family: inherit;
        }

        /* Toast */
        .toast {
            position: fixed; bottom: 30px; left: 50%;
            transform: translateX(-50%) translateY(80px);
            background: #1e1e2e; color: white;
            padding: 12px 24px; border-radius: 14px;
            font-size: 0.9rem; font-weight: 600;
            transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
            z-index: 999; white-space: nowrap;
            box-shadow: 0 8px 24px rgba(0,0,0,0.2);
        }
        .toast.show { transform: translateX(-50%) translateY(0); }

        /* Section icon colors */
        .habit-card[data-section="Bewegung"] .habit-icon    { background: linear-gradient(135deg, #ede9fe, #ddd6fe); }
        .habit-card[data-section="Ernährung"] .habit-icon   { background: linear-gradient(135deg, #dcfce7, #bbf7d0); }
        .habit-card[data-section="Wohlbefinden"] .habit-icon{ background: linear-gradient(135deg, #fce7f3, #fbcfe8); }
        .habit-card[data-section="Eigene"] .habit-icon      { background: linear-gradient(135deg, #fef9c3, #fde68a); }

        /* Check animation */
        @keyframes cardPop {
            0%   { transform: scale(1); }
            40%  { transform: scale(1.04); box-shadow: 0 12px 32px rgba(99,102,241,0.28); }
            70%  { transform: scale(0.987); }
            100% { transform: scale(1); }
        }
        .habit-card.pop { animation: cardPop 0.45s cubic-bezier(0.34, 1.56, 0.64, 1); }

        /* Done card tint */
        .habit-card.is-done {
            background: rgba(236, 253, 245, 0.88);
            border-color: rgba(134, 239, 172, 0.45);
        }
        .habit-card.is-done .habit-label { color: #15803d; }

        /* Streak milestone badges */
        .streak-badge { background: linear-gradient(135deg, #a78bfa, #7c3aed); }
        .streak-badge.m7  { background: linear-gradient(135deg, #fbbf24, #d97706); }
        .streak-badge.m30 { background: linear-gradient(135deg, #f97316, #c2410c); }
        .streak-badge.m100{ background: linear-gradient(135deg, #ef4444, #9f1239); box-shadow: 0 0 10px rgba(239,68,68,0.35); }

        /* Confetti particles */
        .confetti-dot {
            position: fixed; pointer-events: none; z-index: 9999;
            width: 7px; height: 7px;
            animation: confettiBurst 0.65s ease-out forwards;
        }
        @keyframes confettiBurst {
            0%   { opacity: 1; transform: translate(0,0) scale(1) rotate(0deg); }
            100% { opacity: 0; transform: translate(var(--dx),var(--dy)) scale(0.2) rotate(var(--rot)); }
        }

        /* Weekly grid */
        .week-grid-wrap {
            background: rgba(255,255,255,0.85);
            backdrop-filter: blur(8px);
            border-radius: 20px;
            padding: 18px 16px;
            margin-bottom: 14px;
            box-shadow: 0 1px 2px rgba(0,0,0,0.03), 0 4px 12px rgba(99,102,241,0.07);
            border: 1px solid rgba(255,255,255,0.7);
            overflow-x: auto;
            animation: fadeSlideIn 0.4s cubic-bezier(0.22,1,0.36,1) both;
        }
        .wg-table { width: 100%; border-spacing: 3px; border-collapse: separate; min-width: 340px; }
        .wg-table thead th {
            font-size: 0.65rem; font-weight: 800; color: var(--muted);
            text-align: center; padding: 0 0 10px; text-transform: uppercase; letter-spacing: 0.8px;
        }
        .wg-table thead th.today-col { color: var(--primary); }
        .wg-name-cell { display: flex; align-items: center; gap: 8px; padding-right: 10px; white-space: nowrap; }
        .wg-name-cell .ico { font-size: 1.1rem; }
        .wg-name-cell .lbl { font-size: 0.8rem; font-weight: 600; }
        .wg-dot {
            width: 30px; height: 30px; border-radius: 50%;
            margin: 0 auto; background: #efefef;
            display: flex; align-items: center; justify-content: center;
            font-size: 0.7rem; font-weight: 700; color: transparent;
            transition: background 0.25s, transform 0.25s;
        }
        .wg-dot.done { background: var(--gradient); color: white; transform: scale(1.08); }
        .wg-dot.today-ring { box-shadow: 0 0 0 2.5px var(--primary); }
        .wg-dot.future { opacity: 0.2; }

        /* Empty state */
        .empty-state {
            text-align: center; padding: 64px 24px 32px;
            animation: fadeSlideIn 0.4s ease both;
        }
        .empty-state .e-icon { font-size: 4rem; margin-bottom: 16px; }
        .empty-state h2 { font-size: 1.2rem; font-weight: 700; margin-bottom: 8px; color: var(--text); }
        .empty-state p { color: var(--muted); font-size: 0.9rem; line-height: 1.6; }
    </style>
</head>
<body>

<header>
    <h1>Gewohnheitstracker</h1>
    <p class="date" id="datum"></p>
</header>

<div class="tabs">
    <button class="tab-btn active" onclick="switchTab('heute')">📋 Heute</button>
    <button class="tab-btn" onclick="switchTab('woche')">📅 Woche</button>
</div>

<div class="tab-content active" id="tab-heute">
    <div class="container">
        <div id="habits-container"></div>
        <button class="add-habit-btn" onclick="openModal()">+ Gewohnheit hinzufügen</button>
        <button class="save-btn" onclick="speichern()">Speichern ✓</button>
        <p class="saved-hint" id="saved-hint"></p>
    </div>
</div>

<div class="tab-content" id="tab-woche">
    <div class="container">
        <div id="week-container"></div>
    </div>
</div>

<!-- Modal -->
<div class="modal-overlay" id="modal" onclick="closeModalOutside(event)">
    <div class="modal">
        <h2>Gewohnheit hinzufügen</h2>
        <div class="form-row">
            <div class="form-group" style="flex:0 0 72px">
                <label>Icon</label>
                <input type="text" id="new-icon" maxlength="2" placeholder="⭐" style="text-align:center;font-size:1.4rem;padding:8px">
            </div>
            <div class="form-group">
                <label>Name</label>
                <input type="text" id="new-name" placeholder="z.B. Wasser trinken">
            </div>
        </div>
        <div class="form-group">
            <label>Kategorie</label>
            <input type="text" id="new-section" placeholder="z.B. Gesundheit">
        </div>
        <div class="form-group">
            <label>Typ</label>
            <div class="type-btns">
                <button class="type-btn active" onclick="selectType('toggle', this)">✅ Ja / Nein</button>
                <button class="type-btn" onclick="selectType('number', this)">🔢 Zahl</button>
            </div>
        </div>
        <div class="number-options" id="number-options">
            <div class="form-row">
                <div class="form-group">
                    <label>Einheit</label>
                    <input type="text" id="new-unit" placeholder="z.B. Gläser">
                </div>
                <div class="form-group">
                    <label>Schritte</label>
                    <input type="number" id="new-step" value="1" min="0.1" step="0.1">
                </div>
            </div>
        </div>
        <div class="modal-actions">
            <button class="btn-cancel" onclick="closeModal()">Abbrechen</button>
            <button class="btn-add" onclick="addHabit()">Hinzufügen</button>
        </div>
    </div>
</div>

<div class="toast" id="toast"></div>

<script>
const DEFAULT_HABITS = [
    { id: 'training',        label: 'Training',        icon: '🏋️', type: 'toggle', section: 'Bewegung',     extra: 'training' },
    { id: 'schulteruebung',  label: 'Schulterübungen', icon: '💪', type: 'toggle', section: 'Bewegung' },
    { id: 'beckenuebung',    label: 'Beckenübungen',   icon: '🦵', type: 'toggle', section: 'Bewegung' },
    { id: 'protein',         label: 'Protein',         icon: '🥩', type: 'number', unit: 'g',      step: 10,  section: 'Ernährung' },
    { id: 'kaffee',          label: 'Kaffee',          icon: '☕', type: 'number', unit: 'Tassen', step: 1,   section: 'Ernährung' },
    { id: 'bier',            label: 'Bier',            icon: '🍺', type: 'number', unit: 'Gläser', step: 1,   section: 'Ernährung' },
    { id: 'schlaf',          label: 'Schlaf',          icon: '😴', type: 'number', unit: 'Std',    step: 0.5, section: 'Wohlbefinden' },
    { id: 'meditation',      label: 'Meditation',      icon: '🧘', type: 'toggle', section: 'Wohlbefinden' },
];

// Merge: always use current DEFAULT_HABITS + any custom habits the user added
const storedHabits = JSON.parse(localStorage.getItem('habits_config') || '[]');
const customHabits = storedHabits.filter(h => h.id.startsWith('custom_'));
let habits = [...DEFAULT_HABITS, ...customHabits];
let data   = JSON.parse(localStorage.getItem('gewohnheiten') || '{}');
let selectedTraining = '';
let newHabitType = 'toggle';

// ── Date helpers ──────────────────────────────────────────────────────────
function getToday() { return new Date().toISOString().split('T')[0]; }

function getLast7Days() {
    return Array.from({ length: 7 }, (_, i) => {
        const d = new Date();
        d.setDate(d.getDate() - 6 + i);
        return d.toISOString().split('T')[0];
    });
}

function formatDate(d) {
    const mo = ['Januar','Februar','März','April','Mai','Juni','Juli','August','September','Oktober','November','Dezember'];
    const wd = ['Sonntag','Montag','Dienstag','Mittwoch','Donnerstag','Freitag','Samstag'];
    const dt = new Date(d + 'T12:00:00');
    return `${wd[dt.getDay()]}, ${dt.getDate()}. ${mo[dt.getMonth()]} ${dt.getFullYear()}`;
}

function shortDay(dateStr) {
    return ['So','Mo','Di','Mi','Do','Fr','Sa'][new Date(dateStr + 'T12:00:00').getDay()];
}

// ── Streak ────────────────────────────────────────────────────────────────
function calcStreak(habitId) {
    let streak = 0;
    const d = new Date(getToday() + 'T12:00:00');
    if (!data[getToday()] || !data[getToday()][habitId]) {
        d.setDate(d.getDate() - 1);
    }
    while (true) {
        const key = d.toISOString().split('T')[0];
        if (!data[key] || !data[key][habitId]) break;
        streak++;
        d.setDate(d.getDate() - 1);
    }
    return streak;
}

function makeStreakBadge(streak) {
    if (streak === 0) return '';
    const cls = streak >= 100 ? 'm100' : streak >= 30 ? 'm30' : streak >= 7 ? 'm7' : '';
    const label = streak >= 100 ? `🔥 ${streak} Tage 🏆` : streak >= 30 ? `🔥 ${streak} Tage ⭐` : `🔥 ${streak} Tag${streak !== 1 ? 'e' : ''}`;
    return `<span class="streak-badge ${cls}">${label}</span>`;
}

// ── Render today ──────────────────────────────────────────────────────────
function render() {
    const todayData = data[getToday()] || {};
    const sections = [...new Set(habits.map(h => h.section))];
    let html = '';

    if (habits.length === 0) {
        document.getElementById('habits-container').innerHTML = `
        <div class="empty-state">
            <div class="e-icon">🌱</div>
            <h2>Noch keine Gewohnheiten</h2>
            <p>Tippe auf "+ Gewohnheit hinzufügen"<br>und starte deinen ersten Streak!</p>
        </div>`;
        return;
    }

    let cardIndex = 0;
    sections.forEach(section => {
        html += `<p class="section-title">${section}</p>`;
        habits.filter(h => h.section === section).forEach(habit => {
            html += buildCard(habit, todayData, cardIndex++);
        });
    });

    document.getElementById('habits-container').innerHTML = html;

    // Restore training selection
    if (todayData.training) {
        const extra = document.getElementById('training-extra');
        if (extra) extra.style.display = 'block';
    }
    if (todayData.training_art) {
        selectedTraining = todayData.training_art;
        document.querySelectorAll('.training-tags button').forEach(b => {
            if (b.dataset.art === todayData.training_art) b.classList.add('active');
        });
    }

    if (todayData.gespeichert_um) {
        document.getElementById('saved-hint').textContent =
            `Zuletzt gespeichert um ${todayData.gespeichert_um} Uhr`;
    }
}

function buildCard(habit, todayData, index = 0) {
    const delay = `animation-delay:${index * 55}ms`;
    const isCustom = !DEFAULT_HABITS.find(h => h.id === habit.id);
    const streak = habit.type === 'toggle' ? calcStreak(habit.id) : 0;
    const badge = habit.type === 'toggle' ? makeStreakBadge(streak) : '';
    const del = isCustom
        ? `<button class="habit-delete" onclick="deleteHabit('${habit.id}')" title="Löschen">✕</button>` : '';

    if (habit.extra === 'training') {
        const chk = todayData.training ? 'checked' : '';
        const done = todayData.training ? 'is-done' : '';
        return `
        <div class="habit-card ${done}" data-section="${habit.section}" style="${delay}">
            <div class="card-main">
                <div class="habit-icon">${habit.icon}</div>
                <div class="habit-info">
                    <div class="habit-label">${habit.label}${badge}</div>
                </div>
                <label class="toggle">
                    <input type="checkbox" id="training" ${chk} onchange="toggleTraining(this); popCard(this)">
                    <span class="toggle-slider"></span>
                </label>
                ${del}
            </div>
            <div class="training-extra" id="training-extra">
                <p>Art des Trainings:</p>
                <div class="training-tags">
                    <button data-art="Kraft" onclick="selectTraining(this)">Kraft</button>
                    <button data-art="Laufen" onclick="selectTraining(this)">Laufen</button>
                    <button data-art="Mobilität" onclick="selectTraining(this)">Mobilität</button>
                    <button data-art="Sonstiges" onclick="selectTraining(this)">Sonstiges</button>
                </div>
            </div>
        </div>`;
    }

    if (habit.type === 'toggle') {
        const chk = todayData[habit.id] ? 'checked' : '';
        const done = todayData[habit.id] ? 'is-done' : '';
        return `
        <div class="habit-card ${done}" data-section="${habit.section}" style="${delay}">
            <div class="card-main">
                <div class="habit-icon">${habit.icon}</div>
                <div class="habit-info">
                    <div class="habit-label">${habit.label}${badge}</div>
                </div>
                <label class="toggle">
                    <input type="checkbox" id="${habit.id}" ${chk} onchange="popCard(this)">
                    <span class="toggle-slider"></span>
                </label>
                ${del}
            </div>
        </div>`;
    }

    if (habit.type === 'number') {
        const val = todayData[habit.id] ?? 0;
        return `
        <div class="habit-card" data-section="${habit.section}" style="${delay}">
            <div class="card-main">
                <div class="habit-icon">${habit.icon}</div>
                <div class="habit-info">
                    <div class="habit-label">${habit.label}</div>
                    <div class="habit-sub">${habit.unit}</div>
                </div>
                <div class="number-input">
                    <button onclick="adjust('${habit.id}', ${-habit.step})">−</button>
                    <input type="number" id="${habit.id}" value="${val}" min="0" step="${habit.step}">
                    <button onclick="adjust('${habit.id}', ${habit.step})">+</button>
                </div>
                ${del}
            </div>
        </div>`;
    }
    return '';
}

// ── Render weekly grid ────────────────────────────────────────────────────
function renderWeekGrid() {
    const today = getToday();
    const days = getLast7Days();

    // Header row
    let headerCols = days.map(d => {
        const isToday = d === today;
        return `<th class="${isToday ? 'today-col' : ''}">${shortDay(d)}<br><span style="font-weight:400;font-size:0.6rem">${new Date(d+'T12:00:00').getDate()}</span></th>`;
    }).join('');

    let rows = habits.map(habit => {
        let cells = days.map(d => {
            const isFuture = d > today;
            const isToday = d === today;
            const val = data[d] ? data[d][habit.id] : undefined;
            const done = habit.type === 'toggle' ? !!val : (val > 0);
            const cls = [
                'wg-dot',
                done && !isFuture ? 'done' : '',
                isToday ? 'today-ring' : '',
                isFuture ? 'future' : ''
            ].filter(Boolean).join(' ');
            return `<td style="text-align:center"><div class="${cls}">${done && !isFuture ? '✓' : ''}</div></td>`;
        }).join('');

        return `<tr>
            <td><div class="wg-name-cell">
                <span class="ico">${habit.icon}</span>
                <span class="lbl">${habit.label}</span>
            </div></td>
            ${cells}
        </tr>`;
    }).join('');

    // Streak summary below grid
    let streaks = habits
        .filter(h => h.type === 'toggle')
        .map(h => {
            const s = calcStreak(h.id);
            return `<div style="display:flex;align-items:center;gap:8px;padding:6px 0;border-bottom:1px solid #f4f4f4">
                <span style="font-size:1.1rem">${h.icon}</span>
                <span style="font-size:0.85rem;font-weight:600;flex:1">${h.label}</span>
                ${makeStreakBadge(s) || '<span style="font-size:0.8rem;color:#ccc">— kein Streak</span>'}
            </div>`;
        }).join('');

    const html = `
        <p class="section-title" style="margin-top:16px">Wochenübersicht</p>
        <div class="week-grid-wrap">
            <table class="wg-table">
                <thead><tr><th style="text-align:left">Gewohnheit</th>${headerCols}</tr></thead>
                <tbody>${rows}</tbody>
            </table>
        </div>
        ${streaks ? `<p class="section-title">Streaks</p><div class="week-grid-wrap" style="padding:8px 16px">${streaks}</div>` : ''}
    `;

    document.getElementById('week-container').innerHTML = html;
}

// ── Controls ──────────────────────────────────────────────────────────────
function popCard(input) {
    const card = input.closest('.habit-card');
    card.classList.toggle('is-done', input.checked);

    if (input.checked) {
        card.classList.remove('pop');
        void card.offsetWidth;
        card.classList.add('pop');
        card.addEventListener('animationend', () => card.classList.remove('pop'), { once: true });
        // Confetti only if not already saved today
        const alreadySaved = data[getToday()] && data[getToday()][input.id];
        if (!alreadySaved) spawnConfetti(input);
    }
}

function spawnConfetti(input) {
    const rect = input.getBoundingClientRect();
    const cx = rect.left + rect.width / 2;
    const cy = rect.top + rect.height / 2;
    const colors = ['#6366f1','#8b5cf6','#ec4899','#f59e0b','#10b981','#3b82f6','#f97316'];
    for (let i = 0; i < 14; i++) {
        const el = document.createElement('div');
        el.className = 'confetti-dot';
        const angle = (i / 14) * 360 + Math.random() * 20;
        const dist = 35 + Math.random() * 45;
        const dx = Math.cos(angle * Math.PI / 180) * dist;
        const dy = Math.sin(angle * Math.PI / 180) * dist - 15;
        el.style.cssText = `left:${cx}px;top:${cy}px;background:${colors[i%colors.length]};`
            + `--dx:${dx}px;--dy:${dy}px;--rot:${Math.random()*360}deg;`
            + `animation-delay:${Math.random()*80}ms;`
            + `border-radius:${Math.random()>.5?'50%':'3px'};`;
        document.body.appendChild(el);
        setTimeout(() => el.remove(), 850);
    }
}

function adjust(id, amount) {
    const input = document.getElementById(id);
    if (!input) return;
    const val = Math.round((parseFloat(input.value) + amount) * 100) / 100;
    input.value = Math.max(0, val);
}

function toggleTraining(cb) {
    const extra = document.getElementById('training-extra');
    if (extra) extra.style.display = cb.checked ? 'block' : 'none';
    if (!cb.checked) {
        selectedTraining = '';
        document.querySelectorAll('.training-tags button').forEach(b => b.classList.remove('active'));
    }
}

function selectTraining(btn) {
    selectedTraining = btn.dataset.art;
    document.querySelectorAll('.training-tags button').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
}

function speichern() {
    const today = getToday();
    const uhr = new Date().toLocaleTimeString('de-AT', { hour: '2-digit', minute: '2-digit' });
    const entry = { gespeichert_um: uhr };

    habits.forEach(habit => {
        const el = document.getElementById(habit.id);
        if (!el) return;
        entry[habit.id] = habit.type === 'toggle' ? el.checked : (parseFloat(el.value) || 0);
    });
    entry.training_art = selectedTraining;

    data[today] = entry;
    localStorage.setItem('gewohnheiten', JSON.stringify(data));

    zeigeToast('Gespeichert! ✓');
    document.getElementById('saved-hint').textContent = `Zuletzt gespeichert um ${uhr} Uhr`;
    render();
}

// ── Tabs ──────────────────────────────────────────────────────────────────
function switchTab(tab) {
    document.querySelectorAll('.tab-btn').forEach((b, i) => {
        b.classList.toggle('active', (tab === 'heute' && i === 0) || (tab === 'woche' && i === 1));
    });
    document.getElementById('tab-heute').classList.toggle('active', tab === 'heute');
    document.getElementById('tab-woche').classList.toggle('active', tab === 'woche');
    if (tab === 'woche') renderWeekGrid();
}

// ── Add / Delete habit ────────────────────────────────────────────────────
function openModal() { document.getElementById('modal').classList.add('open'); }
function closeModal() { document.getElementById('modal').classList.remove('open'); }
function closeModalOutside(e) { if (e.target.id === 'modal') closeModal(); }

function selectType(type, btn) {
    newHabitType = type;
    document.querySelectorAll('.type-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById('number-options').classList.toggle('show', type === 'number');
}

function addHabit() {
    const name = document.getElementById('new-name').value.trim();
    const icon = document.getElementById('new-icon').value.trim() || '⭐';
    const section = document.getElementById('new-section').value.trim() || 'Eigene';
    if (!name) { zeigeToast('Bitte einen Namen eingeben!'); return; }

    const habit = { id: 'custom_' + Date.now(), label: name, icon, type: newHabitType, section };
    if (newHabitType === 'number') {
        habit.unit = document.getElementById('new-unit').value.trim() || '';
        habit.step = parseFloat(document.getElementById('new-step').value) || 1;
    }

    habits.push(habit);
    localStorage.setItem('habits_config', JSON.stringify(habits));
    closeModal();
    ['new-name','new-icon','new-section','new-unit'].forEach(id => {
        document.getElementById(id).value = '';
    });
    document.getElementById('new-step').value = '1';
    render();
    zeigeToast(`"${name}" hinzugefügt!`);
}

function deleteHabit(id) {
    habits = habits.filter(h => h.id !== id);
    localStorage.setItem('habits_config', JSON.stringify(habits));
    render();
}

// ── Toast ─────────────────────────────────────────────────────────────────
function zeigeToast(text) {
    const t = document.getElementById('toast');
    t.textContent = text;
    t.classList.add('show');
    setTimeout(() => t.classList.remove('show'), 2500);
}

// ── Init ──────────────────────────────────────────────────────────────────
document.getElementById('datum').textContent = formatDate(getToday());
render();
</script>
</body>
</html>
