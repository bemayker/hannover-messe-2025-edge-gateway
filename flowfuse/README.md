# Flowfuse Services

Each remote instance is stored in a separate folder containing a `data` folder
for storing the data of the instance along with the `device.yml` file for
configuring the device.

## Adding a new instance

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
