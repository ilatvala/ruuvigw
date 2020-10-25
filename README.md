# ruuvigw
Ruuvi gateway with InfluxDB and Grafana

On Raspberry Pi, install https://www.raspberrypi.org/downloads/

Then run:
apt install ansible git -y
git clone https://github.com/ilatvala/ruuvigw.git
cd ruuvigw/
ansible-playbook -i "127.0.0.1," ruuvigw.yml
