# ruuvigw
Ruuvi gateway with InfluxDB and Grafana

On Raspberry Pi, install https://www.raspberrypi.org/downloads/

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
