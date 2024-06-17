# Arun's SSL Induction Log

## Prereqs

-Set up AWS instance

-Public ip : ~65.0.7.234~ 13.235.82.67 (Lost the first one cos i set the ip to my own but then my router restarted so its gone lmao)

-Private ip : 172.31.8.134

-No elastic ip so do not turn it off

-keypair saved "ssl-<redacted>.pem"

-keypair protected with "$ chmod 400"

## Setting up VM

-~Accessed VM with "$ ssh -i ./ssl-<redacted>.pem ubuntu@65.0.7.234"~
-~Accessed VM with "$ ssh -i ./ssl-<redacted>.pem ubuntu@13.235.82.67"~

-sudo apt updated and upgraded

# Unattended Packages

-Set up unattended packages with "sudo apt install unattended-upgrades"

-Unattended upgrades keeps the system up to date with the most recent security upgrades

-"$apt-get install unattended-upgrades apt-listchanges"

-"$lsb_release -c" to get the codename " Noble "

-Navigated to "etc/apt/apt.conf.d/50unattended-upgrades"

-Used nano to set up the package lists to ubuntu@noble and turned on settings for :

- "Unattended-Upgrade::Allowed-Origins {"ubuntu:noble-security";};"
- "Unattended-Upgrade::Remove-Unused-Dependencies "true";"
- "Unattended-Upgrade::Automatic-Reboot "true";"
- "Unattended-Upgrade::Allow-APT-Mark-Fallback "true";"
- "Unattended-Upgrade::Skip-Updates-On-Metered-Connections "false";"
- "Unattended-Upgrade::OnlyOnACPower "false";"
- "Unattended-Upgrade::Remove-Unused-Dependencies "true";"
- "Unattended-Upgrade::Remove-New-Unused-Dependencies "true";"
- "Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";"
- "Unattended-Upgrade::AutoFixInterruptedDpkg "true";"

-Navigated to "sudo nano /etc/apt/apt.conf.d/20auto-upgrades"

-Used nano to set up these

- APT::Periodic::Update-Package-Lists "1";
- APT::Periodic::Download-Upgradeable-Packages "1";
- APT::Periodic::AutocleanInterval "7";
- APT::Periodic::Unattended-Upgrade "1";

- The numeric values indicate the number of days between each action

-Activated unattended packages with "sudo dpkg-reconfigure -plow unattended-upgrades" & "sudo systemctl restart unattended-upgrades.service"

# Strengthen SSH

-Went to etc/ssh/sshd_config and set password login and root user login to "no" and public key auth to yes

-Already set up a keypair when i made the aws instance

-Added my linux id_rsa to the servers ssh and turned off all other login points.

-Simply added my key to the authorized file in the .ssh (~/.ssh/authorized_keys)

-Now I can connect with ssh ~ubuntu@65.0.7.234~ ubuntu@13.235.82.67

# fail2ban

-Setup fail2ban, downloaded it from apt and set the max tries to 5 and ban time to 10 mins

- sudo apt install fail2ban
- sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local‍ - to make a copy cos the main conf says not to modify it

- sudo nano /etc/fail2ban/jail.local

- set these setting:

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
findtime = 600
bantime = 3600

- sudo systemctl enable fail2ban && sudo systemctl start fail2ban

- doesnt work wtf

# ufw

- sudo apt-get install ufw - it was already there

- sudo ufw allow 2222/tcp
- sudo ufw allow 80/tcp
- sudo ufw allow 443/tcp
- sudo ufw default deny incoming
- sudo ufw default allow outgoing
- sudo ufw enable

sudo ufw status
Status: active

'''
To Action From

---

2222/tcp ALLOW Anywhere  
80/tcp ALLOW Anywhere  
443/tcp ALLOW Anywhere  
2222/tcp (v6) ALLOW Anywhere (v6)  
80/tcp (v6) ALLOW Anywhere (v6)  
443/tcp (v6) ALLOW Anywhere (v6)  
'''
