# avillachlab-pic-sure-splunk-template
Dashboard templates for Splunk 8.0

To integrate pic-sure logging, a few items must be configured, both in splunk and on the system (host) running the various pic-sure stack applications.  A splunk forwarder needs to be set up on all hosts that should be monitored with Splunk; this process is described in the various repositories for 


To start with, a PICSURE source type needs to be configured on the splunk server that will receive the log data.  This source type need only be configured once, as it is shared between all pic-sure applications that use log4j output.


To correctly parse out the useful fields for reporting, an extract needs to be applied to the soruce type.  When creating a new source type use default values for all fields but the name & description, then on the 'Advanced' tab and add a new setting called 'EXTRACT-USER,QUERY' with the following regular expression to extract the USER and QUERY fields from pic-sure and psama logs:

```
^(?:[^ \n]* )+?___(?P<USER>.*?)___(.*?)___(?P<QUERY>.*?)___
```

Then a splunk index will need to be created.  There should be one index per environment (which may contain several hosts).  This index serves to tie the logs from various hosts together and will be the key that the dashboard uses to gather data.  The index name should reflect the environment and stage e.g., 'pl-dev' or 'udn-prod'

The next step is to prepare the template for use.  The base template file in source control is a generic version, with the string 'ENVIRONMENT' as a placeholder for the index name and the display value for some dashboard titles.  This file should be filtered using either a utility such as sed or a text editor find-and-replace-all function to replace the ENVIROMENT string with the correct case-sensitive name for the splunk index defined above.  Once this is prepared, a new dashboard can be created.

Under the apps: Search and Reporting, open the 'Dashboards' page.  Use the button in the top right to create a new dashboard.  This brings you to a page showing the dashboard edit controls.  In the top left corner, there is an option to view the 'raw' content.  Click this button to open an XML view of the dashboard, and past in the contents of the configured template file, overwriting any default content that may be present.  Save the dashboard and you are ready to go!

Scheduling email delivery for a new dashboard is handled through the 'export' button in the top right area, under 'schedule PDF delivery'.  Fill in the dialog with the appropriate information, and enjoy your regular splunk reports from picsure!
