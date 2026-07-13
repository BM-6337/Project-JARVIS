# Project JARVIS

> Your personal desktop AI assistant powered by a double clap.

J.A.R.V.I.S (Just A Rather Very Intelligent System) continuously listens for a **double clap** through your microphone. Once detected, it automatically executes your personalized startup routine—launching Spotify, opening Chrome tabs, greeting you with an AI-generated voice using ElevenLabs, and bringing Cursor into focus.

Designed to make sitting down at your desk feel like activating your own AI assistant.

---

## Features

- Detects a **double clap** with adaptive noise filtering
- Opens your favorite Spotify song or playlist
- Launches Chrome with configurable websites
- Speaks a personalized welcome message using ElevenLabs TTS
- Caches generated speech for instant playback
- Opens or focuses Cursor automatically
- Automatically detects the best available microphone
- Highly configurable through constants and environment variables

---

## Demo Flow

```text
Double Clap
      ↓
Spotify starts playing
      ↓
Chrome opens your favorite websites
      ↓
"Welcome home, sir..."
      ↓
Cursor opens in fullscreen
```

---

# Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/project-jarvis.git
cd project-jarvis
```

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

---

# Environment Variables

The script loads variables from a `.env` file placed beside `jarvis.py`.

## Required

| Variable | Description |
|----------|-------------|
| `ELEVENLABS_API_KEY` | Your ElevenLabs API key |
| `ELEVENLABS_VOICE_ID` | Voice ID used for the welcome message |

Without these variables, the voice greeting is skipped.

---

## Optional

| Variable | Description |
|----------|-------------|
| `ELEVENLABS_MODEL_ID` | TTS model (default: `eleven_multilingual_v2`) |
| `ELEVENLABS_OUTPUT_FORMAT` | Audio format (default: `pcm_24000`) |
| `ELEVENLABS_PCM_SAMPLE_RATE` | Override playback sample rate |
| `JARVIS_WELCOME_CACHE_DIR` | Custom cache location |
| `JARVIS_INPUT_DEVICE` | Force a specific microphone |
| `CLAUDE_CODE_URL` | URL opened in Chrome |
| `TASARADAR_URL` | Custom dashboard URL |
| `CHROME_NEW_WINDOW_WAIT_S` | Wait time for Chrome window detection |
| `CHROME_WINDOW_WIDTH` | Chrome window width |
| `CHROME_WINDOW_HEIGHT` | Chrome window height |

Example:

```env
ELEVENLABS_API_KEY=your_api_key
ELEVENLABS_VOICE_ID=your_voice_id
```

---

# Running

```bash
python jarvis.py
```

Grant microphone permission if prompted.

Stop anytime with:

```text
Ctrl + C
```

---

# Configuration

Most behavior can be customized directly from the constants at the top of `jarvis.py`.

| Constant | Purpose |
|-----------|---------|
| `SONG_URI` | Spotify song or playlist to open |
| `JARVIS_WELCOME_PHRASE` | AI greeting text |
| `SPIKE_RATIO` | Clap detection sensitivity |
| `COOLDOWN_S` | Time between clap detections |
| `MIN_RMS` | Minimum clap volume |
| `BLOCK_MS` | Audio processing interval |
| `SAMPLE_RATE` | Audio sampling rate |
| `OPEN_CLAUDE_CODE_IN_CHROME` | Open Claude automatically |
| `OPEN_BINANCE_BTC_IN_CHROME` | Open Binance automatically |
| `CURSOR_OPEN_FULLSCREEN` | Launch Cursor in fullscreen |

---

# How Clap Detection Works

Jarvis continuously analyzes microphone input using:

- Adaptive noise floor estimation
- RMS audio analysis
- Spike detection
- Double-clap timing validation
- Automatic microphone fallback
- Cooldown protection against repeated triggers

This makes detection reliable even in noisy environments.

---

# Troubleshooting

### Wrong microphone

Jarvis automatically selects the loudest active microphone.

To force one:

```env
JARVIS_INPUT_DEVICE=Microphone Name
```

or

```env
JARVIS_INPUT_DEVICE=3
```

List available devices:

```bash
python -c "import sounddevice as sd; print(sd.query_devices())"
```

---

### Claps aren't detected

- Lower `SPIKE_RATIO`
- Move closer to the microphone
- Increase microphone input volume

---

### Too many false triggers

Increase:

- `SPIKE_RATIO`
- `COOLDOWN_S`

---

### ElevenLabs greeting doesn't play

Ensure the following are correctly set in your `.env`:

- `ELEVENLABS_API_KEY`
- `ELEVENLABS_VOICE_ID`

---

### Audio errors

Try:

- Updating audio drivers
- Changing `SAMPLE_RATE` to `48000`

---

# Tech Stack

- Python
- NumPy
- SoundDevice (PortAudio)
- python-dotenv
- ElevenLabs API
- Windows Automation APIs

---

# Roadmap

- [ ] Wake word support ("Hey Jarvis")
- [ ] OpenAI integration
- [ ] Local LLM support (Ollama)
- [ ] Plugin system
- [ ] Custom routines
- [ ] Weather & calendar briefing
- [ ] Smart Home integration
- [ ] Cross-platform support (macOS/Linux)

---

# Disclaimer

This project is intended for personal desktop automation. It continuously accesses your microphone while running, but all clap detection is performed locally. Audio is only sent externally when generating the configured ElevenLabs welcome message.

---

# License

MIT License

---

*"Sometimes the best shortcut is simply two claps."*| `MIN_RMS`     | Floor on how loud a block must be (helps in very quiet rooms).  |
| `SAMPLE_RATE` | Try `48000` if your device does not like `44100`.                 |

## Troubleshooting

- **Wrong or quiet mic:** On startup the script probes your default Windows input. If it is silent, it **auto-selects** the loudest working mic. To force a specific device, set `JARVIS_INPUT_DEVICE` in `.env` (index or name substring from `sounddevice.query_devices()`).
- **PortAudio / audio errors:** Update audio drivers or try another `SAMPLE_RATE`.
- **No reaction to claps:** Lower `SPIKE_RATIO` slightly or speak/clap closer to the mic.
- **Spam logs:** Raise `SPIKE_RATIO` or `COOLDOWN_S`.
- **No welcome speech:** Set `ELEVENLABS_API_KEY` and `ELEVENLABS_VOICE_ID` in `.env` and restart the terminal so variables load.
