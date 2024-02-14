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
- Gitea (maybe with clones/backups of my GitHub repos)

### Setup Notes:
[Moved here](setup_notes/overview.md)

### ToDos
- [ ] set up alerts in Uptime-Kuma
- [ ] build ansible roles and playbooks
  - [ ] setup
  - [ ] startup
  - [ ] shutdown
  - [ ] backup
- [ ] Build lab diagram
- [ ] podman secrets for passwords
- [ ] Internal DNS (including actual domain)

---

### References
- https://wiki.craftycontrol.com/en/4/docs/API%20V2
- https://www.procustodibus.com/blog/2022/09/wireguard-port-forward-from-internet/
- https://github.com/mochman/Bypass_CGNAT
- https://crates.io/crates/podlet
- https://www.redhat.com/sysadmin/multi-container-application-podman-quadlet
- https://www.redhat.com/sysadmin/podman-kubernetes-secrets
- https://serverfault.com/questions/1101002/wireguard-client-addition-without-restart