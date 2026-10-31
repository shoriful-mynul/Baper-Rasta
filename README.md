# Amar Baper Rasta

### আমার বাপের রাস্তা · AI Edition

Built by **Team Crack Platoon** for Hacktoberfest Hack Day.

An AI-powered browser arcade game set on a chaotic Bangladeshi street. Dodge traffic, cows, rickshaws, and potholes, collect coins and fuel, and take to the air when the road gets too crowded.

Gemma adds a running commentary to the ride: satirical news, crash roasts, tactical tips, and interactive police or crowd encounters where generated dialogue can affect the game.

The game interface, styles, and gameplay logic live in `index.html`, using vanilla JavaScript, CSS, and HTML5 Canvas. No build step or frontend framework is required. Multiplayer and QR invites use external browser libraries.

## Features

- **Arcade riding:** Steer through obstacles, fly using fuel, and dive back toward the road.
- **Dynamic world:** Increasing speed, a day/night cycle, particles, and synthesized sound effects.
- **AI co-pilot:** Game-state-based headlines, commentary, greetings, and an end-of-run news report.
- **Interactive encounters:** Police and crowd dialogue with three generated response options and outcomes that can change score, speed, or crashes.
- **Gemma Rival:** Race a computer-controlled opponent whose strategy is periodically selected by Gemma, with rule-based reflexes and fallback decisions.
- **Multiplayer rooms:** Host a room for 2–6 players, share a six-character room code or invite link, and race on a shared seeded road with remote riders shown as ghost bikes.
- **QR invites:** Generate an optional QR code using the public game URL.
- **Local AI:** Connect to a locally running Gemma model through Ollama.
- **Fallback content:** Built-in lines keep the game playable when model requests fail or AI is unavailable.
- **Language options:** English and Bangla options for generated text, with partial interface localization.
- **Speech output:** Browser speech synthesis reads dialogue aloud when a suitable voice is available.
- **Workflow graph:** A built-in Canvas visualization, labeled Graphify in the interface, shows game components and recorded events. Export it as a PNG.
- **Touch controls:** On-screen steering, flight, and dive buttons for mobile play.

## Quick start

1. Download or clone this repository.
2. Open `index.html` in a modern browser.
3. Enter your rider name and click **START GAME** to race Gemma Rival.

You can play without configuring AI. In that case, the game uses its built-in fallback content rather than live model output.

For a consistent local origin, you can also serve the repository with Python:

```bash
python -m http.server 8000
```

Open [http://localhost:8000/](http://localhost:8000/).

## Enable local AI with Ollama

Install [Ollama](https://ollama.com/download), then download the model used by the game's default settings:

```bash
ollama pull gemma2:2b
```

Make sure Ollama is running. If its service is not already active, start it:

```bash
ollama serve
```

In the game's **Settings**, enter:

| Setting | Value |
| --- | --- |
| Provider | Ollama |
| Ollama URL | `http://localhost:11434` |
| Model | `gemma2:2b` |

Click **Save & Test Connection**. A successful request updates the AI status indicator. You can also use the sidebar's AI test button to request a sample response.

Model downloads require internet access. After downloading the model, local inference does not require a cloud API key. Response speed depends on the machine running Ollama. At startup, the game checks installed Ollama models and may select another installed Gemma model if the configured one is missing.

### Google AI Studio option

In **AI Settings**, select **Google AI Studio**, enter your API key, and choose a model from **Gemini API Model**. The current interface offers `gemma-3-4b-it`, `gemma-3-12b-it`, `gemma-3-1b-it`, and `gemini-2.5-flash`. The request URL uses the selected identifier.

These are options present in the source; model access, quotas, and availability depend on the provider and your account. Use **Save & Test Connection** to check your configuration.

For an open-weight AI demonstration, select a Gemma model. The Gemini option is a separate provider-hosted model and should not be presented as open-weight Gemma.

Select **Ollama** or **Google AI Studio** explicitly. The current **Auto** implementation does not reliably proceed from Ollama to Google after a failed request.

## Game modes

### Solo: race Gemma Rival

**START GAME** launches a run against Gemma Rival. Roughly every eight seconds of active gameplay, the rival requests a strategy based on fuel, crashes, and its position relative to the player:

| Strategy | Effect |
| --- | --- |
| `AGGRESSIVE` | Faster riding with riskier obstacle handling |
| `BALANCED` | Moderate speed and obstacle handling |
| `CAUTIOUS` | Slower riding with more reliable obstacle handling |
| `FUEL_SAVER` | Lower fuel use for obstacle jumps |

JavaScript handles the rival's frame-by-frame movement and jumps; model output selects the strategy and supplies a short taunt. If no valid strategy is returned, rules select one from the race state. At the end of the player's run, the solo result compares the riders' relative positions.

### Multiplayer: race your team

1. Each player opens the game on their device and enters a rider name.
2. The host clicks **Create Room** and shares the six-character room code.
3. Teammates click **Join Room**, enter the code and their name, and join.
4. Once at least two players are present, the host clicks **START RACE**.
5. A countdown starts the race. Remote riders appear as colored ghost bikes; the results board ranks submitted final scores.

Rooms support **up to six players including the host**. Multiplayer uses PeerJS/WebRTC with host-relayed state updates and a shared road seed. Gemma Rival is enabled in solo mode only.

The host can also share an invite link or expand **Show QR code**. QR links use a publicly reachable game URL with a `?join=CODE` parameter. When playing locally, enter the public game URL manually to generate a usable QR invite; teammates can otherwise open their own copy and join by room code.

**Connection requirements:** Multiplayer needs internet access for PeerJS signaling and may use the configured STUN/TURN services. Keep the host connected. Network restrictions or unavailable external services can prevent a room connection.

### Browser library dependencies

The page first tries `lib/peerjs.min.js` and `lib/qrcode.js`, then falls back to CDN copies of **PeerJS 1.5.4** and **qrcode-generator 1.4.4**. The current repository does not include those local `lib/` files, so these features depend on CDN access. The solo game engine and local Ollama integration do not depend on PeerJS or QR generation.

## Controls

| Action | Keyboard | Touch |
| --- | --- | --- |
| Move left | Left arrow / A | Left button |
| Move right | Right arrow / D | Right button |
| Fly | Up arrow / W / Space | Fly button |
| Dive | Down arrow / S | Down button |
| Pause / resume | Esc | No dedicated button |

### Scoring and survival

- Score increases as you ride, with twice the normal score gain while flying.
- Each coin adds **25 points**.
- Fuel pickups restore **30 fuel**, capped at 100. Fuel also slowly regenerates while grounded.
- Crashes increase the crash counter. The run ends at **8 crashes**.
- Encounter outcomes can deduct score, increase speed, or add a crash.

The police and crowd encounters are fictional arcade satire.

## How AI affects the game

The browser sends short prompts containing relevant game state to the selected provider. With Ollama, requests go to `/api/generate`, and the returned text is used in the interface.

| AI feature | Input or trigger | Result |
| --- | --- | --- |
| Greeting | Start of a run | A short welcome from the co-pilot |
| News feed | Periodic updates during play | Satirical headlines based on the current run |
| Commentary | Speed changes and periodic prompts | Hype lines or tips based on game state |
| Crash roast | Collision | A short reaction to the crash |
| Negotiation | Some traffic collisions | An opening line, three player choices, and a response with an outcome |
| Rival strategy | Periodic solo-race decisions | A strategy that changes rival speed, obstacle skill, and fuel use, plus a taunt |
| End-of-run report | Game over | A headline summarizing the final stats |

Negotiation results are mapped to `LET_GO`, `BRIBE`, `CHASE`, or `ESCAPE_FAILED`. These can release the rider, deduct score, increase speed, or apply a crash penalty. The current parser expects a specific text format; malformed responses may default to release.

Movement, collisions, scoring, and rendering are handled by JavaScript. AI contributes generated narrative, encounter decisions, and solo rival strategy. Fallback text keeps these interactions available when no model response is returned.

## Workflow graph

Open **Graph** to inspect the built-in visualization of the game, player, world, AI, and events such as crashes, pickups, and negotiations. Solo runs add rival and strategy nodes; multiplayer runs add a remote-player node.

Choose **Download PNG** to export `amar-baper-rasta-graph.png`.

This is a custom visualization implemented inside the HTML file. It does not require a separate Graphify package or service and is not a model execution trace.

## Project structure

| File | Purpose |
| --- | --- |
| [index.html](index.html) | Game interface, styles, game loop, AI calls, rival, and multiplayer |
| [Graphify Workflow.jpeg](<Graphify Workflow.jpeg>) | Included workflow image |
| [LICENSE](LICENSE) | MIT License |
| [README.md](README.md) | Project overview and usage |


## Technology

| Component | Implementation |
| --- | --- |
| Interface | HTML and CSS |
| Game logic | Vanilla JavaScript |
| Rendering | HTML5 Canvas 2D |
| AI requests | Browser Fetch API |
| Local model runtime | Ollama |
| Default local model | Gemma 2, `gemma2:2b` |
| Voice output | Web Speech API |
| Sound effects | Web Audio API |
| Settings persistence | Browser `localStorage` |
| Graph export | Canvas PNG download |
| Multiplayer | PeerJS 1.5.4 / WebRTC data connections |
| Invite QR | qrcode-generator 1.4.4 |

## Troubleshooting and limitations

| Issue | What to check |
| --- | --- |
| AI is offline | Confirm Ollama is running, the selected model is downloaded, and the URL matches the local service. |
| Connection refused | Start the Ollama service or correct its host and port in Settings. |
| Browser blocks the AI request | Check the browser console for origin/CORS errors. Serve the game locally and configure Ollama to allow that specific local origin if needed. |
| AI replies are slow | Local model latency depends on available memory and compute. The current request code has no explicit timeout. |
| Bangla speech is unavailable | Speech synthesis depends on installed browser/system voices. Use an available voice or disable speech. |
| Some labels remain in English | Interface localization is partial; generated text follows the selected prompt language when the model supports it. |
| Google requests fail | Check the key, selected model, account access, and provider quota; test another available model or use Ollama. |
| Room creation or joining fails | Check CDN access, the room code, host availability, and network restrictions affecting PeerJS/WebRTC. |
| QR code is missing | Confirm a public game URL is entered and the QR library loaded. Room-code joining does not need a QR code. |

AI settings, rider name, and the optional public game URL are saved locally. Settings, including any entered Google API key, are stored in browser `localStorage`. The application makes provider requests directly from the browser. For a shared deployment using a cloud key, move requests behind a backend and keep the key server-side.

## Team

**Crack Platoon**

Built by:

- **Shoriful Islam**
- **Jabir Sharif**
- **Ekramul Islam**
- **Md Tasnimul Hasan**
- **Ishtiaq Mahmud**

## Contributing

Issues and pull requests are welcome. Describe the problem or proposed change clearly, keep the single-file setup easy to run, and check both normal gameplay and behavior when AI is unavailable.

Useful areas for improvement include full Bangla localization, more reliable provider selection, structured negotiation responses, request timeouts, and accessibility.

## License and acknowledgments

This project's source code is licensed under the **MIT License**. See [LICENSE](LICENSE) for the full terms.

Gemma is an open-weight model subject to its own terms. The game's code license does not replace the model's terms or the terms of any external service.

Thanks to the Gemma and Ollama projects for making local language-model experimentation accessible.
