MOTD:

The basic design is rather simple. The update-motd package creates a directory, /etc/update-motd.d, and installs a cronjob, /etc/cron.d/update-motd,
which calls /usr/sbin/update-motd every 10 minutes (by default).

/usr/sbin/update-motd uses run-parts to execute each script in /etc/update-motd.d in lexigraphic order, concatenating the results with the message-of-the-day
header, /etc/motd.tail.

In this way, users, or even other packages can drop scripts into /etc/update-motd.d to affect the MOTD.


nano /etc/update-motd.d/00-system-info

################################
#!/bin/bash
#
# File: 00-system-info - create the MOTD
# Copyright: (C) Martin Doering <martin@datapulp.de>
# License: Public Domain (do what you want with it)
#

diskusage=$(df -H -x squashfs -x tmpfs -x devtmpfs --total|grep total)

printf "%`tput cols`s"|tr ' ' '#'
echo " Hostname: $(hostname)"
echo " Distro: $(lsb_release -s -d)"
echo
echo " CPU: $(cat /proc/cpuinfo | grep 'model name' | head -1 | cut -d':' -f2)"
echo " Disk: $(echo $diskusage | awk '{ a = $2 } END { print a }'|sed 's/G/ G/'|sed 's/T/ T/')"
echo " Memory: $(free -m | head -n 2 | tail -n 1 | awk {'print $2'}) M"
echo " Swap: $(free -m | tail -n 1 | awk {'print $2'}) M"
echo

if which ip >/dev/null
then
echo " IP V4 Networks"
ip -o -4 -br a|grep -v '^lo'|sed 's/^/ /'|sed 's/\/..//'
echo
echo " IP V6 Networks"
ip -o -6 -br a|grep -v '^lo'|sed 's/^/ /'|sed 's/\/..//'
fi
printf "%`tput cols`s"|tr ' ' '#'

################################

chmod 755 /etc/update-motd.d/00-system-info