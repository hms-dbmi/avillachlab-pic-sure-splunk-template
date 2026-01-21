# PIC-SURE Splunk Dashboard Templates

Dashboard templates for Splunk to monitor PIC-SURE application.

## Overview

This repository contains dashboard templates for monitoring the PIC-SURE application using Splunk.

## Prerequisites

- Splunk Enterprise server 10.0 or later

## Setup Instructions

### Initial: Configure the PICSURE Source Type in Splunk Server - If it does not exist

The source type `picsure_log4j` only needs to be configured once and is shared across all PIC-SURE applications using log4j output.

1. Navigate to **Settings > Source types** in the Splunk server
2. Click **New Source Type**
3. Configure the basic settings:
   - **Name**: `picsure_log4j`
   - **Description**: `PIC-SURE application logs`
   - Leave all other fields at their default values
4. Switch to the **Advanced** tab
5. Add a new setting:
   - **Name**: `EXTRACT-USER,QUERY`
   - **Value**: 
     ```regex
     ^(?:[^ \n]* )+?___(?P<USER>.*?)___(.*?)___(?P<QUERY>.*?)___
     ```
6. Save the source type

This regular expression extracts the `USER` and `QUERY` fields from PIC-SURE and PSAMA logs for reporting.

### ON Splunk server: 1. Create Splunk Index

Create one index per environment (each environment may contain multiple hosts).

1. Navigate to **Settings > Indexes**
2. Click **New Index**
3. Name the index using the pattern: `{environment}-{stage}`
   - Examples: `pl-dev`, `udn-prod`, `bdcat-staging`
4. Configure other settings as needed for your organization
5. Save the index

**Note**: The index name will be used to tie logs from various hosts together and is required for the dashboard configuration.

### ON PIC_Sure server: 2. Configure the Splunk Forwarder

On each host running PIC-SURE applications:

1. Clone the Docker Splunk Frowarder
2. Configure the forwarder to monitor PIC-SURE log files
3. Start the forwarder container

### Optional: Create a Dashboard

#### Prepare the Dashboard Template

The template file contains the placeholder string `__ENVIRONMENT_INDEX__` and `__ENVIRONMENT_Name__` that must be replaced with actual values.

**Option 1: Using sed (Linux/Mac)**
```bash
cp picsure_splunk_dashboard_template.xml picsure_splunk_dashboard.xml
sed -i 's/__ENVIRONMENT_INDEX__/your-index-name/g' picsure_splunk_dashboard.xml
sed -i 's/__ENVIRONMENT_NAME__/your-env-name/g' picsure_splunk_dashboard_template.xml
```

**Option 2: Using a text editor**
1. Open the template file in your preferred text editor
2. Use Find & Replace All to replace `__ENVIRONMENT_INDEX__` with your index name and `__ENVIRONMENT_NAME__` with the name
3. Ensure the replacement is case-sensitive
4. Save the file

#### Load template into Splunk server

1. Navigate to **Apps > Search & Reporting**
2. Click **Dashboards** in the top menu
3. Click **Create New Dashboard** (top right)
4. Provide a title and description for your dashboard
5. Click **Create Dashboard**
6. In the dashboard editor, click **Source** in the top left corner to view the XML
7. Delete the default content and paste in your configured template
8. Click **Save**

Your dashboard is now ready to use!

### Optional: Schedule Email Delivery

To receive regular PDF reports of your dashboard:

1. Open your dashboard
2. Click **Export** in the top right
3. Select **Schedule PDF Delivery**
4. Configure the schedule:
   - Set the frequency (daily, weekly, etc.)
   - Add recipient email addresses
   - Customize the email subject and message
5. Click **Schedule** to save

You'll now receive regular Splunk reports for your PIC-SURE environment!

## Troubleshooting

- **No data appearing**: Verify that the Splunk forwarder is running and configured with the correct source type and index
- **Missing fields**: Ensure the EXTRACT-USER,QUERY regex is correctly configured in the source type
- **Dashboard errors**: Confirm that all instances of `__ENVIRONMENT_INDEX__` and `__ENVIRONMENT_Name__` in the template were replaced with your actual index name

## Support

For issues or questions, please contact your Splunk Server administrator.