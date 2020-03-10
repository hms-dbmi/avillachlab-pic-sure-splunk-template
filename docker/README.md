
This directory provides a template for the splunk universal forwarder to pic up PIC-SURE logs from docker containers ans ship them to a configured Splunk 8.0+ server.  


There are two files that need to be configured, inputs.conf and outputs.conf.  replace the values in all caps with the correct values and run docker-compose up.

The default value for the avillach lab splunk8 source type is picsure_log4j.
