# Arun's SSL Induction Log

## Prereqs

-Set up AWS instance

-Public ip : ~65.0.7.234~ ~13.235.82.67 (Lost the first one cos i set the ip to my own but then my router restarted so its gone lmao) ~ 4.240.104.178 switches to azure cos aws dumb
no arunnats@arun.ssl.airno.de

-Private ip : redacted

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

-Navigated to " "

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

-added ssl's key
"ssh-rsa AAAAB3NzaC1yc2EAAAASSSSSSSSSSSfgjmclM0fbj/K/NYqNcfNovc0GDztI+LQwGuP6Z+P8XXXXXXXXXXXXXXXXXkpfRp8vu8oh8epvqFICCCCCCCCCCCCCCCCCCCCCCCCCcserver"

# fail2ban

-Setup fail2ban, downloaded it from apt and set the max tries to 5 and ban time to 10 mins

- sudo apt install fail2ban
- sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local‍ - to make a copy cos the main conf says not to modify it

- sudo nano /etc/fail2ban/jail.local

- set these setting:

[default]
maxretry = 3
findtime = 600
bantime = 3600

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log

- sudo systemctl enable fail2ban && sudo systemctl start fail2ban

- doesnt work wtf

- [BR]: ERROR No module named 'asynchat' on Ubuntu 24.04

- ubuntu 24.04 doesnt support the newest version of python, found a script to fix it on github

-ran script (https://github.com/alexdcodes/ConfigureUbuntu/blob/main/fail2ban/install.sh)
-worked

-made above changes

-should work now

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

- but this stops me from ssh-ing in
- so sudo ufw allow ssh

-works

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

## ufw logging

-sudo ufw logging medium
-sudo ufw status verbose

- didnt work cos my whole logging this had an error so i "sudo service rsyslog restart" and it works

# Sidequest

- ssh was being a nuisance so i did sudo apt install openssh
- kinda works now but had to go and redo the whole ssh config

- tried to set up zsh cos i hate bash but chsh asks for a password which then says pam auth failed. gotta ask around

- pam is prolly the reason i cant chsh i gotta fix that

- tried changind the chsh file in pam.d but didnt work

- ended up just adding zsh to the end of bashrc (lifehax) and figlet for a nice welcome message

- got oh my zsh and copied some of my plugins over. life is better now

# Google 2fa

- i tried this on the other vm i made before i got locked out and it asked for password even after verification code, tried again here

- sudo apt-get install libpam-google-authenticator

- ran google-authenticator and set it up with the qr code and the app

-added teh following to /etc/pam.d/sshd
'''
auth required pam_google_authenticator.so nullok
auth required pam_permit.so
'''

- added "ChallengeResponseAuthentication yes" and to etc/ssh/sshd_config and restarted ssh

- added "AuthenticationMethods publickey,password publickey,keyboard-interactive" to the ssd_config

- commented out the following in the pam.d config

'''

Standard Un\*x authentication.

#@include common-auth
'''

- it works now :)

# Set up Suricata IDS

(https://www.digitalocean.com/community/tutorials/how-to-install-suricata-on-ubuntu-20-04#step-2-configuring-suricata-for-the-first-time)

- Wanted to try Snort but it wanted me to sign up i think

- Installed suricata

- Used the tgreen/hunting ruleset for configuring it

- Tested it with curl http://testmynids.org/uid/index.html

- got the correct output - it works

- added it to the startup scripts

# make the accounts

sudo adduser exam_1 - password : exam1
sudo adduser exam_2 - password : exam2
sudo adduser exam_3 - password : exam3
sudo adduser examadmin - password : examadmin
sudo adduser examaudit - password : examaudit

sudo usermod -aG sudo examadmin - made it super user

sudo groupadd home_audit
sudo usermod -aG home_audit examaudit - added audit to his group

sudo chown exam_1:home_audit /home/exam_1
sudo chmod 750 /home/exam_1
sudo chown exam_1:home_audit /home/exam_1
sudo chmod 750 /home/exam_1
sudo chown exam_1:home_audit /home/exam_1
sudo chmod 750 /home/exam_1

sudo usermod -aG home_audit examadmin - to add examadmin to the group

ls -ld /home/exam_1 /home/exam_2 /home/exam_3 to check if its all good

essentially ive made it so that " " ADD HERE

# ssh access into these accounts

so far im using $sudo su - account name but i want to be able to ssh into it directly

- need to generate keys for each account

from local computer
$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/exam_1_rsa - $

from root user
$ sudo mkdir -p /home/exam_1/.ssh

from local computer
$ scp -i redacted.pem ~/.ssh/exam_1_rsa.pub ubuntu@13.235.82.67:/home/ubuntu/exam_1_rsa.pub $

from root user
$ sudo mv /home/ubuntu/exam_1_rsa.pub /home/exam_1/.ssh/authorized_keys

this works now "ssh -i ~/.ssh/exam_1_rsa exam_1@13.235.82.67" but i'll add my local pc's key to them all for east access

from local computer
$ scp -i path_to_your_aws_key.pem ~/.ssh/id_rsa.pub ubuntu@13.235.82.67:/home/ubuntu/id_rsa.pub $

from root user

$sudo mv /home/ubuntu/id_rsa.pub /home/exam_1/.ssh/temp_id_rsa.pub

sudo sh -c 'cat /home/exam_1/.ssh/temp_id_rsa.pub >> /home/exam_1/.ssh/authorized_keys'

sudo chmod 600 /home/exam_1/.ssh/authorized_keys
sudo chown -R exam_1:exam_1 /home/exam_1/.ssh $

sudo rm /home/exam_1/.ssh/temp_id_rsa.pub
Instead of doing this for the rest, imma jsut make a script to automate this

nvm script was risky, time to run these manually

-worksm i can use ssh username@13.235.82.67 and all the permissions work as instructed

## directory safetly

ran "sudo chmod 700 /home/exam_1/.ssh $" from the root - works, exam_1 can only access exam 1

- ok this broke my vm, fucked over my entire progress and i have to start again i hate this so much. might switch to azure next time

NEW LOG

lost the nice domain
made ssl.arunnats.com temporarily
so ssh arunants@ssl.arunnats.com

Im starting from teh user thing

sudo adduser exam_1
sudo adduser exam_2
sudo adduser exam_3

sudo adduser examadmin
sudo usermod -aG sudo examadmin

sudo adduser examaudit

man SCREW THIS
did a buncha random stuff
lcoked root out of /home somehow
idk why this shit is so complicated
bun wont work
cos fuck bun thats why
whu cant I, as a super user, use bun? wtf

ok wait

ATTEMPT 6- final attempt
cos when i set it up, upto ngnix last time i ran outta azure creds rip and got locked out

back on aws bois : ubuntu 22.04

-Public ip : 13.233.113.5
-protected private key with chmod 600
-Set up domain: ssl.arunnats.com

-added my public key and the ssl public key to the authorized files

-sudo apt update
-sudo apt upgrade

-it did a kernal update & asked me to reboot

-not able to ssl into the vm after that, insert skumm emoji

ATTEMPT 7- apparently 6 failed vms werent enought to knock sense into me fml

-Public ip : 13.127.128.77
-protected private key with chmod 600
-Set up domain: ssl.arunnats.com

-added my public key and the ssl public key to the authorized files

-sudo apt update
-sudo apt upgrade

-installed zsh, cowsay and set up omz with some nice plugins

sudo apt install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting

added to plugins in .zshrc

# Strengthen SSH

-Went to etc/ssh/sshd_config and set password login and root user login to "no" and public key auth to yes

# fail2ban

wget -O fail2ban.deb https://github.com/fail2ban/fail2ban/releases/download/1.1.0/fail2ban_1.1.0-1.upstream1_all.deb
wget -O fail2ban.deb.asc https://github.com/fail2ban/fail2ban/releases/download/1.1.0/fail2ban_1.1.0-1.upstream1_all.deb.asc

gpg --verify fail2ban.deb.asc fail2ban.deb
dpkg -I fail2ban.deb
sudo service fail2ban stop
sudo dpkg -i fail2ban.deb
sudo apt -f install

sudo systemctl start fail2ban
sudo systemctl status fail2ban

- sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local‍ - to make a copy cos the main conf says not to modify it

- sudo nano /etc/fail2ban/jail.local

- set these setting:

[default]
maxretry = 3
findtime = 600
bantime = 3600

[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log

# ufw

- sudo apt-get install ufw - it was already there

- sudo ufw allow 2222/tcp
- sudo ufw allow 80/tcp
- sudo ufw allow 443/tcp
- sudo ufw default deny incoming
- sudo ufw default allow outgoing
- sudo ufw allow ssh
- sudo ufw enable

sudo ufw status
Status: active

-sudo ufw logging medium
-sudo ufw status verbose

# IDS - Suricata

-sudo add-apt-repository ppa:oisf/suricata-stable
-sudo apt install suricata
-sudo systemctl enable suricata.service
-sudo systemctl stop suricata.service
-ip -p -j route show default -> to get the network interface
-sudo nano /etc/suricata/suricata.yaml

updated the network interface somewhere along line 590
add
'''

detect-engine:

- rule-reload: true

'''
to the yaml

-sudo kill -usr2 $(pidof suricata) to restart suricata

-sudo suricata-update - to get the rulesets
-sudo suricata-update list-sources
-sudo suricata-update enable-source tgreen/hunting
-sudo suricata -T -c /etc/suricata/suricata.yaml -v
-sudo systemctl start suricata.service
-sudo systemctl status suricata.service

to validate i used testmynids

-curl http://testmynids.org/uid/index.html

# app time

-sudo apt install nginx
-sudo adduser appuser
-sudo chmod 755 /home/appuser

-cd /home/appuser
-sudo -u appuser wget https://do.edvinbasil.com/ssl/app -O app1
-sudo -u appuser wget https://do.edvinbasil.com/ssl/app.sha256.sig

verified signatures with

sha256sum app1
cat app.sha256.sig

sudo -u appuser git clone https://gitlab.com/tellmeY/issslopen.git
cd issslopen
sudo -u appuser npm install
sudo -u appuser npm start &

for the issslopen
-sudo apt install nodejs
-node -v
-sudo apt install npm

-sudo -u appuser git clone https://gitlab.com/tellmeY/issslopen.git
-cd issslopen
-cp .env.example .env
-added my name uwu
-npm install - needs to be sudo for some reason

-node index.ts - didnt work
-node run index.ts- didnt work

-installed bun on snap

-bun run - apparently the package.json wasnt able to be run cso permission issue :(

-npm install -g typescript ts-node

-gay it can only use bun cos its configured that way

-sudo apt install -y docker.io docker-compose
-sudo usermod -aG docker $USER
-newgrp docker

-didnt work

ran these

sudo apt update
sudo apt install -y ca-certificates curl gnupg

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
 "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER
newgrp docker

sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

then recloned repo and ran
docker-compose up -d --build

-docker-compose pull && docker-compose up -d
idk it says it started, i trust vysakhettan

-it worked

-set up nginx with the reverse proxy

-then set up the nginx config

-doesntn work??

-turns out aws doesnt let you assign a domain without using a differnet subservice (costs moneys)

-works kinda with the vysakhettan app
