APEX Engineer AI
A voice-based race engineer for F1 25 on Windows. APEX reads live game telemetry, listens to your radio messages, answers using an AI model, and can optionally press configured keyboard controls.

!!! IMPORTANT -- HANDS FREE MODE IS TURNED ON BY DEFAULT WHICH MEANS WHEN YOU START ENGINEER YOUR MICROPHONE WILL BE ACTIVE UNTILL YOU STOP ENGINEER !!!

The project is designed to be customised. Every user should create their own local settings in config.json before racing.

AI provider note: this release currently uses Groq's OpenAI-compatible cloud API. Local Whisper speech recognition does not mean the language model runs locally.
Status: Community project / experimental. Always test controls in dry-run mode before allowing the program to send key presses in-game.

Features
- Live F1 25 UDP telemetry (port 20777, packet format 2025)
- Voice input using local speech recognition (faster-whisper)
- Hands-free voice activity detection or push-to-talk
- AI radio replies through the Groq API
- British neural text-to-speech using Edge TTS
- Proactive race briefings and telemetry-triggered alerts
- Optional configurable keyboard sequences for pit requests and other game actions
- Text input fallback if you do not want to use a microphone
Requirements
- Windows 10 or Windows 11
- Python 3.10 or newer (64-bit recommended)
- F1 25 for live telemetry
- Internet connection for Groq, package installation, first-time Whisper model download, and Edge TTS
- A Groq API key
- VLC is recommended for reliable, interruptible voice playback
The speech recognition model runs locally after it has been downloaded. AI text generation uses the Groq cloud API. Do not assume that your voice audio is sent to Groq: the application sends recognised text to the configured AI endpoint.
Installation
1. Download the repository using Code → Download ZIP, or clone it:git clone https://github.com/YOUR-USERNAME/APEX-Engineer-AI.git
2. Open the project folder.
3. Install the Python packages:install_all.bat
4. Create a Groq API key at Groq Console.
5. Open config.json and enter your key in llm.api_key. Also customise your driver name, engineer name, voice, microphone and controls.
6. In F1 25, enable UDP telemetry:
   - UDP Format: 2025
   - IP Address: 127.0.0.1
   - Port: 20777
7. Run check_setup.bat.
8. Start the engineer with start_engineer.bat.
Never publish your personal API key. Keep your edited config.json private if it contains a key. The starter configuration included in this repository has an empty API-key field.
Configuration guide
All settings are in config.json. The file is intentionally included as a safe starter template so you can edit it without creating a new file.
General and AI
Setting	What it does
driver_name	Name APEX uses when speaking to you
engineer_name	Engineer's name
dashboard	compact, full, or off
llm.provider	Currently groq
llm.api_key	Your private Groq API key
llm.model	Groq model ID available to your account
llm.temperature	Response creativity
llm.max_tokens	Maximum response length
llm.history_messages	Conversation history retained


Voice output
Under voice:
- enabled: turn spoken replies on or off
- name: Edge TTS voice, for example en-GB-RyanNeural
- rate: speech speed, for example +0%, +10%, or -10%
You can browse available voices with the Microsoft Edge TTS voice list.
Microphone and speech recognition
Under mic:
- enabled: enable or disable microphone input
- mode: vad for hands-free listening or ptt for push-to-talk
- ptt_key: keyboard key to hold in PTT mode
- ptt_joystick_button: optional zero-based wheel/controller button number
- joystick_id: controller index, usually 0
- device_index: microphone input device number; null uses the system default
- model: Whisper model, for example small.en or medium.en
- language: speech language code; the default is English (en)
- device: cpu by default
- compute_type: int8 by default
- vad_threshold: increase it if background noise triggers the microphone; decrease it if speech is missed
- vad_silence_seconds: how long APEX waits after you stop speaking
- listen_while_speaking: whether the microphone keeps listening while APEX is speaking
Useful helpers:
- list_mics.bat lists audio devices. Put the correct input-device number in mic.device_index.
- check_setup.bat checks the installation.
- python main.py --miclevel displays microphone loudness to help tune vad_threshold.
- Set mic.mode to ptt to use push-to-talk.
- find_wheel_button.bat identifies the button number to use for ptt_joystick_button.
Automatic race briefings
Under alerts:
- periodic_interval_seconds: how often APEX gives a regular race briefing, in seconds. The default is 60; the code enforces a minimum of 15 seconds.
Telemetry-triggered alerts (such as weather changes, safety car, penalties, tyre wear and low fuel) are separate from the regular briefing timer.
Keyboard controls and custom actions
Start with controls.dry_run: true. In this mode APEX prints the keys it would press without sending them to the game.
1. In F1 25, open Settings → Controls and bind keyboard keys for the radio/MFD actions you want to automate.
2. Enter those key names under controls.keys.
3. Define an action under controls.sequences.
4. Add a plain-English explanation under controls.descriptions.
Example:
"keys": {
  "radio_open": "t",
  "radio_up": "up",
  "radio_down": "down",
  "radio_select": "right",
  "radio_close": "esc"
},
"sequences": {
  "pit_request": [
    "radio_open",
    "radio_down*2",
    "radio_select",
    "radio_close"
  ]
},
"descriptions": {
  "pit_request": "request a pit stop at the end of this lap"
}
radio_down*2 means press the configured radio_down key twice. You can also add waits, for example wait_0.5, to pause for half a second between inputs.

The number of menu steps depends on your own F1 25 menu layout and bindings. The example is only a starting point; verify every sequence manually.

When you have verified the sequence in dry-run mode and are ready to test it in-game, set controls.dry_run to false. Keep controls.require_game_focus enabled so APEX only sends keys when the game window is focused. You can also disable all automation with controls.enabled: false.

Important: Automated key presses can select the wrong menu item if your game menu differs. Use this feature at your own risk and supervise it.
F1 25 telemetry settings
In F1 25, enable UDP telemetry and use:
- UDP IP: 127.0.0.1
- UDP Port: 20777
- UDP Format: 2025
If you changed the port, update udp.port in config.json.
Using APEX
Start start_engineer.bat, then start or resume an on-track F1 25 session.

You can speak to APEX through the microphone or type messages into the console. Example questions:
- “Engineer, radio check.”
- “How are the tyres looking?”
- “What's the gap to the car ahead?”
- “How is our fuel?”
- “Can we make it to the end?”
- “Box this lap.”
Console commands:
Command	Action
mute	Stop spoken output
unmute	Enable spoken output
controls on / controls off	Enable or disable game controls
dry on / dry off	Enable or disable dry-run mode
do ACTION_NAME	Test a configured action
exit	Shut down APEX


Troubleshooting
- No telemetry: check F1 25 UDP settings, IP, port and packet format.
- No AI response: check your internet connection and Groq API key in config.json.
- Microphone not detected: run list_mics.bat and set mic.device_index.
- Speech recognition is inaccurate: use a headset, tune vad_threshold, or try a larger Whisper model. Larger models need more system resources.
- APEX hears its own voice: use headphones or set listen_while_speaking to false.
- Voice playback is unreliable: install VLC.
- Controls do not work: keep dry-run enabled first, verify key names and sequences, and ensure the F1 25 window title contains the configured window_title_contains text.
- Logs: runtime logs are written to logs/telemetry.log.
Known limitations
- APEX can only report information present in the telemetry packets it receives. It cannot reliably see opponents' tyre compounds, their complete strategies, or whether a pit request was accepted by the game.
- Teammate information is limited to fields available from the current session telemetry.
- AI-generated strategy advice can be wrong. Treat it as assistance, not as an authoritative race strategy system.
- The quality and speed of answers depend on the selected Groq model and your internet connection.
- Keyboard automation depends on your own F1 25 key bindings and menu layout.
Privacy and security
- Your Groq API key is a secret. Do not share it, commit it, or include it in screenshots.
- Speech recognition is performed locally with Whisper after the model is downloaded. Recognised text is sent to Groq for AI responses.
- Do not use a configuration file containing secrets when sharing bug reports. Share a copy with llm.api_key removed.
Contributing
Bug reports and improvements are welcome. When reporting an issue, include:
- Your Windows and Python versions
- The relevant settings with API keys and personal information removed
- The error message or a short excerpt from logs/telemetry.log
Please test changes with game controls disabled or in dry-run mode before using them in a live session.
Engineer has a lot of room for improvment so feel free to contribute
