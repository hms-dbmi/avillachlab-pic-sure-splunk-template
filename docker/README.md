
This directory provides a template for the splunk universal forwarder to pic up PIC-SURE logs from docker containers ans ship them to a configured Splunk 8.0+ server.  


There are two files that need to be configured, inputs.conf and outputs.conf.  replace the values in all caps with the correct values and run docker-compose up.

The default value for the avillach lab splunk8 source type is "picsure_log4j".

INSTALLATION DIRECTIONS:
  1) clone this repo to `/var/local/docker-config`
  2) create a file named `monitoring.env` within `/var/local/docker-config/avillachlab-pic-sure-splunk-template` that contains: 
      DD_API_KEY, SSL_PASS, SPLUNK_PASS, SPLUNK_START_ARGS, and SPLUNK_PASSWORD
  3) within `/var/local/docker-config/avillachlab-pic-sure-splunk-template/docker/splunk/local/inputs.conf` edit the following variables:
      HOSTNAME, \_\_ENVIRONMENT_INDEX\_\__ and \_\_SOURCE_TYPE\_\_
  4) within `/var/local/docker-config/avillachlab-pic-sure-splunk-template/docker/splunk/local/outputs.conf` edit the following variable:
      SPLUNK_SERVER (be sure to use "HOSTNAME:PORT" format)
  5) Use the following command to start the splunk container:
  
      `docker run -d --name splunk -v /var/run/docker.sock:/var/run/docker.sock:ro -v /var/log/wildfly-docker-logs:/docker/log/wildfly:ro -v /var/lib/docker/containers:/host/containers:ro -v /usr/local/docker-config/splunk/avillachlab-pic-sure-splunk-template/docker/splunk/certs:/opt/splunk/certs:rw -v /usr/local/docker-config/splunk/avillachlab-pic-sure-splunk-template/docker/splunk/local:/opt/splunkforwarder/etc/system/local:rw -v /var/log/wildfly-docker-logs:/var/log/wildfly:ro --env-file .env --restart always  splunk/universalforwarder:8.0.1`

