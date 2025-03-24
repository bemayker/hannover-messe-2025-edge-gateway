# Hannover Messe 2025: UNS In A Box

## 1. Prepare the Raspberry Pi

### 1.1. Formatting the SD Card

Use a disk utility tool to format the SD Card with the following settings:
    - Partition Scheme: GUID
    - File System: FAT32

### 1.2. Flashing the OS to the SD Card

Use the Raspberry Pi Imager to flash the OS to the SD Card. You can download it
from [here](https://www.raspberrypi.org/software/). Pick the Raspberry Pi OS
Lite (64-bit) image and follow the instructions to flash the OS to the SD Card
Use the following settings:

- Hostname: `raspberrypi.local`
- Username: `admin`
- Password: `admin`
- Enable SSH (with password)

> [!NOTE]
> You are free to change the hostname, username and password, but make sure to
> remember them as you will need them to connect to the Raspberry Pi later.

### 1.3. Connecting to the Raspberry Pi

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

### 1.4. [Optional] Configure WiFi

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

### 1.5. Installing Docker

First, update the package index:

```bash
sudo apt-get update
```

Use the following command to install Docker:

```bash
sudo apt-get install docker.io
```

### 1.6. Installing Docker Compose

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

### 1.7. Set permissions for Docker

Add your user to the Docker group to avoid using sudo with Docker commands:

```bash
sudo usermod -aG docker $USER
```

**Note:** You'll need to log out and log back in for this change to take effect. Alternatively, you can run:

```bash
newgrp docker
```

## 2. Using the Docker Compose file

Create a `.env` file in the same directory as the `docker-compose.yml` file and
add the following environment variables:

```bash
# InfluxDB
INFLUXDB_INIT_MODE=setup
INFLUXDB_INIT_USERNAME=your-username
INFLUXDB_INIT_PASSWORD=your-password
INFLUXDB_INIT_ORG=your-organization
INFLUXDB_INIT_BUCKET=your-bucket
INFLUXDB_INIT_ADMIN_TOKEN=your-admin-token
```

To start the services, run the following command:

```bash
docker compose up -d

# Check the status of the services
docker compose ps

# Check the logs of a specific service (run without specifying the service name to see all logs)
docker compose logs <service-name>
```

To restart the services, run the following command:

```bash
docker compose restart
```

To stop the services, run the following command:

```bash
docker compose down
```

## 3. Accessing the services

### 3.1. Accessing the FlowFuse instances

To access either of the Node-RED instances, go the FlowFuse Dashboard and find
the remote instance you want to access. You can find a link to the Node-RED
editor there.

### 3.2. Accessing the MQTT Broker

To access the MQTT Broker, you can use any MQTT client. The connection details
are:

From within the docker cluster (e.g. from any of the FlowFuse instances):

- Host: `hivemq`
- Port: `1883`

From outside the docker cluster:

- Host: `raspberrypi.local` or `<ip-address>`
- Port: `1883`

### 3.3. Accessing the InfluxDB

To access the InfluxDB, you can use any InfluxDB client. The connection details
are:

From within the docker cluster (e.g. from any of the FlowFuse instances):

- Host: `influxdb`
- Port: `8086`

From outside the docker cluster:

- Host: `raspberrypi.local` or `<ip-address>`
- Port: `8086`

> [!NOTE]
> Use the credentials you set in the `.env` file to access the InfluxDB.
