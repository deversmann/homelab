# Homelab

This is my repository for my Homelab escapades.  For now, it's mostly for me to store/backup stuff and keep some notes.  Maybe it'll become useful for teaching and learning for others eventually.

### Current Services:
- RHEL Box with KVM, Podman and Cockpit
- Crafty with 3 Minecraft servers
- PiHole
- Uptime Kuma
- VPS with WireGuard Tunnel
- Postgresql db (maybe switching to MariaDB or a db install per app in containers or pods)
- Semaphore (testing still)
- Homepage dashboard
- TrueNAS

### Planned Services:
- Redundant PiHole
- Reverse Proxy (Nginx Proxy Manager probably)

### Workflow:

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


### ToDos
- [ ] set up alerts in Uptime-Kuma
- [ ] build ansible roles and playbooks
  - [ ] setup
  - [ ] startup
  - [ ] shutdown
  - [ ] backup
- [ ] Build lab diagram


---

### References
- https://wiki.craftycontrol.com/en/4/docs/API%20V2
- https://www.procustodibus.com/blog/2022/09/wireguard-port-forward-from-internet/
- https://github.com/mochman/Bypass_CGNAT