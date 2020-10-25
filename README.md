# ruuvigw
Ruuvi gateway with InfluxDB and Grafana  
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
Grafana should be available in port 80 with some default Ruuvi dashboards.

Here is the output from the playbook:  
```
root@raspberrypi:~/ruuvigw# ansible-playbook -i "127.0.0.1," ruuvigw.yml

PLAY [127.0.0.1] ******************************************************************

TASK [apt install needed packages] ************************************************
changed: [127.0.0.1]

TASK [Install Influxdb and Grafana] ***********************************************
changed: [127.0.0.1] => (item=https://dl.influxdata.com/influxdb/releases/influxdb_1.8.3_armhf.deb)
changed: [127.0.0.1] => (item=https://dl.grafana.com/oss/release/grafana_7.2.2_armhf.deb)

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

TASK [Install RuuviCollector] *****************************************************
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
