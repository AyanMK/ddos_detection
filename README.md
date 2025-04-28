# Open source DDoS Detection
## Overview
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/overview.jpg?raw=true">





## pfSense setup guideline

Follow the step to setup pfSense VM in virtualbox

```bash
https://github.com/AyanMK/pfsense_setup
```


## PfSense log forwarding to server
Note: Before doing this you must setu an Ubuntu server/OS.

1. After login go to	Status > System Logs
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_1.png?raw=true">

2. Then go to settings:
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_2.png?raw=true">

3. Scroll down and enable remote logging
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_3.png?raw=true">

4. Scroll down and set the IP of the server (which will store the logs)
5. Then save.
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_4.png?raw=true">

6. Check the dumps using the command to the serve,
 - tcpdump -i <interface> port 514
 - tcpdump -i enp0s3 port 514

<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/server_log_capture.png?raw=true">
Check the output log here!

## SIME setup
