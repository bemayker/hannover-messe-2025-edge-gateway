![banner](assets/banner-mayker.jpg)

# Hannover Messe 2025: UNS In A Box  <!-- omit in toc -->

- [Prepare the Raspberry Pi](#prepare-the-raspberry-pi)
  - [Formatting the SD Card](#formatting-the-sd-card)
  - [Flashing the OS to the SD Card](#flashing-the-os-to-the-sd-card)
  - [Connecting to the Raspberry Pi](#connecting-to-the-raspberry-pi)
  - [Configuring WiFi](#configuring-wifi)
  - [Installing Docker](#installing-docker)
  - [Installing Docker Compose](#installing-docker-compose)
  - [Setting permissions for Docker](#setting-permissions-for-docker)
- [Cloning the repository](#cloning-the-repository)
- [Adding the FlowFuse Device Agent configurations](#adding-the-flowfuse-device-agent-configurations)
- [Using the Docker Compose file](#using-the-docker-compose-file)
- [Accessing the services](#accessing-the-services)
  - [FlowFuse instances](#flowfuse-instances)
  - [MQTT Broker](#mqtt-broker)
  - [InfluxDB](#influxdb)
  - [Telegraf](#telegraf)
- [Adding another FlowFuse instance](#adding-another-flowfuse-instance)

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

> [!NOTE]
> You are free to change the hostname, username and password, but make sure to
> remember them as you will need them to connect to the Raspberry Pi later.

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

### Configuring WiFi

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

### Setting permissions for Docker

Add your user to the Docker group to avoid using sudo with Docker commands:

```bash
sudo usermod -aG docker $USER
```

**Note:** You'll need to log out and log back in for this change to take effect. Alternatively, you can run:

```bash
newgrp docker
```

## Cloning the repository

Clone the repository to the Raspberry Pi:

```bash
git clone https://github.com/bemayker/hannover-messe-2025-edge-gateway.git
```

Navigate to the repository and run the following command to set the necessary
permissions:

```bash
sudo chmod -R 777 ./influxdb ./hivemq ./flowfuse
```

## Adding the FlowFuse Device Agent configurations

Create remote instances in the FlowFuse Cloud for the devices you want to connect
to the Raspberry Pi. Add the configurations for the devices to their respective
folders in the `flowfuse` directory under the `data` folder.

## Using the Docker Compose file

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

## Accessing the services

### FlowFuse instances

To access either of the Node-RED instances, go the FlowFuse Dashboard and find
the remote instance you want to access. You can find a link to the Node-RED
editor there.

### MQTT Broker

To access the MQTT Broker, you can use any MQTT client. The connection details
are:

From within the docker cluster (e.g. from any of the FlowFuse instances):

- Host: `hivemq`
- Port: `1883`

From outside the docker cluster:

- Host: `raspberrypi.local` or `<ip-address>`
- Port: `1883`

### InfluxDB

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

### Telegraf

Telegraf is configured to collect data from the HiveMQ MQTT broker and write it to InfluxDB. The Telegraf configuration file is located at `telegraf/config/telegraf.conf`.

By default, Telegraf subscribes to all topics (`#`) on the MQTT broker and writes the data to InfluxDB. The data is stored in the bucket specified in the `.env` file with the organization and token also specified there.

If you need to customize the Telegraf configuration, you can edit the configuration file and restart the service with:

```bash
docker compose restart telegraf
```

## Adding another FlowFuse instance

First, prepare the folder structure for the new instance:

```bash
# Create a new folder for the instance
mkdir flowfuse/<instance-name>

# Add the data folder
mkdir flowfuse/<instance-name>/data

# Add the device.yml file and retrieve the content from the Flowfuse Cloud
touch flowfuse/<instance-name>/device.yml
```

Then, add the instance to the `docker-compose.yml` file:

```bash
# Add the instance to the services section of the docker-compose.yml file
<service-name>:
    image: flowfuse/device-agent:latest
    restart: unless-stopped
    ports:
        - "<port>:1880" # Node-RED port
    volumes:
        - ./flowfuse/<instance-name>/data:/opt/flowfuse-device
```

> [!IMPORTANT]
> Replace `<service-name>` with the name of the service in the `docker-compose.yml` file.
> Replace `<port>` with the port on which the service will be exposed. Ensure that the port is not already in use by another service.
> Replace `<instance-name>` with the name of the instance. This is the name of the folder in the `flowfuse` directory.
