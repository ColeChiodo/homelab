# Tailscale

## Installation
```
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

Then Login

### On Any Server:
If you want your laptop/phone to access other devices at home (printer, NAS, router UI, etc.), make your server a subnet router:
```
sudo tailscale up --advertise-routes=192.168.1.0/24
```
Then approve the route in the Tailscale admin console.
