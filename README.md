# Born2beRoot
My 42 Lyon's Born2beRoot

Download Debian 10 @ https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-10.9.0-amd64-netinst.iso

Open VirtualBox

New

Name

Type Linux

Version Debian (64-bit)

Create fixed size VDI (Several ~4 Go)

Start VM

Select the iso you just downloaded

Install

Select time zone / language / keymap etc

Hostname "your42login"42

Root password Ceciestunbon42

New user "your42login"

User password Ceciestunbon42

Select LVM encrypt with separated /home

Disk passphrase Ceciestunbon42

No http proxy

Select softwares SSH and standard system utilities

Install GRUB to /dev/sda

Log as root


apt install sudo
  
adduser root sudo
  
adduser "your42login" sudo
  
addgroup user42
  
adduser "your42login" user42
  
touch /var/log/sudo/sudolog

apt install vim

vim /etc/sudoers.d/sudoconf
  
  Defaults  passwd_tries=3
  
  Defaults  badpass_message="!NO! Try <Ceciestunbon42>"
  
  Defaults  logfile="/var/log/sudo/sudolog"
  
  Defaults  requiretty
  
  Defaults  secure_path="/sur/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/snap/bin"

apt install ufw

ufw enable

ufw allow 4242

vim /etc/login.defs
  
  :se nu
  
  go to line 160
  
  PASS_MAX_DAYS 30
  
  PASS_MIN_DAYS 2
  
  PASS_WARN_AGE 7

apt install libpam-pwquality

vim /etc/pam.d/common-password
  
  :se nu
  
  goto line 25 and add at the end of the line
  
  minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root

vim /etc/ssh/sshd_config
  
  :se nu
  
  go to line 13 and uncomment it
  
  Port 4242
  
  go to line 32
  
  PermitRootLogin no

vim /home/<your42login>/monitoring.sh
  
  #!/bin/bash
  
  echo -n "Architecture: "
  
  uname -a | grep Linux
  
  echo -n "CPU physical: "
  
  cat/proc/cpuinfo | grep "cpu cores" | sed 's/cpu cores : //'
  
  echo -n "vCPU: "
  
  cat /proc/cpuinfo | grep siblings | sed 's/siblings   : //'
  
  echo -n "Memory Usage: "
  
  free | grep Mem | awk '{print $3 / 1000}' | tr '\n ' ' ' && echo -n "MB / " && free | grep Mem | awk '{print $2 / 1000}' | tr '\n' ' ' && echo -n "MB (" && free | grep Mem | awk '{print $3/$2 * 100.0}' | tr '\n ' ' ' && echo "%)"
  
  echo -n "Disk Usage: "
  
  df -hBM | awk '{tot += $2} {use +=$3} END {print use "/" tot "MB {" use/tot*100 "%)"}'
  
  echo -n "CPU load: "
  
  vmstat -SM | awk 'NR ==3 {print 100-$15 "%"}'
  
  echo -n "Last boot: "
  
  last reboot | awk 'NR == 1 {print $5 " " $6 " " $7 " " $8}'
  
  echo -n "LVM usage: "
  
  if lsblk | grep lvm > /dev/null ; then
  
    echo "yes"
  
  else
    
    echo "no"
  
  fi
  
  echo -n "TCP connection(s): "
  
  cat /proc/net/tcp | wc -l | awk '{print $1-1}'
  
  echo -n "User log: "
  
  w | wc -l | awk '{print $1 - 2}'
  
  echo -n "Network: "
  
  ip addr | grep global | awk '{print $2}' | tr '\n' ' ' && echo -n "(" && ip addre | grep ether | awk '{print $2}' | tr '\n' ' ' && echo ")"
  
  echo -n "Sudo: "
  
  cat /var/log/sudo/sudolog | wc -l | tr '\n' ' ' && echo "commands"

crontab -u root -e
  */10 * * * * sh /home/"your42login"/monitoring.sh | wall

vim /etc/motd
  "your welcome message"
