# IPMI-Fan-Control
#!/bin/bash

#                                primary User Settings
#--------------------------------------------------------------------------------------
#enter IPMI IP Adress
IP="192.168.178.47"
#enter IPMI Username
USERNAME="root"
#enter IPMI Password
PASSWORD="calvin"

#                                 advanced Settings
#--------------------------------------------------------------------------------------
#Speedsteps in 1(%)
spdnorm="0x29"
spd50="0x32"
spd70="0x49"
spd85="0x59"
spd100="0x64"
#tempsteps
temp50="40"
temp70="55"
temp85="65"
temp100="75"
#frequency of running script in seconds
sleeptime="5"
#Log File Adress
log=/var/log/ipmiservice/ipmi.log
#--------------------------------------------------------------------------------------
#Standard variables

#Standerd IPMI Command
std="ipmitool -I lanplus -H $IP -U $USERNAME -P $PASSWORD"
#set to manual command
man="raw 0x30 0x30 0x01 0x00"
#speed set
speedset="raw 0x30 0x30 0x02 0xff"
#tempsteps
temp50u=$(($temp50 +1 ))
temp70u=$(($temp70 +1 ))
temp85u=$(($temp85 +1 ))
#set manual fan control
$std $man
#fan speed algorhytmus
while true
do
#CPU Temp
currenttemp=$($std sdr type temperature | grep 0Eh | awk '{print $9}')
#Fan RPM
fanrpm=$($std  sdr type fan | grep 30h | awk '{print $10}')
if [ "$currenttemp" -ge 0 ] && [ "$currenttemp" -le "$temp50" ]; then
        $std $speedset $spdnorm
elif [ "$currenttemp" -ge "$temp50u" ] && [ "$currenttemp" -le "$temp70" ]; then
        $std $speedset $spd50
elif [ "$currenttemp" -ge "$temp70u" ] && [ "$currenttemp" -le "$temp85" ]; then
        $std $speedset $spd70
elif [ "$currenttemp" -ge "$temp85u" ] && [ "$currenttemp" -le "$temp100" ]; then
        $std $speedset $spd85
else
        $std $speedset $spd100
fi
sleep $sleeptime
echo "$(date "+%Y-%m-%d %H:%M:%S")" "CPU Temp : $currenttemp | Fan : $fanrpm" >> "$log"
done
