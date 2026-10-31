# Amar Baper Rasta 🏍️
আমার বাপের রাস্তা · AI Edition
An AI-powered browser bike arcade game set on a chaotic Bangladeshi street. Dodge traffic, cows, rickshaws, and potholes, collect coins and fuel, and take to the air when the road gets too crowded.

Gemma adds a running commentary to the ride: satirical news, crash roasts, tactical tips, and interactive police or crowd encounters where generated dialogue can affect the game.

The game is built in a single HTML file with vanilla JavaScript, CSS, and HTML5 Canvas. No build step or frontend framework is required.
Features
- Arcade riding: Steer through obstacles, fly using fuel, and dive back toward the road.
- Dynamic world: Increasing speed, a day/night cycle, particles, and synthesized sound effects.
- AI co-pilot: Game-state-based headlines, commentary, greetings, and an end-of-run news report.
- Interactive encounters: Police and crowd dialogue with three generated response options and outcomes that can change score, speed, or crashes.
- Local AI: Connect to a locally running Gemma model through Ollama.
- Fallback content: Built-in lines keep the game playable when model requests fail or AI is unavailable.
- Language options: English and Bangla options for generated text, with partial interface localization.
- Speech output: Browser speech synthesis reads dialogue aloud when a suitable voice is available.
- Workflow graph: A built-in Canvas visualization, labeled Graphify in the interface, shows game components and recorded events. Export it as a PNG.
- Touch controls: On-screen steering, flight, and dive buttons for mobile play.
Quick start
1. Download or clone this repository.
2. Open amar-baper-rasta.html in a modern browser.
3. Click START GAME.
You can play without configuring AI. In that case, the game uses its built-in fallback content rather than live model output.

For a consistent local origin, you can also serve the repository with Python:
python -m http.server 8000
Open http://localhost:8000/amar-baper-rasta.html.
Enable local AI with Ollama
Install Ollama, then download the model used by the game's default settings:
ollama pull gemma2:2b
Make sure Ollama is running. If its service is not already active, start it:
ollama serve
In the game's Settings, enter:
Setting	Value
Provider	Ollama
Ollama URL	http://localhost:11434
Model	gemma2:2b


Click Save & Test Connection. A successful request updates the AI status indicator. You can also use the sidebar's AI test button to request a sample response.

Model downloads require internet access. After downloading the model, local inference does not require a cloud API key. Response speed depends on the machine running Ollama.
Google provider in the current source
The settings also expose a Google AI Studio provider and an API-key field. Its request URL is hardcoded to the model identifier gemma-2b. This README does not verify that endpoint's availability; the cloud option may require a supported model identifier and corresponding code changes before use.

Select Ollama explicitly for the documented local setup. The current Auto implementation does not reliably proceed from Ollama to Google after a failed request.
Controls
Action	Keyboard	Touch
Move left	Left arrow / A	Left button
Move right	Right arrow / D	Right button
Fly	Up arrow / W / Space	Fly button
Dive	Down arrow / S	Down button
Pause / resume	Esc	No dedicated button


Scoring and survival
- Score increases as you ride, with twice the normal score gain while flying.
- Each coin adds 25 points.
- Fuel pickups restore 30 fuel, capped at 100.
- Crashes increase the crash counter. The run ends at 8 crashes.
- Encounter outcomes can deduct score, increase speed, or add a crash.
The police and crowd encounters are fictional arcade satire.
How AI affects the game
The browser sends short prompts containing relevant game state to the selected provider. With Ollama, requests go to /api/generate, and the returned text is used in the interface.
AI feature	Input or trigger	Result
Greeting	Start of a run	A short welcome from the co-pilot
News feed	Periodic updates during play	Satirical headlines based on the current run
Commentary	Speed changes and periodic prompts	Hype lines or tips based on game state
Crash roast	Collision	A short reaction to the crash
Negotiation	Some traffic collisions	An opening line, three player choices, and a response with an outcome
End-of-run report	Game over	A headline summarizing the final stats


Negotiation results are mapped to LET_GO, BRIBE, CHASE, or ESCAPE_FAILED. These can release the rider, deduct score, increase speed, or apply a crash penalty. The current parser expects a specific text format; malformed responses may default to release.

Movement, collisions, scoring, and rendering are handled by JavaScript. AI contributes generated narrative and encounter decisions. Fallback text keeps these interactions available when no model response is returned.
Workflow graph
Open Graph to inspect the built-in visualization of the game, player, world, AI, and events such as crashes, pickups, and negotiations.

Choose Download PNG to export amar-baper-rasta-graph.png.

This is a custom visualization implemented inside the HTML file. It does not require a separate Graphify package or service and is not a model execution trace.
Project structure
.
├── amar-baper-rasta.html   # Complete game: interface, styles, game loop, and AI calls
├── SETUP_GUIDE.md          # Original setup notes
├── LICENSE                 # MIT License
└── README.md              # Project overview and usage
Technology
Component	Implementation
Interface	HTML and CSS
Game logic	Vanilla JavaScript
Rendering	HTML5 Canvas 2D
AI requests	Browser Fetch API
Local model runtime	Ollama
Default local model	Gemma 2, gemma2:2b
Voice output	Web Speech API
Sound effects	Web Audio API
Settings persistence	Browser localStorage
Graph export	Canvas PNG download


Troubleshooting and limitations
Issue	What to check
AI is offline	Confirm Ollama is running, the selected model is downloaded, and the URL matches the local service.
Connection refused	Start the Ollama service or correct its host and port in Settings.
Browser blocks the AI request	Check the browser console for origin/CORS errors. Serve the game locally and configure Ollama to allow that specific local origin if needed.
AI replies are slow	Local model latency depends on available memory and compute. The current request code has no explicit timeout.
Bangla speech is unavailable	Speech synthesis depends on installed browser/system voices. Use an available voice or disable speech.
Some labels remain in English	Interface localization is partial; generated text follows the selected prompt language when the model supports it.
Google requests fail	The hardcoded model endpoint may need updating; use the documented Ollama setup.


Settings, including any entered Google API key, are stored in browser localStorage. The application makes provider requests directly from the browser. For a shared deployment using a cloud key, move requests behind a backend and keep the key server-side.
Team
Built by:
- Shoriful Islam
- Jabir Sharif
- Ekramul Islam
- Md Tasnimul Hasan
- Ishtiaq Mahmud
Contributing
Issues and pull requests are welcome. Describe the problem or proposed change clearly, keep the single-file setup easy to run, and check both normal gameplay and behavior when AI is unavailable.

Useful areas for improvement include full Bangla localization, more reliable provider selection, structured negotiation responses, request timeouts, and accessibility.
License and acknowledgments
This project's source code is licensed under the MIT License. See LICENSE for the full terms.

Gemma is an open-weight model subject to its own terms. The game's code license does not replace the model's terms or the terms of any external service.

Thanks to the Gemma and Ollama projects for making local language-model experimentation accessible.
