# ruuvigw
Ruuvi gateway with InfluxDB and Grafana. Works with https://ruuvi.com/ tags.  
Tested to work at least with Raspberry Pi 4 and Banana Pi zero (installed as root)  
Should work with other ARM v7 also.

On Raspberry Pi, install https://www.raspberrypi.org/downloads/  
On Banana Pi zero, install https://www.armbian.com/bananapi-m2-zero/  (used Armbian Buster)  

Then run:
```
apt install ansible git -y
git clone https://github.com/ilatvala/ruuvigw.git
cd ruuvigw/
```
Modify the MAC addresses of your ruuvi tags to the ruuvinames var part in the beginning of the ruuvigw.yml playbook (if you know what they are)  
You can also modify those later in /etc/RuuviCollector/target/ruuvi-names.properties

Run playbook with:
```
ansible-playbook -i "127.0.0.1," ruuvigw.yml
```
If you get any errors, then just run the playbook again.  
Reboot after installation.  
Grafana should be available in port 80 (admin/admin) with some default Ruuvi dashboards.

Here is the output from the playbook:  
```
root@raspberrypi:~/ruuvigw# ansible-playbook -i "127.0.0.1," ruuvigw.yml

PLAY [127.0.0.1] ******************************************************************

TASK [apt install needed packages] ************************************************
changed: [127.0.0.1]

TASK [Install Influxdb and Grafana] ***********************************************
changed: [127.0.0.1] => (item=https://dl.influxdata.com/influxdb/releases/influxdb_1.8.4_armhf.deb)
changed: [127.0.0.1] => (item=https://dl.grafana.com/oss/release/grafana_7.5.4_armhf.deb)

TASK [Enable and Start Influxdb] **************************************************
changed: [127.0.0.1]

TASK [Prerequisite pip libraries] *************************************************
changed: [127.0.0.1]

TASK [Create ruuvi database] ******************************************************
changed: [127.0.0.1]

TASK [Enable and Start Grafana] ***************************************************
changed: [127.0.0.1]

TASK [Check that Grafana API is fully running] ************************************
ok: [127.0.0.1]

TASK [Create ruuvi datasource] ****************************************************
changed: [127.0.0.1]

TASK [Install RuuviCollector] *****************************************************
changed: [127.0.0.1]

TASK [Build RuuviCollector] *******************************************************
changed: [127.0.0.1]

TASK [Modify tools permissions] ***************************************************
changed: [127.0.0.1] => (item=hcitool)
changed: [127.0.0.1] => (item=hcidump)

TASK [Copy example names config] **************************************************
changed: [127.0.0.1]

TASK [Copy example collector config] **********************************************
changed: [127.0.0.1]

TASK [Make a service file for RuuviCollector] *************************************
changed: [127.0.0.1]

TASK [Enable and Start RuuviCollector service] ************************************
changed: [127.0.0.1]

TASK [Fetch Ruuvi dashboards] *****************************************************
changed: [127.0.0.1]

TASK [Direct 3000 port to 80] *****************************************************
changed: [127.0.0.1]

TASK [Save iptables] **************************************************************
changed: [127.0.0.1]

TASK [Replace DB name for template] ***********************************************
changed: [127.0.0.1] => (item=/etc/RuuviDashboards/Ruuvi Measurements-1524070633428.json)
changed: [127.0.0.1] => (item=/etc/RuuviDashboards/Ruuvi Analytics-1524070587040.json)
changed: [127.0.0.1] => (item=/etc/RuuviDashboards/Ruuvi V2-1524070606424.json)
 [WARNING]: Consider using the replace, lineinfile or template module rather than
running sed.  If you need to use command because replace, lineinfile or template
is insufficient you can add warn=False to this command task or set
command_warnings=False in ansible.cfg to get rid of this message.


TASK [Import ruuvi dashboards to Grafana] *****************************************
changed: [127.0.0.1] => (item=/etc/RuuviDashboards/Ruuvi Measurements-1524070633428.json)
changed: [127.0.0.1] => (item=/etc/RuuviDashboards/Ruuvi Analytics-1524070587040.json)
changed: [127.0.0.1] => (item=/etc/RuuviDashboards/Ruuvi V2-1524070606424.json)

PLAY RECAP ************************************************************************
127.0.0.1                  : ok=20   changed=19   unreachable=0    failed=0
```

Note about Bananapi m2 zero: Sometimes bluetooth did not start properly at boot, so I put a check to crontab to reboot if BT is not ok. Also network connectivity problem would trigger a reboot. For additional stability I changed min and max CPU speed to 480MHz and governor to performance with armbian-config. My cron lines are:
```
*/5 * * * * hciconfig -a | grep -q DOWN >/dev/null && if [ $? -eq 0 ]; then /usr/sbin/shutdown -r +1 Bluetooth down, rebooting ; fi
*/10 * * * * ping -c4 www.google.com >/dev/null && if [ $? -ne 0 ]; then /usr/sbin/shutdown -r +1 Connection lost, rebooting ; fi
```

In grafana I sometimes need to disable alerting for a certain timeframe and I do that with an hourly script in cron
```
#!/bin/bash
# Disable alerts fetween hours
# usage: ./disable-alerts-between.sh 06 21
TZ='Europe/Helsinki' date +%H | grep $1 && (
  curl http://localhost:3000/api/admin/pause-all-alerts -d '{"paused":true}' -u admin:mypassword -H 'Content-Type: application/json'
)
TZ='Europe/Helsinki' date +%H | grep $2 && (
  curl http://localhost:3000/api/admin/pause-all-alerts -d '{"paused":false}' -u admin:mypassword -H 'Content-Type: application/json'
)
```
