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
