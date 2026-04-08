
# SagePublicHomeAssistantSensorData
Expose some sensors to the internet publicly. (No auth required)  I wont go into Port Forwarding i assume you have already done this.

This allows you to then configure Google Gemini to obtain your sensor data via Gemini.

Login to Gemini and add a "personal context" like this:
When I say "Get HA Data" then a sensor name, you should go to this website http://EXTERNALIP/DNS:8123/local/ha_data.json and tell me the value of the sensor.


Examples below use 3 of my sensors. Inverter, powerpal & battery. Update them to use your own.


Step 1: Create the Shell Script
You need a physical script file to handle the file writing. This bypasses the Home Assistant template restriction.

Use the File Editor or SSH to create a file at /config/www/write_json.sh.

Paste this code into the .sh file.

#!/bin/bash
# $1=solar, $2=consumption, $3=battery, $4=updated
echo "{\"solar\":\"$1\", \"consumption\":\"$2\", \"battery\":\"$3\", \"updated\":\"$4\"}" > /config/www/ha_data.json


You must give this file permission to run. If you have the Terminal/SSH add-on, run:
chmod +x /config/www/write_json.sh

Step 2: Update configuration.yaml
Now, update your shell_command to call this script and pass the data as arguments.


shell_command:
  write_dashboard_json: >
    /bin/bash /config/www/write_json.sh 
    "{{ states('sensor.inverter_5010kmsu21') }}" 
    "{{ states('sensor.powerpal_live_consumption_watts') }}" 
    "{{ states('input_number.ch_battery') }}" 
    "{{ now().strftime('%Y-%m-%d %H:%M:%S') }}"


Step 3: Reboot Home Assistant.

Access the sensor data from here:
http://ExternalIP:PORT/local/ha_data.json
