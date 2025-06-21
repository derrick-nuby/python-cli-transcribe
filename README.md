# Python-Transcribe — Command-Line Real-Time Speech-to-Text

A tiny CLI utility that pipes your microphone into [RealtimeSTT](https://github.com/KoljaB/RealtimeSTT) and prints beautifully color-coded live transcripts.
Use it to **drive other command-line tools or AIs with your voice**, prototype voice UIs, or simply take hands-free notes.

This project is based on the work from [Linguflex](https://github.com/KoljaB/Linguflex) by [@KoljaB](https://github.com/KoljaB), which provides an excellent foundation for voice-activated applications.

## ✨ Features

- **Low-latency partials + high-accuracy final sentences** (dual-model approach)
- Color-coded Rich TUI with automatic sentence splitting
- Optional _type-to-keyboard_ output (`pyautogui`) — talk → text appears in any window
- Works offline (models cached locally)
- Tested on Windows 10/11 + Python 3.12

## Demonstration

![Transcription Demo](video.mp4)

## Prerequisites

| Component | Version / Notes                                        |
| --------- | ------------------------------------------------------ |
| Python    | 3.10 – 3.12 (64-bit)                                   |
| OS        | Windows 10/11 (Linux & macOS should work—but untested) |
| Hardware  | Mic, ≥ 8 GB RAM (16 GB recommended for _large_ models) |

## Quick Start

```powershell
# 1 – clone repository
git clone https://github.com/derrick-nuby/python-cli-transcribe.git
cd python-cli-transcribe

# 2 – create virtual environment
python -m venv .venv
.\.venv\Scripts\activate

# 3 – install dependencies (CPU build)
python -m pip install --upgrade pip wheel
pip install torch torchaudio --index-url https://download.pytorch.org/whl/cpu
pip install -r requirements.txt      # or run the long pip line below

# 4 – first run (models auto-download to D:\models\whisper)
python .\realtimestt.py --model small --rt-model tiny.en --lang en --root "D:\models\whisper"
```

### Full dependency line

```powershell
pip install realtimestt PyAudio==0.2.14 rich colorama pyautogui ^
             faster-whisper pvporcupine webrtcvad-wheels halo scipy ^
             openwakeword websockets websocket-client soundfile
```

> **GPU users:** replace the CPU Torch wheel with
> `pip install torch torchaudio --index-url https://download.pytorch.org/whl/cu121`

## Configuration

Open `realtimestt.py` and tweak the `recorder_config` dict:

| Key                   | Meaning                                     | Typical values                               |
| --------------------- | ------------------------------------------- | -------------------------------------------- |
| `model`               | Accuracy-first model (runs after you pause) | `small`, `medium`, `large-v2`, custom HF ct2 |
| `realtime_model_type` | Ultra-fast model for partials               | `tiny.en`, `distil-small.en`                 |
| `language`            | Force language, else auto-detect            | `en`, `fr`, `rw`, …                          |
| `input_device_index`  | Mic index (see below)                       | `0`, `1`, …                                  |

### How to list microphones

```python
python - << "PY"
import pyaudio, pprint, json, sys
p = pyaudio.PyAudio()
for i in range(p.get_device_count()):
    info = p.get_device_info_by_index(i)
    if info['maxInputChannels'] > 0:
        print(f"{i}: {info['name']}")
PY
```

## Running the Test Suite

RealtimeSTT ships a minimal pytest to ensure models load and basic transcription works:

```powershell
pip install pytest
pytest -q tests\realtimestt_test.py
```

The test file is identical to
[https://github.com/KoljaB/RealtimeSTT/blob/master/tests/realtimestt_test.py](https://github.com/KoljaB/RealtimeSTT/blob/master/tests/realtimestt_test.py).

## Troubleshooting

| Symptom                                 | Fix                                                                                             |
| --------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `ModuleNotFoundError: install_packages` | A stub `install_packages.py` is provided; don't delete it, or comment out the two import lines. |
| Long first sentence latency             | Use a smaller `model` or set `beam_size=1`.                                                     |
| Mic not recognised                      | Set `input_device_index` to the ID printed by the listing snippet above.                        |
| High CPU on older PCs                   | Disable partials: `enable_realtime_transcription = False`.                                      |

## Roadmap

- Cross-platform packaging via PyInstaller
- Simple GUI wrapper (Tkinter + Rich)
- Docker image for server use
- Model-download manager

## Contributing

1. Fork → create a feature branch
2. `pre-commit install` (Black + Ruff)
3. Make your changes + add tests
4. Open a PR — be sure to describe **why** and **how**

Voice ideas, bug reports, or collaboration requests are welcome!

## Credits

- **Linguflex** by [@KoljaB](https://github.com/KoljaB) — the original foundation this project is based on
- **RealtimeSTT** by [@KoljaB](https://github.com/KoljaB) — core recording/transcription engine
- **Whisper** models by [OpenAI](https://github.com/openai/whisper)
- **Rich** by Will McGugan — console UI
- Special thanks to anyone who tests on macOS & Linux
