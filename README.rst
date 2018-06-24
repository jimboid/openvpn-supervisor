Openvpn Supervisor
******************

A simple Linux based supervisor script to monitor the OpenVPN client for stale
connections. This tool is designed to work in conjunction with a firewall based
kill switch such as iptables or ufw, in principle as long as you have a way to 
prevent traffic from going out if the VPN disconnects then this tool will work.

Kill Switch Preparation
=======================

You should download and install the OpenVPN client and configure it such that it
connects to a server locale from your chosen VPN provider. It should
also be set to connect after reboot. Once you have this done do the following.

You should then set up a VPN killswitch using one of the Linux based firewalls.
An example of doing this with ufw on ubuntu 18 can be found below, your mileage
may vary based on the flavour of Linux you have, but you should be able to find
a guide somewhere:

Install ufw

    sudo apt install ufw
    
Set default to deny all incoming and outgoing

    sudo ufw default deny incoming
    
    sudo ufw default deny outgoing

Allow ssh, DNS and OpenVPN

    sudo ufw allow 22 comment ssh
    
    sudo ufw allow out 53 comment DNS
    
    sudo ufw allow out 1198 comment OpenVPN

Allow all traffic over VPN (you'll need to find your VPN tunnel interface, mine
is tun0), if you want more restrictive outgoing rules then make sure ping can
go out on the VPN interface but not on the unsecured connection.

    sudo ufw allow out on tun0 comment openvpn
 
 
Installation of Supervisor
==========================

Download the latest version from github and extract it.

    wget https://github.com/jimboid/openvpn-supervisor/archive/master.zip -O openvpn-supervisor.zip

    unzip openvpn-supervisor.zip
    
Move it to the /opt directory.

    sudo mv openvpn-supervisor-master/ /opt/openvpn-supervisor

Make root own all files to avoid permission problems.

    sudo chown -R root:root /opt/openvpn-supervisor
    
Move the systemd file into one of the system directories.

    sudo mv /opt/openvpn-supervisor/openvpn-supervisor.service /lib/systemd/system/

Reload the daemon and start the openvpn-supervisor

    sudo systemctl daemon-reload
    
    sudo systemctl enable openvpn-supervisor.service
    
    sudo systemctl start openvpn-supervisor.service
    
and that should be it. You should find that the openvpn supervisor is logging to
/var/log/openvpn-supervisor/openvpn-supervisor.log 

Hopefully events should be pretty rare and keep the log size down, in future, log
rotation will be implemented.
