# Arun's SSL Induction Log

## Prereqs

-Set up AWS instance
-Public ip : 65.0.7.234
-Private ip : 172.31.8.134
-No elastic ip so do not turn it off
-keypair saved "ssl-<redacted>.pem"
-keypair protected with "$ chmod 400"

## Setting up VM

-Accessed VM with "$ ssh -i ./ssl-<redacted>.pem ubuntu@65.0.7.234"
-sudo apt updated and upgraded

# Unattended Packages

-Set up unattended packages with "sudo apt install unattended-upgrades"
-Unattended upgrades just keeps the system up to date with the most recent security upgrades
-"$apt-get install unattended-upgrades apt-listchanges"
-"$lsb_release -c" to get the codename " Noble "
-Navigated to "etc/apt/apt.conf.d/50unattended-upgrades"
-Used nano to set up the package lists to ubuntu@noble and turne on setting
-Activated unattended packages with "sudo dpkg-reconfigure -plow unattended-upgrades" & "sudo systemctl restart unattended-upgrades"

# Strengthen SSH

-Went to etc/ssh/sshd_config and set password login and root user login to "no" and public key auth to yes
-Already set up a keypair when i made the aws instance
-Added my linux id_rsa to the servers ssh and turned off all other login points.
-Simply added my key to the authorized file in the .ssh
-Now I can connect with ssh ubuntu@65.0.7.234
