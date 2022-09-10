<p align=center>
  <img alt="Project's status" src="https://img.shields.io/badge/Status-Old%20and%20not%20maintained-red">
  <img alt="Project's focus" src="https://img.shields.io/badge/Focus-Virtualization-blue">
</p>

# Born2beRoot

Born2beRoot is a 42 project aiming to create a virtual machine using Virtual Box.

Full subject is available [here](docs/)

Below is the list of required steps to set up the VM

- Download Debian 10 iso [here](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-10.9.0-amd64-netinst.iso)
- Open VirtualBox
- Click "New"
- Set a name
- Select "Linux" type
- Select version "Debian (64-bit)"
- Select "Create a fixed size VDI" with ~5 Go size
- Start the VM you just created
- When prompted, select the iso you just downloaded
- Select time zone / language / keymap...
- Choose appropriate hostname \<your_42_login\>42
- Choose an appropriate root password, for example "Ceciestunbon42"
- Chose a login for your new user, for example \<your_42_login\>
- Choose an appropriate user password, for example "Ceciestunbon42"
- Select LVM encryption with separated /home folder
- Choose an appropriate disk passphrase, for example "Ceciestunbon42"
- Choose not to use http proxy
- Select softwares SSH and standard system utilities
- Install GRUB to /dev/sda
- Log in to your VM as root

Below is the list of commands used to configure the VM

- `apt install sudo`
- `adduser root sudo`
- `adduser <your_42_login> sudo`
- `addgroup user42`
- `adduser <your_42_login> user42`
- `touch /var/log/sudo/sudolog`
- `apt install vim`
- `vim /etc/sudoers.d/sudoconf`
- Type this in

  ```shell
    Defaults passwd_tries=3
    Defaults badpass_message="!NO! Try <password hint>"
    Defaults logfile="/var/log/sudo/sudolog"
    Defaults requiretty
    Defaults secure_path="/sur/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/snap/bin"
  ```

- `apt install ufw`
- `ufw enable`
- `ufw allow 4242`
- `vim /etc/login.defs`
- Type this in

  ```shell
  :se nu
  go to line 160
  PASS_MAX_DAYS 30
  PASS_MIN_DAYS 2
  PASS_WARN_AGE 7
  ```

- `apt install libpam-pwquality`
- `vim /etc/pam.d/common-password`
- Type this in
  
  ```shell
  :se nu
  goto line 25 and add at the end of the line
  minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
  ```

- `vim /etc/ssh/sshd_config`
- Type this in

  ```shell
  :se nu
  go to line 13 and uncomment it
  Port 4242
  go to line 32
  PermitRootLogin no
  ```

- `vim /home/<your_42_login>/monitoring.sh`
- Type this in

  ```shell
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
  ```

- `crontab -u root -e`
- Type this in

  ```shell
  */10 * * * * sh /home/<your_42_login>/monitoring.sh | wall
  ```

- `vim /etc/motd`
- Type this in
  
  ```shell
  <your_welcome_message>
  ```

### And you're done !
