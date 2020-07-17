---
published: false
---

## Configure WireGuard Client in our Lightsail machine
Finally, install the config file on your client as /etc/wireguard/wg0.conf and start WireGuard:

# Install the config file to the WireGuard configuration directory on your
# Linux client:
sudo install -o root -g root -m 600 <username>.conf /etc/wireguard/wg0.conf

# Start the WireGuard VPN:
sudo systemctl start wg-quick@wg0

# Check that it started properly:
sudo systemctl status wg-quick@wg0

# Verify the connection to the AlgoVPN:
sudo wg

# See that your client is using the IP address of your AlgoVPN:
curl ipv4.icanhazip.com

# Optionally configure the connection to come up at boot time:
sudo systemctl enable wg-quick@wg0
If your Linux distribution does not use systemd you can bring up WireGuard with sudo wg-quick up wg0.
