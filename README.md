# Hannover Messe 2025: UNS In A Box

## Prepare the Raspberry Pi

### Formatting the SD Card

Use a disk utility tool to format the SD Card with the following settings:
    - Partition Scheme: GUID
    - File System: FAT32

### Flashing the OS to the SD Card

Use the Raspberry Pi Imager to flash the OS to the SD Card. You can download it
from [here](https://www.raspberrypi.org/software/). Pick the Raspberry Pi OS
Lite (64-bit) image and follow the instructions to flash the OS to the SD Card
Use the following settings:

- Hostname: `raspberrypi.local`
- Username: `admin`
- Password: `admin`
- Enable SSH (with password)

### Connecting to the Raspberry Pi

Establish a wired ethernet connection between the Raspberry Pi and your
computer. The Raspberry Pi will automatically obtain an IP address via DHCP,
which will be displayed in the terminal of the Raspberry Pi after startup.

Use one of the following commands to connect to the Raspberry Pi:

```bash
ssh admin@raspberrypi.local
```

```bash
ssh admin@192.168.1.100    # Replace with the IP address of the Raspberry Pi
```

### [Optional] Configure WiFi

If a message is displayed about the localisation (country) of the Raspberry Pi
not being set, blocking Wi-Fi from working, you can set the localisation via the
Raspberry Pi Configuration tool.

```bash
sudo raspi-config
```

Select `Localisation Options` and then `WLAN Country`. Select an appropriate
country, e.g. `United States` and select `OK`.

Next, use the Network Manager TUI to configure the WiFi connection.

```bash
sudo nmtui
```

Select `Edit a connection` and then select `Add`. Select `Wi-Fi` and select `Create`.
Enter the network name and password.

Select `Activate a connection` and then select the network you just added.

### Installing Docker

First, update the package index:

```bash
sudo apt-get update
```

Use the following command to install Docker:

```bash
sudo apt-get install docker.io
```

### Installing Docker Compose

Install Docker Compose as a Docker plugin:

```bash
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.23.3/docker-compose-linux-$(uname -m) -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
```

Verify the installation:

```bash
docker compose version
```

### Set permissions for Docker

Add your user to the Docker group to avoid using sudo with Docker commands:

```bash
sudo usermod -aG docker $USER
```

**Note:** You'll need to log out and log back in for this change to take effect. Alternatively, you can run:

```bash
newgrp docker
```

## Import the Docker Compose file

<!-- TODO: Add warning about permissions for the container folders -->
