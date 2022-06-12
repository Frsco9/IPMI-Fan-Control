This Skript controls the Fan Speed by reading the CPU temerature and compare it with the adjustible thresholds in 5 steps.

How to use it:


Install a debian or ubuntu invironmant/VM/LXC in your network
Bring it up to date and secure it.

install the IPMI tool:
apt install ipmitool

make sure you have acess to ipmi, for example read the current CPU temperature:
ipmitool -I lanplus -H 192.168.178.47 -U root -P calvin sdr type temperature

create a bash skript and give him a name (for example "fanctl") :
nano fanctl.sh

Copy the code to the File
change the IP, user, and password to your personal settings under "primary user settings"

under "advanced settings" you can change the threshold steps and the fan speeds

save it with ctrl+o (with nano editor)
leave with ctrl+x

make the skript executable:
chmod +x /usr/local/sbin/ipmiservice.sh

create a log file(to see a timeline of cpu temp and fan rpm):
mkdir /var/log/ipmiservice

create a systemd service to start the skript om every bootup:
nano /etc/systemd/system/ipmi.service

copy the systemd script
save&exit

eneble the systemd skript:
systemctl enable ipmi.service

start the systemd skript:
systemctl start ipmi.service

with this command you see the log file:
tail -f /var/log/ipmiservice/ipmi.log

Thanks for using the skipt!
DONATE paypal.me/shoko9
