<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Animax Browser Shell</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    
    <style>
        /* --- CSS Styling (Embedded) --- */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }

        body {
            display: flex;
            flex-direction: column;
            height: 100vh;
            background-color: #f0f0f0;
        }

        /* --- 1. The Toolbar Layout --- */
        #toolbar {
            display: flex;
            align-items: center;
            padding: 8px 10px;
            background-color: #ffffff;
            border-bottom: 1px solid #e0e0e0;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            position: relative;
            z-index: 10;
        }

        /* --- Navigation Buttons --- */
        .nav-controls button {
            background: none;
            border: none;
            cursor: pointer;
            font-size: 16px;
            color: #555;
            padding: 8px;
            margin-right: 5px;
            transition: color 0.2s;
        }

        .nav-controls button:hover {
            color: #000;
        }
        
        .nav-controls button:disabled {
            color: #ccc;
            cursor: not-allowed;
        }

        /* --- 2. The Address Bar Styling --- */
        .address-bar-container {
            display: flex;
            align-items: center;
            flex-grow: 1;
            background-color: #f7f7f7;
            border: 1px solid #ccc;
            border-radius: 20px;
            padding: 4px 10px;
            margin-left: 15px;
            margin-right: 15px;
        }

        #address-input {
            border: none;
            flex-grow: 1;
            padding: 5px 8px;
            font-size: 14px;
            background: transparent;
            outline: none;
        }

        .lock-icon {
            font-size: 14px;
            color: #6c757d;
            margin-right: 8px;
        }

        #go-btn {
            background: none;
            border: none;
            cursor: pointer;
            font-size: 16px;
            color: #007bff;
            padding: 5px;
        }

        #go-btn:hover {
            color: #0056b3;
        }

        /* --- 3. The Display Window --- */
        #content-display {
            flex-grow: 1;
            width: 100%;
            border: none;
            background-color: white;
        }
    </style>
</head>
<body>

    <div id="toolbar">
        <div class="nav-controls">
            <button id="back-btn"><i class="fas fa-arrow-left"></i></button>
            <button id="forward-btn"><i class="fas fa-arrow-right"></i></button>
        </div>

        <div class="address-bar-container">
            <i class="fas fa-lock lock-icon"></i>
            <input type="url" id="address-input" placeholder="Enter URL (e.g., example.com)">
            <button id="go-btn" title="Go/Reload"><i class="fas fa-redo-alt"></i></button>
        </div>
    </div>

    <iframe id="content-display" src="about:blank" frameborder="0"></iframe>

    <script>
        // --- JavaScript Logic (Embedded) ---
        const iframe = document.getElementById('content-display');
        const addressInput = document.getElementById('address-input');
        const goBtn = document.getElementById('go-btn');
        const backBtn = document.getElementById('back-btn');
        const forwardBtn = document.getElementById('forward-btn');

        let history = []; // Stores the URLs the user has visited
        let historyIndex = -1; // Tracks the current position in the history array

        // --- Core Function: Navigating to a New URL ---
        function navigate(url) {
            if (!url) return;
            
            // 1. Prepend 'https://' if missing (prevents local file loading issues)
            if (!url.startsWith('http://') && !url.startsWith('https://') && url.includes('.')) {
                url = 'https://' + url;
            }
            
            // 2. Update the address bar and load the iframe
            addressInput.value = url;
            iframe.src = url;

            // 3. Update history (clears 'future' if moving from a past point)
            if (historyIndex < history.length - 1) {
                history = history.slice(0, historyIndex + 1);
            }
            history.push(url);
            historyIndex = history.length - 1;
            
            // 4. Update button states
            updateNavButtons();
        }

        // --- Helper: Enabling/Disabling Buttons ---
        function updateNavButtons() {
            // Disable Back if at the start of history or history is empty
            backBtn.disabled = historyIndex <= 0;
            // Disable Forward if at the current tip of history
            forwardBtn.disabled = historyIndex >= history.length - 1;
        }

        // --- Event Listeners ---

        // 1. Go Button Click
        goBtn.addEventListener('click', () => {
            navigate(addressInput.value);
        });

        // 2. Enter Key in Address Bar
        addressInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                navigate(addressInput.value);
            }
        });

        // 3. Back Button Click
        backBtn.addEventListener('click', () => {
            if (historyIndex > 0) {
                historyIndex--;
                const url = history[historyIndex];
                iframe.src = url;
                addressInput.value = url;
                updateNavButtons();
            }
        });

        // 4. Forward Button Click
        forwardBtn.addEventListener('click', () => {
            if (historyIndex < history.length - 1) {
                historyIndex++;
                const url = history[historyIndex];
                iframe.src = url;
                addressInput.value = url;
                updateNavButtons();
            }
        });

        // --- Initialization ---
        updateNavButtons(); // Disable buttons on load
    </script>
</body>
</html>
