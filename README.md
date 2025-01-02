# Zerotier-reverse-proxy-to-a-VPS-Minecraft-Server-
Utilizes a VPS with a public IP address and open ports to reverse proxy to a home server running minecraft. This allows you to run a minecraft server if you don't have a static public IP address or if your ISP is behind CGNAT.

Buy a VPS with a public IP address and supports opening custom ports. I use surfercloud.com
Ensure you are already running a Minecraft server in your home server on port 25565.


1. Install and Configure NGINX on your VPS
--------------------------------

Install NGINX:
sudo apt update
sudo apt install nginx -y

Enable and Start NGINX:
sudo systemctl enable nginx
sudo systemctl start nginx

Test NGINX Installation:
sudo nginx -t


2. Edit the NGINX Configuration
--------------------------------

Open the NGINX Configuration File:
sudo nano /etc/nginx/nginx.conf

Add Stream Block for TCP and UDP Forwarding:
Insert the following configuration under the existing content or replace the default stream section if applicable:

stream {
    # Forward TCP traffic on port 25565
    server {
        listen 25565;
        proxy_pass 10.244.102.165:25565;
    }

    # Forward UDP traffic on port 19132
    server {
        listen 19132 udp;
        proxy_pass 10.244.102.165:19132;
    }
}

Save and Exit:
Press CTRL+O to save, then CTRL+X to exit.


3. Test and Restart NGINX
--------------------------

Test the Updated Configuration:
sudo nginx -t

Restart NGINX:
sudo systemctl restart nginx


4. Set Up ZeroTier on both VPS and Home Server
-------------------

Install ZeroTier:
curl -s https://install.zerotier.com | sudo bash

Join a ZeroTier Network:
Replace <NETWORK_ID> with your ZeroTier network ID.
sudo zerotier-cli join <NETWORK_ID>

Verify ZeroTier Status:
sudo zerotier-cli status

List Peers to Check Connectivity:
sudo zerotier-cli listpeers


5. Configure Firewall Rules in VPS
----------------------------

Allow TCP and UDP Traffic:
sudo ufw allow 25565/tcp
sudo ufw allow 19132/udp
sudo ufw reload

Verify Listening Ports:
sudo netstat -tuln | grep 25565
sudo netstat -tuln | grep 19132


6. Test the Forwarding
-----------------------

Test TCP Port 25565:
telnet localhost 25565

Test UDP Port 19132:
sudo nc -v -u -z localhost 19132
