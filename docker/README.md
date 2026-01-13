# PIC-SURE Splunk Universal Forwarder Configuration

This directory provides configuration templates for the Splunk Universal Forwarder to collect PIC-SURE logs from Docker containers and forward them to a Splunk 10.0+ server.

## Overview

The Universal Forwarder monitors Docker container logs and ships them to your configured Splunk server for indexing and analysis. This setup is designed specifically for PIC-SURE applications running in Docker containers.

## Prerequisites

- Docker installed and running
- Access to a Splunk 10.0+ server
- PIC-SURE applications running in Docker containers
- Root or sudo access on the host machine

## Configuration Files

Three main configuration files need to be customized:

- **`inputs.conf`**: Defines what logs to monitor and collect
- **`outputs.conf`**: Specifies where to send the collected logs
- **`server.conf`**: Specifies for connection and performance tuning

## Installation Steps

### 1. Clone the Repository

```bash
cd $DOCKER_CONFIG_DIR
git clone https://github.com/hms-dbmi/avillachlab-pic-sure-splunk-template splunk-forwarder
cd splunk-forwarder
rm -rf .git # removes git tracking so credentials can't be pushed to git
```

### 2. Configure inputs.conf

Edit `docker/splunk/local/inputs.conf`

Replace the following placeholder variables:

- **`__HOSTNAME__`**: The hostname of this server (e.g., `prod-app-01`)
- **`__ENVIRONMENT_INDEX__`**: The Splunk index name (e.g., `pl-dev`, `udn-prod`)

### 3. Configure outputs.conf

Edit `docker/splunk/local/outputs.conf`

Replace the following placeholder variable:

- **`__SPLUNK_SERVER__`**: Your Splunk server address in `HOSTNAME:PORT` format (e.g., `splunk.example.com:9997`)

### 4. Configure server.conf

Edit `docker/splunk/local/server.conf`

Replace the following placeholder variables:

- **`__PASS4SYMKEY__`**: Used for Splunk's internal encryption
- **`__SSLPASSWORD__`**: Used for encryption of SSL certificates

### 4. Start the Splunk Universal Forwarder

Ensure that `DOCKER_CONFIG_DIR` env variable is set. Run the following command to start the forwarder container:

```bash
cd docker
docker compose up -d
```

**Note**: This command assumes the default path structure. Adjust volume mount paths in `docker/docker-compose.yml` if your installation differs.

### 5. Verify the Installation

Check that the container is running:

```bash
docker ps | grep splunk-forwarder
```

View the container logs:

```bash
docker logs splunk-forwarder
```

You should see messages indicating the forwarder has started successfully and is connecting to your Splunk server.

## Troubleshooting

**Forwarder not sending data:**
- Verify the Splunk server address and port in `outputs.conf`
- Check that the Splunk server is reachable: `telnet <splunk-server> 9997`
- Review forwarder logs: `docker logs splunk-forwarder`

**Permission errors:**
- Ensure log directories have appropriate read permissions
- Verify the forwarder has access to the Docker socket

**Container fails to start:**
- Check that all variables are set correctly
- Verify volume mount paths exist on the host system
- Review Docker logs for specific error messages

## Management Commands

**Stop the forwarder:**
```bash
docker stop splunk
```

**Restart the forwarder:**
```bash
docker restart splunk
```

**Remove the forwarder:**
```bash
docker stop splunk
docker rm splunk
```

**Update configuration:**
After modifying `inputs.conf`, `outputs.conf`, or `server.conf` files restart the container:
```bash
docker restart splunk
```

## Add additional logs

  1. Add a read only volume mount to `docker-compose.yml` file pointing to the new log location
  2. Update the `inputs.conf` file to add the new folder/file
  3. Restart the splunk forwarder by recomposing the image `docker compose up -d` which will detect new docker volume mount changes.

## Security Considerations

- Keep the `.env` file secure with appropriate file permissions (e.g., `chmod 600`)
- Regularly update the Universal Forwarder image to the latest stable version
- Never commit credentials to version control

## Support

- Check the Splunk Universal Forwarder documentation: https://help.splunk.com/en/splunk-cloud-platform/forward-and-process-data/universal-forwarder-manual/10.0/about-the-universal-forwarder/about-the-universal-forwarder
- Review container logs for error messages