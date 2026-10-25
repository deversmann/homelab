# NAS Layout

```

tank/                     (Pool)
|-- timemachine/          (Dataset - NO SHARE)
│   |-- archive/          (Dataset - SMB: single user)
│   |-- damien/           (Dataset - SMB: single user, quota)
│   `-- ...               (- One for each Mac)
|-- archive/              (Dataset - SMB: all users)
│   |-- documents/        (Plain Directory)
│   |-- videos/           (Plain Directory)
│   |-- unsorted/         (Plain Directory)
│   `-- photos/           (Plain Directory)
|-- users/                (Dataset - NO SHARE)
│   |-- damien/           (Dataset - SMB: single user, quota)
│   `-- ...               (- One for each user)
|-- infra/                (Dataset - SMB: all users)
│   |-- isos/             (Dataset - NFS: ro)
│   `-- ...               (- More infrastreucture items, e.g. terraform/ansible docs, 
│                           vm templates, container registry, consolidated logs, etc.)
|-- media/                (Dataset - SMB: all users, NFS: ro)
│   |-- shows/            (Plain Directory)
│   |-- music/            (Plain Directory)
│   |-- books/            (Plain Directory)
│   `-- movies/           (Plain Directory)
|-- retro-gaming/         (Dataset - NO SHARE)
│   |-- games/            (Dataset - SMB: all users, NFS: rw,ro)
│   |-- inbound-staging/  (Dataset - SMB: all users, NFS: rw)
│   `-- dat-files/        (Dataset - SMB: all users, NFS: ro)
|-- git-server/           (Dataset - NFS: rw)
`-- app-backups/          (Dataset - NFS: rw)

```

---

# Output from AI discussions:

# TrueNAS SCALE Storage & Permissions Architecture

## System Environment Baseline
* **TrueNAS SCALE Version:** 26.0.0-BETA.3
* **Directory Services:** None (Local User Management Only)
* **Access Requirements:** Hybrid Dual-Protocol (macOS via SMB, RHEL 10.2 via NFSv3/v4 with SYS security)
* **Application Isolation Strategy:** Clean separation of compute and storage. Zero applications or containers run directly on the TrueNAS instance. All containers run on an external RHEL application server managed via Podman and Quadlets.
* **Core Identity Sync Requirement:** Because NFS utilizes numerical IDs rather than usernames, local UIDs/GIDs must be completely synchronized between the TrueNAS local database and the RHEL client hosts.
  * **Human Operations Group:** nas-users (GID: 5000, flagged as an SMB Group)
  * **Automated Curation / Application Group:** external-apps (GID matching the RHEL service account container runtime: e.g., 1005)

---

## 1. ZFS Pool & Dataset Layout

```text
tank (ZFS Pool)
├── users (Parent Dataset - Generic, POSIX 710)
│   ├── damien (Child Dataset - SMB Preset, NFSv4 ACL)
│   ├── zachary (Child Dataset - SMB Preset, NFSv4 ACL)
│   ├── deacon (Child Dataset - SMB Preset, NFSv4 ACL)
│   └── amanda (Child Dataset - SMB Preset, NFSv4 ACL)
├── app-backups (Dataset - Generic Preset, POSIX 700)
├── media (Dataset - SMB Preset, NFSv4 ACL)
│   ├── tvshows/
│   ├── movies/
│   ├── music/
│   └── books/
└── retro-gaming (Parent Dataset - Generic, POSIX 710)
    ├── games (Child Dataset - SMB Preset, NFSv4 ACL)
    │   ├── main-archive/  <--- True source of truth
    │   └── arrangements/  <--- Target for hard-links (RomM, handheld layouts)
    ├── inbound-staging (Child Dataset - SMB Preset)
    └── dat-files (Child Dataset - SMB Preset)
```

---

## 2. Share & Protocol Matrix

| Dataset Path | Protocol | Share Name | Purpose / Security Settings | Client Access Level |
| :--- | :--- | :--- | :--- | :--- |
| /mnt/tank/users/<username> | SMB | <username> | Multi-Protocol Share purpose. Inherits custom user-private restrictions. | Read-Write (Owner Only) |
| /mnt/tank/users/<username> | NFS | NFS Export Path | SYS Security. Restricted via explicit client IPs. | Read-Write (Matching UID Only) |
| /mnt/tank/app-backups | NFS | app-backups | SYS Security. Maproot User/Group = root. Firewall restricted to App Server IP. | Read-Write (RHEL root automation) |
| /mnt/tank/media | SMB | media | Multi-Protocol Share purpose. Optimized to yield cross-protocol locks. | Read-Write (nas-users group) |
| /mnt/tank/media | NFS | media-ro | Protocol Firewall: Enforced Read-Only share configuration. | Read-Only (RHEL Media App Containers) |
| /mnt/tank/retro-gaming/games | SMB | games | Multi-Protocol Share purpose. Browsing and manual card syncing. | Read-Write (nas-users group) |
| /mnt/tank/retro-gaming/games | NFS | games-rw | SYS Security. Maproot = Blank. Essential for hard-link engine. | Read-Write (external-apps group) |
| /mnt/tank/retro-gaming/games/arrangements/romm-layout | NFS | romm-media | Protocol Firewall: Enforced Read-Only share configuration. | Read-Only (RomM backend container) |

---

## 3. Detailed Permission Configurations

### Individual User Directories (`users/<username>`)
* Underlying Engine: NFSv4 ACL via SMB Dataset Preset.
* Parent Dataset (users): To avoid access errors while preserving structural isolation, configure the parent dataset /mnt/tank/users via the Unix Permissions Editor as POSIX 710 (Owner root:rwx, Group nas-users:--x, Other: ---). This allows individual users to securely pass (traverse) through the parent directory without the ability to view, list, or access other users' folders.
* Child ACL: Owner set to the specific user account with Full Control. The default "Everyone" entry is completely stripped out to ensure total user privacy.

### Automated Application Backup Landing Zone (`app-backups`)
* Underlying Engine: Pure POSIX via Generic Dataset Preset.
* Dataset Ownership: Explicitly set to root:root with a permission grid of 700 (rwx------).
* NFS Security Override: By setting Maproot User = root, automated backup cron tabs or systemd timers executing as root on your RHEL node pass their native authorization straight through the NFS connection. This allows your script to store system configs, databases, and application states while fully preserving original Linux UIDs/GIDs (e.g., nginx, postgres) for disaster recovery fidelity.

### Multi-Protocol Cross-Over Media Library (`media`)
* Underlying Engine: NFSv4 ACL via SMB Dataset Preset.
* Share Logic: Human managers upload files over SMB. External web applications parse them via NFS. The SMB share purpose must be set to Multi-Protocol Share to prevent opportunistic locking (oplocks), eliminating file-locking conflicts between macOS clients and Linux apps.
* Advanced ACL Parameters: Human groups (nas-users) receive standard Read/Write privileges. The group identifier running your RHEL containers (external-apps) must be added as an explicit item via Advanced ACL mapping, granting: Read Data, Traverse Folder, Read Attributes, Read Extended Attributes, and Read ACL with the Inherit flag enabled.

### The High-Performance Retro Gaming Complex (`retro-gaming`)
* Underlying Engine: NFSv4 ACL via SMB Dataset Preset inside a unified child dataset layout.
* The Hard-Link Golden Rule: ZFS completely forbids hard-links from crossing dataset filesystem boundaries. Therefore, main-archive/ and all target storefront/handheld arrangements/ must exist inside the same child dataset (games).
* The Multi-Protocol Setup Rule: Hard-links cannot be reliably created over standard network SMB protocol layers from macOS. To successfully deploy zero-space, instantaneous arrangements, the raw games dataset must be exposed over both protocols. Curation scripts and links are executed natively over the NFS write-path on the RHEL server, while macOS retains SMB access for high-level management and asset injection.

---

## 4. Automation & Data Protection Blueprint

### Dynamic Client Deployment
To maximize server stability and avoid hard locks if the TrueNAS storage system undergoes a maintenance cycle or reboot, all long-term dependencies should be handled via dynamic mount wrappers rather than static /etc/fstab entries on the application servers.

Example RHEL On-Demand Shell Mount Automation Wrapper:
```bash
#!/bin/bash
TARGET_MNT="/mnt/truenas_backups"

# 1. Gracefully shut down active production environments
podman stop my-database-container my-web-app

# 2. Mount the secure backup landing pad over NFS on-demand
mkdir -p "\$TARGET_MNT"
mount -t nfs -o rw,soft,intr,timeo=15,retrans=2 truenas.local:/mnt/tank/app-backups "\$TARGET_MNT"

# 3. Synchronize cold storage backups with metadata preservation
rsync -aHAXx --delete /var/lib/my-app-data/ "\$TARGET_MNT/my-app-backup/"

# 4. Tear down the network block cleanly
umount "\$TARGET_MNT"

# 5. Bring services back online safely
podman start my-database-container my-web-app
```

### Recursive Backup & Snapshot Topologies
Because you have organized your files hierarchically, you can use automated, snapshot schedules to manage data protection efficiently:
1. The Infrastructure Snapshot Profile (infra/): Set aggressive, short-term rotation cycles (e.g., hourly snapshots kept for 48 hours, daily snapshots kept for 7 days). This provides instant protection against broken DNS leases or botched infrastructure automation changes.
2. The Golden Archive Profile (retro-gaming/): Set a lower-frequency, long-term rotation cycle (e.g., weekly snapshots kept for 6 months). Because the dataset relies entirely on ZFS hard-links for multiple frontend profiles, rolling back a single snapshot instantly restores the exact state of your verified collections, DAT logs, and symlinks without altering raw user home environments.

---

## 5. Blueprint for Future LDAP Directory Service Migration
Should you decide to scale out to centralized directory management (LDAP) down the road, your storage is pre-staged to handle it safely with minimal friction:
* The Transition Task: TrueNAS will cleanly connect to your directory backend via a simple configuration toggle.
* The Zero-Friction UID Policy: Because your local users and your custom nas-users and external-apps groups were mapped using standard, distinct high-numerical block ranges from day one, you can mirror these identical UID/GID sets directly inside your LDAP schema.
* No chown Required: When the system switches to LDAP authentication, incoming connection IDs will match existing ZFS file system identities exactly. Your data pool remains completely untouched, and your cross-protocol permissions will continue to evaluate flawlessly.



[↩️ Back to the README](../README.md)

