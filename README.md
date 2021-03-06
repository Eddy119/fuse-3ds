# fuse-3ds
FUSE Filesystem Python scripts for Nintendo 3DS files

ARM9 bootROM required. Checked in order of:
* `boot9.bin` (full) in current working directory
* `boot9_prot.bin` (protected) in current working directory
* `~/.3ds/boot9.bin` (full)
* `~/.3ds/boot9_prot.bin` (protected)

Requires Python 3.5+, [a fork of fusepy](https://github.com/billziss-gh/fusepy), and [pycryptodomex](https://github.com/Legrandin/pycryptodome).

Install the fusepy fork with `pip3 install git+https://github.com/billziss-gh/fusepy.git`. Make sure pip is installed, since it doesn't seem to be always included on Windows.

* macOS: [Fuse for macOS](https://osxfuse.github.io)
* Windows: [WinFsp](http://www.secfs.net/winfsp/) - Requires [WinFsp 2017.2 B1](https://github.com/billziss-gh/winfsp/releases/tag/v1.2B1) or later. WinFsp has issues with certain antivirus software like [Avast](https://github.com/billziss-gh/winfsp/issues/97).
* Linux: Most distributions should have fuse included. Use your package manager. **Decryption seems to fail at random parts - trying to figure this out**

## mount_cia.py
Mounts CTR Importable Archive (CIA) files, creating a virtual filesystem of decrypted contents (if encrypted) + Ticket, Title Metadata, and Meta region (if exists).

DLC with missing contents is currently not supported.

```
usage: mount_cia.py [-h] [--dev] [--fg] [--do] [-o OPTIONS] cia mount_point

Mount Nintendo 3DS CTR Importable Archive files.

positional arguments:
  cia          CIA file
  mount_point  mount point

optional arguments:
  -h, --help   show this help message and exit
  --dev        use dev keys
  --fg, -f     run in foreground
  --do         debug output (python logging module)
  -o OPTIONS   mount options
```

### Current files
```
mount_point
├── <id>.<index>.app
├── cert.bin
├── header.bin
├── icon.bin          (only if meta region exists)
├── meta.bin          (only if meta region exists)
├── firm0.bin
├── firm1.bin         (up to 8 firm partitions may be displayed)
├── ticket.bin
├── tmd.bin
└── tmdchunks.bin
```

## mount_nand.py
Mounts NAND images, creating a virtual filesystem of decrypted partitions. Can read essentials backup by GodMode9, else OTP file/NAND CID must be provided in arguments.

```
usage: mount_nand.py [-h] [--otp OTP] [--cid CID] [--dev] [--ro] [--fg] [--do]
                     [-o OPTIONS]
                     nand mount_point

Mount Nintendo 3DS NAND images.

positional arguments:
  nand         NAND image
  mount_point  mount point

optional arguments:
  -h, --help   show this help message and exit
  --otp OTP    path to otp (enc/dec); not needed if NAND image has essentials
               backup from GodMode9
  --cid CID    NAND CID; not needed if NAND image has essentials backup from
               GodMode9
  --dev        use dev keys
  --ro         mount read-only
  --fg, -f     run in foreground
  --do         debug output (python logging module)
  -o OPTIONS   mount options
```

### Current files
```
mount_point
├── _nandinfo.txt
├── agbsave.bin
├── bonus.img         (only if GM9 bonus drive is detected)
├── ctrnand_fat.img
├── ctrnand_full.img
├── firm0.bin
├── firm1.bin         (up to 8 firm partitions may be displayed)
├── nand.bin
├── nand_hdr.bin
├── nand_minsize.bin
├── sector0x96.bin    (only if keysector is detected)
├── twl_full.img
├── twlmbr.bin
├── twln.img
└── twlp.img
```

## mount_romfs.py
Mounts Read-only Filesystem (RomFS) files, creating a virtual filesystem of the RomFS contents.

```
usage: mount_romfs.py [-h] [--fg] [--do] [-o OPTIONS] romfs mount_point

Mount Nintendo 3DS Read-only Filesystem (RomFS) files.

positional arguments:
  romfs        RomFS file
  mount_point  mount point

optional arguments:
  -h, --help   show this help message and exit
  --fg, -f     run in foreground
  --do         debug output (python logging module)
  -o OPTIONS   mount options
```

## mount_sd.py
Mounts SD contents under `/Nintendo 3DS`, creating a virtual filesystem with decrypted contents. `movable.sed` required.

Still needs testing, keep backups.

```
usage: mount_sd.py [-h] --movable MOVABLESED [--ro] [--dev] [--fg] [--do]
                   [-o OPTIONS]
                   sd_dir mount_point

Mount Nintendo 3DS SD card contents.

positional arguments:
  sd_dir                path to folder with SD contents (on SD: /Nintendo 3DS)
  mount_point           mount point

optional arguments:
  -h, --help            show this help message and exit
  --movable MOVABLESED  path to movable.sed
  --ro                  mount read-only
  --dev                 use dev keys
  --fg                  run in foreground
  --do                  debug output (python logging module)
  -o OPTIONS            mount options
```

# License/Credits
`mount_nand.py`, `mount_sd.py` are under the MIT license.

Special thanks to @Stary2001 for help with NAND crypto (especially TWL), and @d0k3 for SD crypto.
