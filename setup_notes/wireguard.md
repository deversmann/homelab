```mermaid
flowchart TD
    iCli[Internet Client]
    subgraph pubSrv[Public Server]
        wg0-host((wg0)):::service
    end
    subgraph prvNet[Private Network]
        direction LR
        subgraph prvSrv[Private Server]
            direction TB
            wg0-node((wg0)):::service
            revProx{{Reverse \nProxy \nContainer}}
            services{{Other \nContainerized \nServices}}
        end
        othSrv[[Other Servers]]
    end
    class prvNet network
    iCli --> pubSrv
    wg0-node -.Wireguard \nTunnel.-> wg0-host
    wg0-node <--> revProx
    revProx <--> othSrv
    revProx <--> services

    classDef default fill: #666, stroke: #77e, stroke-width: 2px, color: #ccc
    classDef network fill: transparent, stroke: #77e, stroke-width: 2px, color: #ccc, stroke-dasharray: 10  5, stroke-width: 2px
    classDef service fill: #ffa, color: #666

```


- Setup container host (private)
  - Install OS
  - Firewall
    - open ssh
    - open app ports (if wanting to access internally)
  - Install Wireguard
  - Configure WG
    - Generate WG keys
    - Copy VPS public key
  - Enable WG Service
  - Start WG Service
- Setup VPS (public)
  - Install OS
  - Firewall
    - open ssh
    - open wireguard (i.e. - 51820)
    - open app ports
  - Install Wireguard
  - Configure WG
    - Generate WG keys
    - Copy container host public key
    - Setup create/remove iptables forwards for each port (in PreUp/PostDown clauses)
    - Setup activate/deactivate iptables masquerading (in PreUp/PostDown clauses)
  - Enable WG Service
  - Start WG Service



### On private server
#### Shell commands (as root):
```console
dnf install wireguard-tools
umask 077 && printf "[Interface]\nPrivateKey = " | tee /etc/wireguard/wg0.conf > /dev/null
wg genkey | tee -a /etc/wireguard/wg0.conf | wg pubkey | tee /etc/wireguard/publickey
vi /etc/wireguard/wg0.conf
systemctl start wg-quick@wg0
systemctl enable wg-quick@wg0
ping 10.9.8.1
```
#### in `/etc/wireguard/wg0.conf`
```
[Interface]
PrivateKey = +PK2Nx8S/5KSuHhPRTLhyyoTQ+5/FDZuDPED8x2btFs=
Address = 10.9.8.2/24

[Peer]
PublicKey = MlTBm89jiVG13QdCcS6yr/KkeKMJw3JVKcgRNuomU1U=
AllowedIPs = 10.9.8.0/24
Endpoint = 192.168.50.80:51820
PersistentKeepalive = 25
```

### On public server:
#### Shell commands (as root):
```console
dnf install wireguard-tools
umask 077 && printf "[Interface]\nPrivateKey = " | sudo tee /etc/wireguard/wg0.conf > /dev/null
sudo wg genkey | sudo tee -a /etc/wireguard/wg0.conf | wg pubkey | sudo tee /etc/wireguard/publickey
vi /etc/wireguard/wg0.conf
systemctl start wg-quick@wg0
firewall-cmd --add-port=51820/udp --permanent
firewall-cmd --reload
systemctl enable wg-quick@wg0
ping 10.9.8.2
```
#### in `/etc/wireguard/wg0.conf`
```
[Interface]
PrivateKey = mKGa9VLa8J2/VNuSLEIuwY2/wdLw4Z2qn2rhdtCvC18=
ListenPort = 51820
Address = 10.9.8.1/24

[Peer]
PublicKey = dqcNZBA2gEU9e0n4J9zfzXqwQemcw0QR6JM+bu1RKG0=
AllowedIPS = 10.9.8.2/32
```
