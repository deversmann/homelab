## NAS Layout

```
tank/                     (Pool)
├── timemachine/          (Dataset - NO SHARE)
│   ├── archive/          (Dataset - SMB: single user)
│   ├── damien/           (Dataset - SMB: single user, quota)
│   └── ...               
├── archive/              (Dataset - SMB: all users)
│   ├── documents/        (Plain Directory)
│   ├── videos/           (Plain Directory)
│   ├── unsorted/         (Plain Directory)
│   └── photos/           (Plain Directory)
├── users/                (Dataset - NO SHARE)
│   ├── damien/           (Dataset - SMB: single user, quota)
│   └── ...               
├── infra/                (Dataset - SMB: all users)
│   ├── isos/             (Dataset - NFS: ro)
│   └── ...               
├── media/                (Dataset - SMB: all users, NFS: ro)
│   ├── shows/            (Plain Directory)
│   ├── music/            (Plain Directory)
│   ├── books/            (Plain Directory)
│   └── movies/           (Plain Directory)
├── retro-gaming/         (Dataset - NO SHARE)
│   ├── games/            (Dataset - SMB: all users, NFS: rw,ro)
│   ├── inbound-staging/  (Dataset - SMB: all users, NFS: rw)
│   └── dat-files/        (Dataset - SMB: all users, NFS: ro)
├── git-server/           (Dataset - NFS: rw)
└── app-backups/          (Dataset - NFS: rw)




```

[↩️ Back to the README](../README.md)
