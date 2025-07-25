<p align="center">
  <img src="logos/logo.png" alt="WhisPad Logo" width="120"/>
</p>

# WhisPad

WhisPad is a transcription and note management tool designed so anyone can turn their voice into text and easily organize their ideas. The application lets you use cloud models (OpenAI) or local whisper.cpp models to work offline.

> **WARNING**
> Always back up your notes before upgrading to a new version. Never expose the app to the internet without additional security measures.

## Table of Contents
1. [Main Features](#main-features)
2. [Disclaimer](#disclaimer)
3. [Quick Setup](#quick-setup)
4. [Installing with Docker Desktop](#installing-with-docker-desktop)
5. [Installing from the Terminal](#installing-from-the-terminal)
6. [API Key Configuration](#api-key-configuration)
7. [Speaker Diarization Setup](#speaker-diarization-setup)
8. [Usage Guide](#usage-guide)
9. [Screenshots](#screenshots)
10. [Contributors](#contributors)

## Main Features
- Real-time voice-to-text transcription from the browser.
- Write and edit markdown notes.
- Integrated note manager: create, search, tag, save, restore and download in Markdown format.
- Automatic text enhancement using AI (OpenAI, Google, OpenRouter or Groq) with streaming responses.
- A blue marker indicating where the transcription will be inserted.
- Compatible with multiple providers: OpenAI, SenseVoice and local whisper.cpp. No model is bundled, but you can download tiny, small, base, medium or large versions from the interface.
- **NEW: SenseVoice Integration** - Advanced multilingual speech recognition with emotion detection and audio event recognition for 50+ languages.
- **NEW: Speaker Diarization Support** - Automatically identify different speakers in audio recordings for both Whisper Local and SenseVoice (see [Speaker Diarization Guide](SPEAKER_DIARIZATION.md)).
- Download or upload local (.bin) whisper.cpp models directly from the interface.
- Upload audio files which are automatically transcribed and stored alongside your notes.
- Export all notes in a ZIP file with one click.
- Deleting a note automatically removes its related quizzes and flashcards.
- Mobile-friendly interface.
- User login with per-user note folders and admin management tools.
- Admin has access to all providers and can manage which ones are available for each user.
- Reliable save and autosave: headings and ordered lists are now preserved correctly.

## Disclaimer
This application is currently in testing and is provided **as is**. I take no responsibility for any data loss that may occur when using it. Make sure you make frequent backups of your data.

## Quick Setup
If you are not comfortable with the terminal, the easiest method is to use **Docker Desktop**. You only need to install Docker, download this project and run it.

1. Download Docker Desktop from <https://www.docker.com/products/docker-desktop/> and install it like any other application.
2. Download this repository as a ZIP from GitHub and unzip it in the folder of your choice.
3. Open Docker Desktop and select **Open in Terminal** (or open a terminal in that folder). Type:
   ```bash
   docker compose up
   ```
4. Docker will download the dependencies and show *"Starting services..."*. When everything is ready, open your browser at `https://localhost:5037`.
5. Sign in with **admin** and the password from `ADMIN_PASSWORD` the first time to access the app.
6. To stop the application, press `Ctrl+C` in the terminal or use the *Stop* button in Docker Desktop.

## Installing with Docker Desktop
This option is ideal if you don't want to worry about installing Python or dependencies manually.

1. Install **Docker Desktop**.
2. Open a terminal and clone the repository:
   ```bash
   git clone https://github.com/tu_usuario/whispad.git
   cd whispad
   ```
   (If you prefer, download the ZIP and unzip it.)
3. Run the application with:
   ```bash
   docker compose up
   ```
4. Go to `https://localhost:5037` and start using WhisPad.
5. Log in with **admin** using the password from `ADMIN_PASSWORD` on first use.
6. To stop it, press `Ctrl+C` in the terminal or run `docker compose down`.
7. If you want to use LM Studio or Ollama for local AI text improvement, set the host to
   `host.docker.internal` in the configuration page so the container can reach
   your local instance.
   Use the **Update Models** button to fetch the list of available models from
   `http://<lmstudio-host>:<port>/v1/models` automatically.

## Installing from the Terminal
If you prefer not to use Docker, you can also run it directly with Python:

1. Make sure you have **Python 3.11** or higher and **pip** installed.
2. Clone the repository or download the code and go to the project folder:
   ```bash
   git clone https://github.com/tu_usuario/whispad.git
   cd whispad
   ```
3. Install the Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Download a whisper.cpp model with the script or from the **Models** menu (no model is included by default):
   ```bash
   bash install-whisper-cpp.sh
   ```
   You can also download or upload models directly from the interface.
5. Run the server:
   ```bash
   python backend.py
   ```
6. Open `index.html` in your browser or serve the folder with `python -m http.server 5037` and visit `https://localhost:5037`.
7. Log in with **admin** using the password from `ADMIN_PASSWORD` the first time you access the app.

## API Key Configuration
Copy `env.example` to `.env` and add your API keys:
```bash
cp env.example .env
```
Edit the `.env` file and fill in the variables `OPENAI_API_KEY`, `GOOGLE_API_KEY`, `DEEPSEEK_API_KEY`, `OPENROUTER_API_KEY` and `GROQ_API_KEY` for the services you want to use. These keys enable cloud transcription and text enhancement.
If you want to send each saved note to an external workflow (for example, an n8n or Dify instance), also set `WORKFLOW_WEBHOOK_URL` and optionally `WORKFLOW_WEBHOOK_TOKEN`.
Use `WORKFLOW_WEBHOOK_USER` to choose which user's notes are sent. The webhook payload now includes the username so your workflow can fetch the note from the correct folder.
Set the database credentials in your `.env` file using `POSTGRES_USER`, `POSTGRES_PASSWORD` and `POSTGRES_DB`.
`DATABASE_URL` should point to `postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@db:5432/${POSTGRES_DB}`.
Set `ADMIN_PASSWORD` to define the initial admin user's password.

## Speaker Diarization Setup

WhisPad supports speaker diarization to automatically identify different speakers in audio recordings. This feature uses [pyannote-audio](https://github.com/pyannote/pyannote-audio) and requires a HuggingFace token with access to gated models.

### Step-by-Step HuggingFace Token Setup

1. **Create a HuggingFace Account**
   - Go to [huggingface.co](https://huggingface.co) and create a free account

2. **Generate an Access Token**
   - Navigate to [HuggingFace Settings > Access Tokens](https://huggingface.co/settings/tokens)
   - Click "New token"
   - Choose "Read" permissions (sufficient for model access)
   - Copy the generated token

3. **Request Access to Gated Models**
   You need to accept the terms and conditions for these specific models:
   
   - **pyannote/speaker-diarization-3.1**: [Accept terms here](https://huggingface.co/pyannote/speaker-diarization-3.1)
   - **pyannote/segmentation-3.0**: [Accept terms here](https://huggingface.co/pyannote/segmentation-3.0)
   - **speechbrain/spkrec-ecapa-voxceleb**: [Accept terms here](https://huggingface.co/speechbrain/spkrec-ecapa-voxceleb)
   
   **Important**: Click "Accept" on each model page. Access is usually granted immediately, but may take a few minutes.

4. **Add Token to Environment**
   Add your HuggingFace token to your `.env` file:
   ```bash
   HUGGINGFACE_TOKEN=your_token_here
   ```

5. **Enable Speaker Diarization**
   - Restart WhisPad after adding the token
   - In the transcription interface, check the "Enable Speaker Diarization" option
   - Works with both Whisper Local and SenseVoice providers

### Features
- **Automatic Speaker Detection**: Identifies different speakers in audio
- **Speaker Labels**: Adds `[SPEAKER 1]`, `[SPEAKER 2]` labels to transcriptions
- **Multi-Provider Support**: Works with both local Whisper and SenseVoice
- **Format Compatibility**: Automatically converts WebM recordings to WAV for processing
- **Clear Line Separation**: Each speaker segment is placed on a separate line for easy reading

### Troubleshooting
- **Token Issues**: Ensure you've accepted terms for all required models
- **Performance**: Speaker diarization adds processing time, especially on CPU
- **Quality**: Works best with clear audio and distinct speakers

## Usage Guide
1. Press the microphone button to record audio and get real-time transcription.
2. Select text fragments and apply style or clarity improvements with a click.
3. Organize your notes: add titles, tags and search them easily.
4. Download each note in Markdown or the entire set in a ZIP file.
5. Download additional whisper.cpp models from the **Models** menu (you can still drag and drop your own files) and enjoy offline transcription.
6. Use the **Restore** menu to import previously saved notes.

With these instructions you should have WhisPad running in just a few minutes with or without Docker. Enjoy fast transcription and all the benefits of organizing your ideas in one place!

## Data Persistence and User Management

WhisPad is designed to persist your data between container restarts, updates, and recreations through Docker volumes and a PostgreSQL database:

### Persistent Data
- **Notes**: Stored in `./saved_notes/` (mounted to `/app/saved_notes` in container)
- **Audio Files**: Stored in `./saved_audios/` (mounted to `/app/saved_audios` in container)
- **Users**: Stored in PostgreSQL (`whispad` database)
- **Provider Config**: Stored in PostgreSQL database (no external file needed)
- **Models**: Stored in `./whisper-cpp-models/` (mounted to `/app/whisper-cpp-models` in container)
- **Logs**: Stored in `./logs/` (mounted to `/var/log/nginx` in container)

### User Management
- **Default Admin**: Username `admin`, password set via `ADMIN_PASSWORD`
- **User Configuration**: Admins can create users and assign different transcription/postprocessing providers
- **Per-User Folders**: Each user's notes are isolated in their own folder under `saved_notes/`
- **Single User Mode**: Set `MULTI_USER=false` in `.env` or the compose file to automatically sign in with the admin account and bypass the login page

### Initial Setup
On first start the backend creates the `admin` user using the password from `ADMIN_PASSWORD`.

**Important**: Change the admin password immediately after first login for security!

## Screenshots

Here are some screenshots of WhisPad in action:

<p align="center">
  <img src="screenshots/screenshot1.png" alt="Main interface" width="700"/>
</p>

<p align="center">
  <img src="screenshots/screenshot2.png" alt="Editing notes" width="700"/>
</p>

<p align="center">
  <img src="screenshots/screenshot3.png" alt="Manage whisper models" width="700"/>
</p>

<p align="center">
  <img src="screenshots/screenshot4.png" alt="Transcribed notes list" width="700"/>
</p>

<p align="center">
  <img src="screenshots/screenshot5.png" alt="Transcribed notes list" width="700"/>
</p>

## Contributors

- **@Drakonis96** - Main idea and core coding.
- **@laweschan** - Contributed fresh insights and tested the application.

This project was developed with the help of AI tools including **Perplexity Labs**, **OpenAI Codex**, and **Claude 4**. Local transcription models run thanks to [whisper.cpp](https://github.com/ggml-org/whisper.cpp) (a copy is bundled here for easier installation).
