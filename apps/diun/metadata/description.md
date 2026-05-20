# DIUN (Docker Image Update Notifier)

Diun is a powerful tool that monitors your Docker repositories and sends notifications when new versions of your images are available. This custom Runtipi version includes a **Tailscale sidecar** to securely scan remote servers (like your Raspberry Pi) via your Tailnet.

## 🛠 Setup Instructions

### 1. SSH Keys & Remote Access
To monitor remote servers, DIUN needs to authenticate via SSH.
1. Create the folder: `app-data/diun/keys`.
2. Place your private keys (e.g., `id_rsa_vps`, `id_rsa_rpi`) inside that folder.
3. **Set Permissions:** SSH will fail if keys are too "public." Run this command on your VPS:
   ```bash
   chmod 600 /path/to/runtipi/app-data/diun/keys/*
   ```

### 2. Generating the known_hosts Fingerprints
DIUN cannot interactively ask you to "trust" a remote server. You must provide the fingerprints in advance:
1. On your VPS (or any machine with Tailscale/SSH access), run the following commands.
2. Replace the bracketed text with your actual IP addresses or hostnames:
	```bash
	ssh-keyscan -H [REMOTE_1] >> known_hosts
	ssh-keyscan -H [REMOTE_2] >> known_hosts
	```
3. Move the resulting `known_hosts` file into `app-data/diun/keys/`.

### 3. Tailscale Integration
Generate an **Auth Key** in your Tailscale Admin Console.
Enter this key into the **Tailscale Auth Key** field in the Runtipi UI settings for DIUN.
Once started, this app will appear as `diun-scanner` in your Tailnet, allowing it to "see" your RPi and other VPS.

### 4. Notification Filtering
This app is pre-configured with the following logic:
* Production Only: It ignores any tags containing rc, dev, beta, alpha, or unstable.
* Global Blocklist: It will not notify you about updates for specific images that are pinned for compatibility:
	* postgres:14
	* postgres:16.9
	* traefik:v3.6.14
To customize these lists, edit the regfilters section in `app-data/diun/config/diun.yml`.


## Configuration
* **Gotify**: Ensure you use your Gotify App Token. The default URL is `http://localhost:8129` (internal Runtipi network).
* **Schedule**: Default is midnight daily (0 0 * * *).