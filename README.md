# WireHole Easy

WireHole Easy is a simple Docker Compose setup that combines:
- [Wireguard Easy (wg-easy)](https://github.com/WeeJeWel/wg-easy) - WireGuard docker image with web gui
- [Pi-hole](https://github.com/pi-hole/pi-hole) - DNS server that blocks unwanted content with web gui

Also this setup can be [easily multiplied](#run-another-instance) if you need many VPN subnets

---

### Installation
- Passwords can be generated using `cat /dev/random | head -c 30 | base64`
1. Install [Docker](https://docs.docker.com/engine/install/) and [Docker Compose](https://docs.docker.com/compose/install/) somehow
2. Clone repo and cd: `clone https://github.com/AzazKamaz/wirehole-easy.git && cd wirehole-easy`
3. Create `.env` (example is `.env.example`):
   - `DNS_WEB_PASS` - password for Pi-hole web gui
   - `WG_WEB_PASS` - password for WireGuard Easy web gui
   - `WG_HOST` - public ip of the server
   - `WG_PORT` - port on which WireGuard will listen
   - `TZ` (optional) - logs rotation timezone (at midnight) (see [Pi-hole](https://github.com/pi-hole/docker-pi-hole/tree/master#recommended-variables))
4. Run: `sudo docker-compose up -d`

### What is accessible from inside the VPN
- `10.13.37.0/24` - VPN subnet
- `172.23.147.1` - server where Docker is run
- `172.23.147.2:53` - Pi-hole DNS
- http://172.23.147.2:80/admin - Pi-hole web gui
- http://172.23.147.3:51821/ - WireGuard Easy web gui

### Access web gui from ouside the VPN (initial setup)
- `ssh -L 51821:172.23.147.3:51821 $SERVER_IP` - forward port through ssh (then it is accessible at http://localhost:51821/ from where you run ssh)
- http://172.23.147.3:51821/ - from Linux server (Docker contrainers are accessible from host)

### Run another instance
- Example is available in [docker-compose.multiple.yml](docker-compose.multiple.yml)
1. Create another deployment or duplicate `wireguard` service and network in `docker-compose.yml`
2. Edit `docker-compose.yml`:
   - Increment every `172.23.147.x` ip by 8: `172.23.147.3` -> `172.23.147.11`
   - Increment every `fc00:a056:4629:ada0::x` ip by 8: `fc00:a056:4629:ada0::3` -> `fc00:a056:4629:ada0::b` (notice hex numbers)
   - Optional: change VPN subnet address at `WG_DEFAULT_ADDRESS`