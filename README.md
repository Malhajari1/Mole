The Mole (Beta 1.1)
A real-time, multiplayer social deduction game where players must identify the mole in their midst. Built with vanilla HTML, CSS, JavaScript, and powered by Google Firebase for its backend.

Table of Contents
How to Play

Core Features

Technical Architecture

Frontend

Backend (Serverless)

Game State Management

Setup & Installation

Firebase Project Setup

API Key Configuration

Deployment

Key Functions Explained

How to Play
Start or Join: One player creates a new game, becoming the "Handler" (Host). Other players can join using the unique Game ID. Players can also use the "Reconnect" feature to rejoin a game if they get disconnected.

Lobby: Players gather in the lobby. The Handler can start the mission once at least 3 players have joined. Any player can leave the lobby by clicking "Back to Main Menu." If the host leaves, the game is deleted.

Round Structure: The game consists of a number of rounds equal to twice the number of players. (e.g., 3 players = 6 rounds).

Word Master Assignment: Each round, one player is secretly assigned the role of Word Master. This role is assigned from a pre-determined, shuffled schedule to ensure every player is the Word Master exactly twice.

Codename Phase: The Word Master is prompted to enter a secret codename. All other players see a waiting screen.

Role Reveal: After the codename is set, one player (who is not the Word Master) is randomly chosen to be The Mole. All players are then shown their roles privately.

The Mole does NOT see the codename.

All other players (Operatives) see the codename.

Interrogation Phase: All players return to a group screen. Now, the discussion begins! The goal of the Operatives is to ask questions and find the one person who doesn't know the secret word—The Mole. The Mole's job is to blend in and pretend they know the word.

Debrief: The Handler (Host) can end the round at any time by clicking "End Round." All players are then taken to a debriefing screen where the identity of The Mole is revealed.

Next Round: The Handler can then start the next round, and a new Word Master is chosen according to the schedule.

Game Over: After all rounds are complete, the game ends. All players will see a "Mission Complete" screen.

Any player can click "Return to Lobby" to leave the game and go back to the main menu.

If the Host clicks "Return to Lobby", the game resets and takes all remaining players back to the lobby to play again.

Core Features
Real-time Multiplayer: Uses Firestore's real-time database to keep all players perfectly in sync.

Persistent User Identity: Uses localStorage to assign a persistent userId to each browser, allowing players to reconnect to games after a disconnect.

Fair Round-Robin System: The Word Master role is not random. The game creates a fixed, shuffled schedule to ensure every player gets to be the Word Master exactly twice per game.

Procedurally Generated Music: Uses Tone.js to create an evolving, atmospheric spy-themed soundtrack with four distinct, upbeat tracks that rotate automatically every 90 seconds.

Technical Architecture
The game is built as a single-page application (SPA) using a serverless architecture.

Frontend
HTML: A single index.html file contains the structure for all game screens. Screens are toggled between display: none and display: flex based on the game's current state.

CSS (Tailwind CSS): Styling is handled primarily through Tailwind CSS, loaded via a CDN for simplicity. Custom CSS is used for unique elements like the "Top Secret" title stamp and button animations.

JavaScript (ESM): All game logic is contained within a single <script type="module">. This includes Firebase initialization, DOM manipulation, game state logic, and the music engine.

Backend (Serverless)
Google Firebase: The game leverages two core Firebase services.

Firestore: Acts as the real-time database. A single game document stores the entire state of a session, including the list of players, the current game status, roles, the secret word, etc. All players' clients subscribe to this document, and any changes are pushed to all clients in real-time.

Firebase Authentication: Used for the anonymous sign-in system to give each player a unique userId without requiring them to create an account.

Game State Management
The game operates as a simple state machine. The entire flow is controlled by the status field in the Firestore game document.

When the status changes (e.g., from lobby to round_start), the onSnapshot listener on each client fires.

The renderGame(gameData) function is called, which reads the new status and calls the appropriate function to show and update the correct screen (e.g., renderLobby(), renderRoundStart()).

Each rendering function is responsible for showing the correct screen and updating its content based on the latest data from Firestore.

Setup & Installation
To run your own instance of this game, you will need to create a free Firebase project.

Firebase Project Setup
Go to the Firebase Console and create a new project.

In your new project, go to the Build > Firestore Database section and create a new database. Start in test mode for now (this allows open read/write access).

Go to the Build > Authentication section, click on the "Sign-in method" tab, and enable the "Anonymous" provider.

Go to your Project Settings (click the gear icon) and under the "General" tab, scroll down to "Your apps".

Click the web icon (</>) to create a new web app. Give it a name and register the app.

Firebase will provide you with a firebaseConfig object. Copy this object.

API Key Configuration
In the code, replace the existing firebaseConfig object with the one you just copied from your project.

Go to the Google Cloud Console API Credentials page for your project.

Click on the name of your API key (e.g., Browser key (auto created by Firebase)).

Under Application restrictions, select Websites.

Under Website restrictions, click "+ Add" and enter the URL where you will host the game (e.g., your-username.github.io/*).

Under API restrictions, ensure that both "Cloud Firestore API" and "Identity Toolkit API" are in the list of allowed APIs.

Click "Save".

Deployment
The easiest way to deploy the game is with GitHub Pages:

Create a new repository on GitHub.

Upload the index.html file to the repository.

In the repository's settings, go to the "Pages" section and enable GitHub Pages for your main branch.

Your game will be live at https://your-username.github.io/your-repository-name/.

Key Functions Explained
joinGame(id, isNew): The central function for connecting a player to a game. It sets up the real-time onSnapshot listener that keeps the client updated.

renderGame(gameData): The main router for the UI. It reads the status from the game data and calls the appropriate function to show and update the correct screen.

startGame(): The host calls this to initialize the game. It calculates the number of rounds and creates the roundOrder array, ensuring a fair rotation of the Word Master role.

startNewRound(): Called by the host to advance the game. It increments the round counter and assigns the next player in the roundOrder as the new Word Master.

setWordAndAssignRoles(): Called by the Word Master. It randomly assigns the Mole role to one of the other players and saves the secret word and roles to the database.

playAgainBtn.addEventListener(...): Handles the logic for the "Return to Lobby" button at the end of the game, allowing players to leave individually or the host to reset the lobby.

setupMusic(): Initializes the Tone.js instruments and sequences for the four rotating background tracks. It also handles the logic for the mute/unmute button.
