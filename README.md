# Open source DDoS Detection
## Overview
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/overview.jpg?raw=true">


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

Note: After setting up server, enable pfSense to forward its log to that server.

# PfSense log forwarding to the server
Note: Before doing this you must setup an Ubuntu server or OS. Which must set under the pfsense internal network.
 
 We used ubuntu os that is why we can brows PfSense admin portal. If you use ubuntu server then you have to use another VM os under PfSense network to access the PfSense admin portal.
 
 hit the brouser with "https://192.168.1.1" 
 - Username: admin 
 - Password: pfsense

Then,

1. After login to the pfSense Dashboard
2. Go to	"Status" > "System Logs"
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_1.png?raw=true">

3. Then go to "settings"
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_2.png?raw=true">

4. Scroll down and enable remote logging
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_3.png?raw=true">

5. Scroll down and set the IP of the server (which will store the logs)
6. Then save.
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/pfsense_dashboard_4.png?raw=true">

7. Check the dumps using the command to the serve,
 - tcpdump -i <interface> port 514
 - tcpdump -i enp0s3 port 514

<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/server_log_capture.png?raw=true">
Check the output log here!



# SIEM setup
## System Requirements
Before running the script, please ensure that your system meets the following requirements:
- Ubuntu OS
- Minimum 4GB of RAM
- Minimum 20GB of free disk space

## Usage
1. Clone this repository to your local machine:
   ```bash
   git clone https://github.com/AyanMK/ddos_detection/siem_setup
   ```
2. Navigate to the repository's directory:
   ```bash
   cd siem_setup
   ```
3. Make the setup_script.sh executable:
   ```bash
   chmod +x setup_script.sh
   ```
4. Execute the setup_script.sh:
   ```bash
   ./setup_script.sh
   ```
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/SIEM setup.png?raw=true">
Note: Just type "y" and setup will start.
5. After few time setup will required a password. You may have to type multiple time. (We must notedown the password!)
6. After completing setup it will show "ALL Done". That's mean SIEM setup is done


# Setting up “Rsyslog” in SIEM server
Rsyslog is needed to capture those log inside the server.
By default ubuntu OS/ubuntu server have already “rsyslog”
1. To check that, type
```bash
   rsyslogd -v 
```
or,
```bash
   systemctl status rsyslog
```
2. Now enable server firewall port to receive logs
```bash
   Sudo ufw allow 514/udp
```
3. Then check those services
```bash
   netstat -4altunp | grep 514
```
4. 
```bash
   netstat -4altunp | grep 514
```
5. Now edit rsyslog.conf file
```bash
   nano /etc/rsyslog.conf
```
6. Then copy and pest those commands
```bash
   # provides UDP syslog reception
   module(load="imudp")
   input(type="imudp" port="514")
   
   # provides TCP syslog reception
   module(load="imtcp")
   input(type="imtcp" port="514")
   
   #Custom template to generate the log filename dynamically based on the client's IP address.
   $template RemInputLogs, "/var/log/remotelogs/%FROMHOST-IP%/%PROGRAMNAME%.log" 
   *.* ?RemInputLogs

```
7. Now to test the config file  
```bash
   rsyslogd -f /etc/rsyslog.conf -N1
```
8. Then restart
```bash
   systemctl restart rsyslog
```
9. Then our remote log will stored on this location “/var/log/remotelogs/192.168.1.1/”
```bash
   Cd /var/log/remotelogs/192.168.1.1/
```
```bash
   ls
```
10. Now add this “/var/log/remotelogs/192.168.1.1/” path to “filebeat.yaml” file
```bash
   Cd /etc/filebeat/
```
```bash
   ls
```
```bash	
   nano filebeat.yaml
```
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/filebeat_file_configuration.png?raw=true">

11. Now Type this in that location and seve
```bash
   - /var/log/remotelogs/192.168.1.1/*.log
```
Note: "192.168.1.1" this is pfsense IP

12. Now restart the filebeat
```bash
   filebeat setup -e
```
```bash
   systemctl restart filebeat
```

# Check the SIEM if it is getting those logs or not
From PfSense connected VM go to the browser and hit "https://localhost:5601" or "https://[SIEM-server-IP]:5601" 
- Username: Elastic
- Password: [password-that-you-used-in-SIEM-setup]

Go to "Manu bar or 3 Dot" > "Analystics" >"Discover"

<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/filebeat_log_check_1.png?raw=true">

Then select "filebeat" if not selected and then "Refresh". Then log will be visible.

<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/filebeat_log_check_2.png?raw=true">
Yes! SIEM is getting logs.


# Suricata insallation on PfSense

Access PfSense Web Interface:Open a web browser and log in to the PfSense web interface.
- Then go to "System" > "Package Manager.
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_setup_on_pfsense_1.png?raw=true">

- Then go to "Available Package" > Search for "suricata" and click "Install."
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_setup_on_pfsense_2.png?raw=true">

After installation complet, conferm suricata installation on pfsense
- Then go to "Installed Package"
- Suricata is now installed
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_setup_on_pfsense_3.png?raw=true">


# Add suricata custom rule on PfSense(Suricata)
1. Check the PfSense WAN port (Access PfSense Web Interface)
- Go to "Services" > "Suricata"
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_1.png?raw=true">

2. Now ssh to PfSense
- Open terminal and type "ssh admin@192.168.1.1"
- Password is pfsense password or diffolt password is "pfsense"
- Then press "8"
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_2.png?raw=true">

3. Then go to this location "/usr/local/etc/suricata" by this command
```bash
 cd /usr/local/etc/suricata
```
```bash
 ls
```
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_3.png?raw=true">

4. Now go to "suricata_54260_em0". Remember "54260" this number may be different on your time. You must check the "em0" then get inside it.
```bash
 cd suricata_54260_em0
```
Then edit "suricata.yaml"
```bash
 ee suricata.yaml
```
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_4.png?raw=true">

5. Go to line "346" and remove the default rule and custome rule like under image. Then press "Esc" > Save & exit
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_5.png?raw=true">

6. Now go to "rules" directory
```bash
 cd rules
```
Now edit "custom.rules"
```bash
 ee custom.rules
```
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_6.png?raw=true">

7. Add thos bellow rule (copy & paste). Then press "Esc" > Save & exit
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/suricata_custom_rule_add_7.png?raw=true">
Now our custome rulle adding complete!

## Suricata custom rule.
- Suricata custome rules are already inside our "siem_setup" folder
- Or you can clone it again by this command on terminal
```bash
 git clone https://github.com/AyanMK/ddos_detection/siem_setup/suricata_custom_rule.txt
```
Now open the text file and copy/paste it.
##

# Kibana Pipeline Adding
Go to SIEM Portal
- "3 Dot Manu" > Management > Deb Tools
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/kibana_pipeline_adding_1.png?raw=true">

- Go to console panel
- Now copy & paste the Kibana pipeline to the console
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/kibana_pipeline_adding_2.png?raw=true">

- Now press the "Play" buttone to check the pipeline
- If it show "200 - ok" then we are good to go!

## Kibana Pipeline

- Kibana pipeline are already inside our "siem_setup" folder
- Or you can clone it again by this command on terminal
```bash
 git clone https://github.com/AyanMK/ddos_detection/siem_setup/kibana_pipeline.txt
```
Now open the text file and copy/paste it.

# Create a Attack Monitoring Dashboard
- "3 Dot Manu" > Management > Deb Tools
- Create dashboard
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/create_kibana_dashboard_1.png?raw=true">
- You have to create you own dashborad

- You can use our layout also.
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/create_kibana_dashboard_2.png?raw=true">
<img width="800" alt="main_pic" src="https://github.com/AyanMK/ddos_detection/blob/main/assets/create_kibana_dashboard_3.png?raw=true">


