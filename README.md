<style>
    :root {
        --pink: #EC1A6B;
        --pink-dark: #C4105A;
        --blue: #4FC3E8;
        --green: #1FAF6E;
        --orange: #F59E0B;
        --red: #DC2626;
        --bg: #F8FAFC;
        --white: #FFFFFF;
        --text: #1A1A2E;
        --soft: #64748B;
        --border: #EADCE5;
    }

    * {
        box-sizing: border-box;
    }

    body {
        margin: 0;
        padding-bottom: 40px;
        font-family: 'Inter', sans-serif;
        background: linear-gradient(180deg, #EAFBFF, #FFFFFF 25%);
        color: var(--text);
    }

    .app {
        max-width: 520px;
        margin: auto;
        padding: 15px;
    }

    /* HEADER */
    .header {
        background: white;
        border-radius: 22px;
        padding: 20px;
        box-shadow: 0 8px 25px rgba(0,0,0,.05);
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .brand {
        display: flex;
        align-items: center;
        gap: 12px;
    }

    .logo {
        width: 45px;
        height: 45px;
        border-radius: 14px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 22px;
        font-weight: 900;
        color: white;
        background: linear-gradient(135deg, var(--pink), var(--blue));
    }

    h1 {
        font-family: Poppins;
        font-size: 20px;
        margin: 0;
        font-weight: 900;
    }

    /* CARD */
    .card {
        background: white;
        margin-top: 15px;
        padding: 18px;
        border-radius: 22px;
        border: 1px solid var(--border);
        box-shadow: 0 8px 25px rgba(236,26,107,.07);
    }

    .title {
        font-family: Poppins;
        font-size: 15px;
        font-weight: 800;
        margin-bottom: 15px;
        display: flex;
        gap: 8px;
        align-items: center;
        color: var(--text);
    }

    .dot {
        width: 9px;
        height: 9px;
        background: var(--pink);
        border-radius: 50%;
    }

    /* INPUT */
    label {
        font-size: 11px;
        font-weight: 700;
        color: var(--soft);
        text-transform: uppercase;
        display: block;
        margin-bottom: 6px;
    }

    input, select {
        width: 100%;
        padding: 13px;
        border-radius: 14px;
        border: 1.5px solid var(--border);
        font-size: 15px;
        font-weight: 600;
        outline: none;
        transition: all 0.2s;
    }

    input:focus, select:focus {
        border-color: var(--pink);
    }

    /* ITEM BUTTON */
    .items {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
        margin-bottom: 10px;
    }

    .item {
        padding: 9px 14px;
        border-radius: 100px;
        background: white;
        border: 1.5px solid var(--border);
        font-size: 13px;
        font-weight: 700;
        cursor: pointer;
        transition: all 0.2s;
    }

    .item.active {
        background: var(--pink);
        color: white;
        border-color: var(--pink);
    }

    #otherItemInput {
        margin-top: 10px;
        border-color: var(--pink);
        display: none;
    }

    /* ROW SHIFT/DATE */
    .row {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 10px;
        margin-bottom: 20px;
    }

    /* GRAM INPUT & PROGRESS */
    .gram-grid {
        display: grid;
        grid-template-columns: repeat(5, 1fr);
        gap: 8px;
        margin-bottom: 10px;
    }

    .gram {
        position: relative;
    }

    .gram span {
        position: absolute;
        top: 4px;
        left: 7px;
        font-size: 9px;
        color: #CBD5E1;
    }

    .gram input {
        padding-top: 18px;
        text-align: center;
        font-family: Poppins;
        font-weight: 800;
        background: #FFF5FA;
    }

    /* PROGRESS BAR & COUNTER (FITUR BARU) */
    .progress-container {
        width: 100%;
        margin-bottom: 15px;
    }

    .progress-bar-bg {
        width: 100%;
        height: 6px;
        background: #EADCE5;
        border-radius: 100px;
        overflow: hidden;
        position: relative;
    }

    .progress-bar-fill {
        height: 100%;
        background: linear-gradient(90deg, var(--blue), var(--pink));
        width: 0%;
        transition: width 0.3s ease;
    }

    .progress-counter {
        text-align: right;
        font-size: 11px;
        color: var(--soft);
        margin-bottom: 4px;
        font-weight: 600;
    }

    /* RESULT STATS */
    .stats {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 10px;
        margin-top: 15px;
        margin-bottom: 15px;
    }

    .stat {
        padding: 15px;
        border-radius: 18px;
        background: white;
        border: 1px solid var(--border);
        text-align: center;
    }

    .stat-title {
        font-size: 11px;
        font-weight: 700;
        color: var(--soft);
        text-transform: uppercase;
    }

    .stat-value {
        font-family: Poppins;
        font-size: 28px;
        font-weight: 900;
        margin-top: 5px;
    }

    .stat-avg .stat-value { color: var(--pink); }
    .stat-mode .stat-value { color: var(--blue); }

    .stat-unit {
        font-size: 12px;
        color: var(--soft);
        margin-top: -3px;
        display: block;
    }

    /* BUTTONS */
    button {
        width: 100%;
        padding: 15px;
        border: none;
        border-radius: 16px;
        font-family: Poppins;
        font-size: 15px;
        font-weight: 800;
        cursor: pointer;
        transition: all 0.2s;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 8px;
    }

    button:disabled {
        opacity: 0.5;
        cursor: not_allowed;
    }

    .btn-save {
        background: linear-gradient(120deg, var(--pink), var(--pink-dark));
        color: white;
        margin-bottom: 10px;
    }
    
    .btn-save:hover { background: var(--pink-dark); }

    .btn-clean {
        background: none;
        color: var(--soft);
        font-size: 13px;
        font-weight: 600;
        padding: 5px;
        margin-top: 5px;
    }

    .btn-wa {
        background: var(--green);
        color: white;
        margin-top: 20px;
    }
    .btn-wa:hover { background: #1da851; }

    .btn-delete {
        background: none;
        border: 1px solid var(--border);
        color: var(--soft);
        margin-top: 10px;
        font-size: 13px;
    }
    .btn-delete:hover { background: #FFF1F1; border-color: var(--red); color: var(--red); }

    /* RIWAYAT (FITUR LAMA) */
    .history-list {
        margin-top: 10px;
    }

    .history-item {
        padding: 12px;
        border-bottom: 1px solid var(--border);
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .history-item:last-child { border-bottom: none; }

    .history-meta {
        font-size: 12px;
        color: var(--soft);
    }

    .history-avg {
        font-family: Poppins;
        font-weight: 800;
        font-size: 16px;
        color: var(--pink);
    }

    .empty-state {
        text-align: center;
        color: var(--soft);
        font-size: 13px;
        padding: 20px;
    }

</style>
