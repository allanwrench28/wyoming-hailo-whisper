# Wyoming Hailo Whisper AddOn for Home Assistant

This add-on integrates Hailo AI accelerators with Home Assistant's voice assistant using Whisper speech recognition models.

## 📋 Table of Contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation Guide](#installation-guide)
  - [Option A: Home Assistant OS (Recommended)](#option-a-home-assistant-os-recommended)
  - [Option B: Home Assistant Container](#option-b-home-assistant-container)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Advanced Usage](#advanced-usage)

## Overview

**What this add-on does:**
- Enables hardware-accelerated speech recognition using Hailo AI processors
- Integrates with Home Assistant's voice assistant via the Wyoming protocol
- Supports Whisper `tiny` and `base` models for speech-to-text

**Tested Configuration:**
- Home Assistant OS 16.1
- Raspberry Pi 5
- Hailo-8L (Hailo-8 should also work)

---

## Prerequisites

Before you begin, verify you have the required hardware and software:

### ✅ Step 1: Check Your Hardware

**Do you have a Hailo AI accelerator?**
- ✅ **Yes** → Continue to Step 2
- ❌ **No** → You need a Hailo-8 or Hailo-8L AI accelerator. Purchase from:
  - [Hailo Official Store](https://hailo.ai/products/)
  - Compatible devices: Raspberry Pi 5 with Hailo-8L M.2 module

**Is your Hailo device connected?**
- For Raspberry Pi 5: Ensure the Hailo-8L is properly inserted in the M.2 slot
- Official connection guide: [Hailo Installation Guide](https://hailo.ai/developer-zone/documentation/)

### ✅ Step 2: Check Your Home Assistant Setup

**What type of Home Assistant installation do you have?**

Run this command to check:
```bash
ha info
```

**If the command works:**
- ✅ You have **Home Assistant OS** → Use [Option A](#option-a-home-assistant-os-recommended)

**If the command doesn't work (command not found):**
- Check if you're running Home Assistant Container:
  ```bash
  docker ps | grep homeassistant
  ```
- ✅ You have **Home Assistant Container** → Use [Option B](#option-b-home-assistant-container)
- ❌ Neither works → Visit [Home Assistant Installation Guide](https://www.home-assistant.io/installation/)

### ✅ Step 3: Verify SSH Access

**Can you SSH into your Home Assistant machine?**

Try connecting:
```bash
ssh root@homeassistant.local
# or
ssh root@<your-ha-ip-address>
```

- ✅ **Connection successful** → Continue to installation
- ❌ **Connection failed** → Enable SSH:
  - Home Assistant OS: Install [Terminal & SSH add-on](https://github.com/home-assistant/addons/tree/master/ssh)
  - Documentation: [SSH Access Guide](https://www.home-assistant.io/common-tasks/os/#enabling-ssh)

---

## Installation Guide

### Option A: Home Assistant OS (Recommended)

This is the recommended installation method for most users.

#### Step 1: Access Your Home Assistant Machine

1. **Open the Terminal & SSH add-on** in Home Assistant:
   - Go to Settings → Add-ons
   - Click on "Terminal & SSH"
   - Click "OPEN WEB UI"
   - Or SSH from your computer: `ssh root@homeassistant.local`

2. **Navigate to the addons directory:**
   ```bash
   cd /addons
   ```

#### Step 2: Download This Add-on

**Option 2a: Using Git (Recommended)**

If git is available:
```bash
git clone https://github.com/allanwrench28/wyoming-hailo-whisper.git
cd wyoming-hailo-whisper
```

**Option 2b: Manual Download**

If git is not available:
```bash
mkdir -p wyoming-hailo-whisper
cd wyoming-hailo-whisper
# Use the Terminal to copy the repository contents here
```
- Download the repository as ZIP from: [GitHub Repository](https://github.com/allanwrench28/wyoming-hailo-whisper)
- Extract and copy all files to `/addons/wyoming-hailo-whisper/`

#### Step 3: Download Hailo Runtime Packages

1. **Create the packages directory:**
   ```bash
   mkdir -p hailo_packages
   cd hailo_packages
   ```

2. **Download the required files:**
   - Visit [Hailo Developer Zone](https://hailo.ai/developer-zone/) (requires free account registration)
   - **Sign up** if you don't have an account
   - Once logged in, go to **Downloads** section
   - **Filter by:** HailoRT / ARM64 / Linux / Python 3.10
   - Download these **two files** (version 4.21):
     - `hailort_4.21.0_arm64.deb`
     - `hailort-4.21.0-cp310-cp310-linux_aarch64.whl`

3. **Upload the files to your Home Assistant:**
   
   **Via SCP from your computer:**
   ```bash
   scp hailort_4.21.0_arm64.deb root@homeassistant.local:/addons/wyoming-hailo-whisper/hailo_packages/
   scp hailort-4.21.0-cp310-cp310-linux_aarch64.whl root@homeassistant.local:/addons/wyoming-hailo-whisper/hailo_packages/
   ```

   **Or via wget (if direct links are available):**
   ```bash
   # This requires authentication, use the SCP method above if needed
   ```

4. **Verify the files are in place:**
   ```bash
   cd /addons/wyoming-hailo-whisper/hailo_packages
   ls -lh
   ```
   You should see both files listed.

#### Step 4: Install the Add-on

1. **Refresh the Add-on Store:**
   - Go to **Settings** → **Add-ons**
   - Click the **⋮** (three dots) menu in the top right
   - Click **Check for updates**
   - Click **Reload**

2. **Find and install the add-on:**
   - Scroll down to **Local add-ons** section
   - Click on **Wyoming Hailo Whisper**
   - Click **INSTALL**
   - Wait for installation to complete (this may take 10-15 minutes)

#### Step 5: Configure the Add-on

1. **Go to the Configuration tab:**
   - Select your **Hailo device type:**
     - `hailo8l` (for Hailo-8L)
     - `hailo8` (for Hailo-8)
   - Select your **Whisper model variant:**
     - `tiny` (faster, less accurate)
     - `base` (slower, more accurate)

2. **Example configuration:**
   ```yaml
   device: hailo8l
   variant: base
   ```

3. **Click SAVE**

#### Step 6: Start the Add-on

1. **Start the add-on:**
   - Click the **START** button
   - Enable **"Start on boot"** (optional, but recommended)
   - Enable **"Watchdog"** (optional, auto-restart on crash)

2. **Check the logs:**
   - Click the **Log** tab
   - You should see: `Init Wyoming Hailo device 'hailo8l' with Whisper model 'base'`
   - If you see errors, check the [Troubleshooting](#troubleshooting) section

#### Step 7: Integrate with Home Assistant

1. **Add the Whisper integration:**
   - Go to **Settings** → **Devices & Services**
   - Click **+ ADD INTEGRATION**
   - Search for **"Wyoming Protocol"**
   - Click on it

2. **Configure the integration:**
   - **Host:** `localhost` (or `homeassistant.local`)
   - **Port:** `10600`
   - Click **SUBMIT**

3. **Configure Voice Assistant:**
   - Go to **Settings** → **Voice assistants**
   - Select your voice assistant (or create a new one)
   - Set **Speech-to-text** to **wyoming-hailo-whisper**
   - Click **SAVE**

#### Step 8: Test Your Setup

1. **Test the voice assistant:**
   - Use the Home Assistant app or interface
   - Tap and hold the microphone icon
   - Speak a command: "Turn on the lights"
   - Check if it recognizes your speech

2. **If it works:** ✅ You're all set!
3. **If it doesn't work:** Check [Troubleshooting](#troubleshooting)

---

### Option B: Home Assistant Container

⚠️ **Note:** This installation method is not extensively tested. Use with caution.

#### Prerequisites Check

Before starting, verify your system has the required tools:

**Step 1: Check if Docker is installed:**
```bash
docker --version
```
- ✅ **Shows version** (e.g., `Docker version 24.0.0`) → Continue
- ❌ **Command not found** → Install Docker:
  - [Docker Installation Guide](https://docs.docker.com/engine/install/)
  - For Raspberry Pi: [Docker on Raspberry Pi](https://docs.docker.com/engine/install/debian/)

**Step 2: Check if Git is installed:**
```bash
git --version
```
- ✅ **Shows version** → Continue
- ❌ **Command not found** → Install Git:
  ```bash
  sudo apt-get update
  sudo apt-get install -y git
  ```

**Step 3: Check Hailo driver version:**
```bash
hailortcli -version
```

**Interpret the results:**
- ✅ **Shows version 4.21.x** → Continue to installation
- ⚠️ **Shows version 4.20.x** → Consider upgrading:
  - Follow [Hailo's upgrade guide](https://hailo.ai/developer-zone/documentation/hailort-v4-21-0/?sp_referrer=install/install.html#ubuntu-installation)
  - Or modify the Dockerfile for v4.20 (see `Dockerfile_experiments`)
- ❌ **Command not found** → Install Hailo drivers:
  - Visit [Hailo Developer Zone](https://hailo.ai/developer-zone/)
  - Download and install HailoRT for your platform
  - [Installation Guide](https://hailo.ai/developer-zone/documentation/hailort-v4-21-0/?sp_referrer=install/install.html)

#### Installation Steps

**Step 1: Clone the Repository**

Open a terminal on your Raspberry Pi and run:
```bash
cd ~
git clone https://github.com/allanwrench28/wyoming-hailo-whisper.git
cd wyoming-hailo-whisper
```

**Step 2: Download Hailo Packages**

1. **Create the packages directory:**
   ```bash
   mkdir -p hailo_packages
   cd hailo_packages
   ```

2. **Download required files:**
   - Visit [Hailo Developer Zone](https://hailo.ai/developer-zone/)
   - **Sign up/Log in** (free account required)
   - Navigate to **Downloads** section
   - **Filter settings:**
     - Product: HailoRT
     - Architecture: ARM64
     - OS: Linux
     - Python: Python 3.10
   - Download these files for **version 4.21**:
     - `hailort_4.21.0_arm64.deb`
     - `hailort-4.21.0-cp310-cp310-linux_aarch64.whl`

3. **Move files to the hailo_packages directory:**
   ```bash
   # If downloaded to ~/Downloads:
   mv ~/Downloads/hailort_4.21.0_arm64.deb .
   mv ~/Downloads/hailort-4.21.0-cp310-cp310-linux_aarch64.whl .
   ```

4. **Verify files are present:**
   ```bash
   ls -lh
   ```
   Expected output:
   ```
   -rw-r--r-- 1 user user  XX MB hailort_4.21.0_arm64.deb
   -rw-r--r-- 1 user user  XX MB hailort-4.21.0-cp310-cp310-linux_aarch64.whl
   ```

**Step 3: Configure Device Type (Optional)**

The default device is `hailo8l`. If you have a Hailo-8 (not 8L):

```bash
cd ~/wyoming-hailo-whisper
nano docker-entrypoint.sh
```

Change the `--device` parameter:
```bash
# Change this line:
    --device 'hailo8l' \
# To:
    --device 'hailo8' \
```

Save and exit (Ctrl+X, then Y, then Enter)

**Step 4: Build the Docker Image**

```bash
cd ~/wyoming-hailo-whisper
docker build --network=host -t wyoming-hailo-whisper:latest .
```

This will take 10-20 minutes. Expected output:
```
[+] Building 600.0s
...
Successfully built abc123def456
Successfully tagged wyoming-hailo-whisper:latest
```

**Step 5: Test Run the Container**

```bash
docker run -it -p 10600:10600 --privileged wyoming-hailo-whisper:latest
```

You should see:
```
INFO Init Wyoming Hailo device 'hailo8l' with Whisper model 'tiny'
```

Press **Ctrl+C** to stop.

**Step 6: Integrate with Home Assistant**

1. **Add the Wyoming Protocol integration:**
   - Open Home Assistant UI
   - Go to **Settings** → **Devices & Services**
   - Click **+ ADD INTEGRATION**
   - Search for **"Wyoming Protocol"**

2. **Configure the integration:**
   - **Host:** IP address of the machine running Docker (e.g., `192.168.1.100`)
   - **Port:** `10600`
   - Click **SUBMIT**

3. **Configure Voice Assistant:**
   - Go to **Settings** → **Voice assistants**
   - Select your assistant (or create new)
   - Set **Speech-to-text** to **wyoming-hailo-whisper**
   - Click **SAVE**

**Step 7: Run as a Persistent Service (Optional)**

For permanent installation, use Docker Compose:

1. **Edit compose.yaml** (optional):
   ```bash
   nano compose.yaml
   ```

2. **Start with Docker Compose:**
   ```bash
   docker-compose up -d
   ```

3. **Check status:**
   ```bash
   docker-compose ps
   docker-compose logs -f
   ```

**Alternative: Using Portainer**
- Install [Portainer](https://www.portainer.io/)
- Import the `compose.yaml` file
- Deploy the stack

---

## Configuration

### Available Options

| Option | Values | Description |
|--------|--------|-------------|
| `device` | `hailo8l`, `hailo8` | Your Hailo AI accelerator model |
| `variant` | `tiny`, `base` | Whisper model size (tiny=faster, base=more accurate) |

### Model Selection Guide

**Tiny Model:**
- ✅ Faster response time (~2-3 seconds)
- ✅ Lower resource usage
- ⚠️ Less accurate with accents/noise

**Base Model:**
- ✅ More accurate recognition
- ✅ Better with accents and background noise
- ⚠️ Slower response time (~4-5 seconds)

---

## Troubleshooting

### Issue: Add-on won't start

**Check the logs:**
```bash
# In Home Assistant Terminal:
docker logs addon_local_wyoming_hailo_whisper
```

**Common causes:**
1. **Missing Hailo packages:** Verify files exist in `hailo_packages/`
2. **Wrong Hailo device:** Check `/dev/hailo0` exists: `ls -l /dev/hailo0`
3. **Driver mismatch:** Update Home Assistant OS or Hailo drivers

### Issue: "Hailo device not found"

**Check if device is detected:**
```bash
ls -l /dev/hailo0
```

**If not found:**
- Ensure Hailo module is physically connected
- Reboot the Raspberry Pi
- Check [Hailo troubleshooting guide](https://hailo.ai/developer-zone/documentation/)

### Issue: Speech recognition not working

**Test the add-on directly:**

1. **Check add-on logs** for errors
2. **Verify Wyoming integration:**
   - Settings → Devices & Services
   - Check Wyoming Protocol is "Connected"
3. **Test microphone:** Ensure your microphone works in Home Assistant
4. **Check network:** Ping the host running the add-on

### Issue: "HEF file not found"

**Error message:** `FileNotFoundError: HEF file not found`

**Solution:**
```bash
cd /addons/wyoming-hailo-whisper/wyoming_hailo_whisper/app
./download_resources.sh
```

### Enable Debug Logging

**For Home Assistant OS:**
1. Go to add-on Configuration tab
2. Add to configuration:
   ```yaml
   log_level: debug
   ```

**For Container:**
Edit `docker-entrypoint.sh` and add `--debug`:
```bash
python3 -m wyoming_hailo_whisper \
    --uri 'tcp://0.0.0.0:10600' \
    --device 'hailo8l' \
    --variant 'tiny' \
    --debug
```

### Interactive Debugging (Container Only)

Run the container interactively:
```bash
docker run -it --entrypoint /bin/bash --privileged wyoming-hailo-whisper:latest
```

Inside the container, manually run:
```bash
./docker-entrypoint.sh
```

Ask Home Assistant voice assistant a question and watch the console output.

---

## Advanced Usage

### Using Different HailoRT Versions

**For HailoRT 4.20:**
1. Download version 4.20 packages instead:
   - `hailort_4.20.0_arm64.deb`
   - `hailort-4.20.0-cp310-cp310-linux_aarch64.whl`
2. Use `Dockerfile_experiments` instead of `Dockerfile`
3. Build: `docker build -f Dockerfile_experiments --network=host -t wyoming-hailo-whisper:latest .`

### Bluetooth Audio Input (Experimental)

**Prerequisites:**
- Raspberry Pi 5 configured with PipeWire
- Bluetooth microphone paired and connected

**Steps:**

1. **Rebuild with audio libraries:**
   ```bash
   docker build -f Dockerfile_experiments --network=host -t wyoming-hailo-whisper:bluetooth .
   ```

2. **Run with PipeWire socket mounted:**
   ```bash
   docker run -it --privileged \
     -v /run/user/1000/pipewire-0:/tmp/pipewire-0 \
     -p 10600:10600 \
     --entrypoint /bin/bash \
     wyoming-hailo-whisper:bluetooth
   ```

3. **Test the vanilla Hailo example:**
   ```bash
   ./start-hailo-example.sh
   ```

4. **Expected output:**
   ```
   Press Enter to start recording, or 'q' to quit:
   ```

5. Press Enter, speak into the Bluetooth microphone, and check the transcription.

---

## Support & Contributing

**Need help?**
- [Open an issue](https://github.com/allanwrench28/wyoming-hailo-whisper/issues)
- [Hailo Community Forum](https://community.hailo.ai/)
- [Home Assistant Community](https://community.home-assistant.io/)

**Want to contribute?**
- Pull requests are welcome!
- Please test your changes before submitting

---

## License

See [LICENSE.md](LICENSE.md) for details.

---

## Acknowledgments

- [Hailo AI](https://hailo.ai/) for the AI accelerator
- [OpenAI Whisper](https://github.com/openai/whisper) for the speech recognition model
- [Wyoming Protocol](https://github.com/rhasspy/wyoming) for Home Assistant integration
