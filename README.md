# ruuvigw
Ruuvi gateway with InfluxDB and Grafana  
Tested to work at least with Raspberry Pi 4 and Banana Pi zero (installed as root)  
Should work with other ARM v7 also.

On Raspberry Pi, install https://www.raspberrypi.org/downloads/  
On Banana Pi zero, install https://www.armbian.com/bananapi-m2-zero/  
- I used version Armbian Buster

Then run:
```
apt install ansible git -y
git clone https://github.com/ilatvala/ruuvigw.git
cd ruuvigw/
```
Modify the MAC addresses of your ruuvi tags to the ruuvinames var part in the beginning of the playbook (if you know what they are)

Run playbook with:
```
ansible-playbook -i "127.0.0.1," ruuvigw.yml
```
Reboot to start using.
Grafana should be available in port 80 with some default Ruuvi dashboards.
