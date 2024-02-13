``` mermaid
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
            revProx{{Reverse 
                    Proxy 
                    Container}}
            services{{Other
                    Containerized
                    Services}}
        end
        othSrv[[Other Servers]]
    end
    class prvNet network


    iCli <--> pubSrv
    wg0-node -. Wireguard
                Tunnel .-> wg0-host
    wg0-node <--> revProx
    revProx <--> othSrv
    revProx <--> services

    classDef default fill: #666, stroke: #77e, stroke-width: 2px, color: #ccc
    classDef network fill: #666, stroke: #77e, stroke-width: 2px, color: #ccc, stroke-dasharray: 10  5, stroke-width: 2px
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
