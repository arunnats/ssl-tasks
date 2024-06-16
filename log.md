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
-Set up unattended packages with "sudo apt install unattended-upgrades"
-Unattended upgrades just keeps the system up to date with the most recent security upgrades
-"$apt-get install unattended-upgrades apt-listchanges"

-
