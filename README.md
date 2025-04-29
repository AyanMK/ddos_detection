# Open source DDoS Detection
## Overview
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/overview.jpg?raw=true">





# pfSense setup guideline

Follow the step of under github link to setup pfSense in virtualbox

```bash
https://github.com/AyanMK/pfsense_setup
```
Then,
# Ubuntu server setup
Ubuntu server or OS setup in virtualbox. For our R&D we used ubuntu OS. Ubuntu server & OS both works fine. Ensure those system requirements. Server network mustbe under pfSense and ensure internet connectivity of the server.
## - System requirements
- Processor: 2 core
- RAM: 4GB (Minimum)
- Disk space 20GB (Minimum)

## - Network
Network adapters 1:	"Internal Network"

Note: After setting up server, enable pfSense log forwarding to this server

# PfSense log forwarding to the server
Note: Before doing this you must setup an Ubuntu server or OS.

1. After login to the pfSense Dashboard
2. Go to	"Status" > "System Logs"
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_1.png?raw=true">

3. Then go to "settings"
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_2.png?raw=true">

4. Scroll down and enable remote logging
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_3.png?raw=true">

5. Scroll down and set the IP of the server (which will store the logs)
6. Then save.
<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_4.png?raw=true">

7. Check the dumps using the command to the serve,
 - tcpdump -i <interface> port 514
 - tcpdump -i enp0s3 port 514

<img width="615" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/server_log_capture.png?raw=true">
Check the output log here!

## SIME setup
