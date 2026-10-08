## NAS Layout

``` text
tank/                     (Pool)
├── mac-backups/          (Dataset - Generic preset, NO SHARE)
│   ├── amanda/           (Dataset - Generic preset, SMB as TimeMachine)
│   ├── damien/           (Dataset - Generic preset, SMB as TimeMachine)
│   ├── deacon/           (Dataset - Generic preset, SMB as TimeMachine)
│   └── zachary/          (Dataset - Generic preset, SMB as TimeMachine)
├── app-backups/          (Dataset - Generic preset, NFS)
├── personal-homes/       (Dataset - SMB preset, SMB as TrueNAS Home Share)
│   ├── damien/           (Directory - auto-created on 1st login)
│   └── ...
├── media/                (Dataset - Generic preset, NFS and SMB)
│   ├── books/            (Directory)
│   ├── movies/           (Directory)
│   ├── music/            (Directory)
│   └── shows/            (Directory)
├── archive/              (Dataset - Generic preset, SMB)
│   ├── documents/        (Directory)
│   ├── photos/           (Directory)
│   └── videos/           (Directory)
├── git-server/           (Dataset - Generic preset, NFS)
└── games/                (Dataset - Generic preset, SMB and NFS)
    ├── dat-files/        (Directory)
    ├── inbound-staging/  (Dataset - Generic preset, SMB)
    └── roms/             (Dataset - Generic preset, SMB and NFS)
        ├── bios/         (Directory)
        ├── metadata/     (Directory)
        │   ├── boxart/   (Directory)
        │   ├── manuals/  (Directory)
        │   └── ...
        ├── atari2600/    (Directory)
        ├── gba/          (Directory)
        ├── gbc/          (Directory)
        └── ...

```


[↩️ Back to the README](../README.md)
