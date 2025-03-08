# Forever-Clicker
this is a game where you can click and upgrade your clicking ability by getting more points to upgrade stuff like points per click and your points per second so you dont have to click so much
# Saving The Game
you can save this game, if you play as a guest this feature is  not available but you can save by signing up for a account 
# Signing Up for a account
to sign up for a account all you do is press the create account box next to the guest profile then enter a username and password to continue. No Email is required for this.
# PLay The Game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Clicker Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: #1e293b;
            font-family: Arial, sans-serif;
            color: white;
            text-align: center;
        }
        .container {
            background: #334155;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        input {
            display: block;
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: none;
            border-radius: 6px;
            font-size: 16px;
        }
        button {
            padding: 10px 20px;
            font-size: 18px;
            background-color: #3b82f6;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: 0.3s;
        }
        button:hover {
            background-color: #2563eb;
        }
        .hidden {
            display: none;
        }
        #gameScreen {
            text-align: center;
        }
        .progress-bar {
            width: 100%;
            height: 20px;
            background-color: #ccc;
            border-radius: 5px;
            margin-top: 10px;
            position: relative;
        }
        .progress-bar-fill {
            height: 100%;
            background-color: #3b82f6;
            border-radius: 5px;
            text-align: center;
            color: white;
        }
    </style>
</head>
<body>

    <!-- Account Creation / Login Screen -->
    <div id="loginScreen" class="container">
        <h1>Login or Create Account</h1>
        <input type="text" id="username" placeholder="Enter your username">
        <input type="password" id="password" placeholder="Enter your password">
        <button onclick="createAccount()">Create Account</button>
        <button onclick="login()">Login</button>
    </div>

    <!-- Game Screen -->
    <div id="gameScreen" class="container hidden">
        <h1>Clicker Game</h1>
        <p>Clicks: <span id="count">0</span></p>
        <button onclick="increment()">Click Me!</button>
        <div class="progress-bar">
            <div id="progressBarFill" class="progress-bar-fill"></div>
        </div>
        <p>Progress: <span id="level">Level 1</span></p>
        <button onclick="logout()">Logout</button>
    </div>

    <script>
        let count = 0;
        let level = 1;
        let clicksForNextLevel = 10;

        // Password scrambling and unscrambling functions
        function scramblePassword(password) {
            let scrambled = '';
            for (let i = 0; i < password.length; i++) {
                scrambled += String.fromCharCode(password.charCodeAt(i) + 3); // Shift characters by 3
            }
            return scrambled;
        }

        function unscramblePassword(scrambledPassword) {
            let unscrambled = '';
            for (let i = 0; i < scrambledPassword.length; i++) {
                unscrambled += String.fromCharCode(scrambledPassword.charCodeAt(i) - 3); // Reverse the shift
            }
            return unscrambled;
        }

        // Function to create an account
        function createAccount() {
            let username = document.getElementById('username').value;
            let password = document.getElementById('password').value;

            if (username && password) {
                let scrambledPassword = scramblePassword(password);
                localStorage.setItem(username, scrambledPassword); // Store the username and scrambled password
                alert("Account Created!");
                document.getElementById('loginScreen').classList.add('hidden');
                document.getElementById('gameScreen').classList.remove('hidden');
                initializeGame(username);
            } else {
                alert("Please enter a username and password");
            }
        }

        // Function to log in to an account
        function login() {
            let username = document.getElementById('username').value;
            let password = document.getElementById('password').value;

            if (username && password) {
                let storedScrambledPassword = localStorage.getItem(username);

                if (storedScrambledPassword) {
                    let unscrambledPassword = unscramblePassword(storedScrambledPassword);
                    if (unscrambledPassword === password) {
                        alert("Login successful!");
                        document.getElementById('loginScreen').classList.add('hidden');
                        document.getElementById('gameScreen').classList.remove('hidden');
                        initializeGame(username);
                    } else {
                        alert("Incorrect password");
                    }
                } else {
                    alert("Username not found");
                }
            } else {
                alert("Please enter your username and password");
            }
        }

        // Initialize the game with saved progress if available
        function initializeGame(username) {
            // Check if there's saved game progress
            let gameProgress = JSON.parse(localStorage.getItem(`${username}_progress`)) || { count: 0, level: 1 };
            count = gameProgress.count;
            level = gameProgress.level;
            clicksForNextLevel = level * 10;

            document.getElementById('count').textContent = count;
            document.getElementById('level').textContent = `Level ${level}`;
            updateProgressBar();
        }

        // Update the progress bar based on current level
        function updateProgressBar() {
            let progressBar = document.getElementById('progressBarFill');
            let progressPercentage = (count / clicksForNextLevel) * 100;
            progressBar.style.width = `${progressPercentage}%`;
        }

        // Increment the click count and check for level up
        function increment() {
            count++;
            if (count >= clicksForNextLevel) {
                level++;
                clicksForNextLevel = level * 10;
            }

            document.getElementById('count').textContent = count;
            document.getElementById('level').textContent = `Level ${level}`;
            updateProgressBar();

            let loggedInUser = localStorage.getItem('loggedInUser');
            if (loggedInUser) {
                let gameProgress = { count, level };
                localStorage.setItem(`${loggedInUser}_progress`, JSON.stringify(gameProgress)); // Save progress
            }
        }

        // Logout function
        function logout() {
            localStorage.removeItem('loggedInUser');
            document.getElementById('gameScreen').classList.add('hidden');
            document.getElementById('loginScreen').classList.remove('hidden');
        }

        // Check if the user is already logged in
        function checkLogin() {
            let loggedInUser = localStorage.getItem('loggedInUser');
            if (loggedInUser) {
                document.getElementById('loginScreen').classList.add('hidden');
                document.getElementById('gameScreen').classList.remove('hidden');
                initializeGame(loggedInUser);
            }
        }

        // On page load, check if user is logged in
        window.onload = checkLogin;
    </script>
</body>
</html>
