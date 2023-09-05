# zfsnapr

`zfsnapr` is a tool for mounting recursive ZFS snapshots on an alternative root path.

Its primary intended use-case is for making consistant point-in-time backups using tools that are otherwise not ZFS-aware, though it is by no means limited to this.

## Usage

    zfsnapr [options] mount mountpoint
    zfsnapr [options] umount mountpoint
    zfsnapr [options] execute mountpoint command [args...]

    -E, --exclude PATH               Do not mount datasets under this path
    -r, --root PATH                  Only mount from the dataset mounted in this path
    -e, --exec                       Do not mount noexec
    -s, --suid                       Do not mount nosuid
    -V, --version                    Display program version and exit
    -D, --devfs                      Mount a /dev in the target
    -T, --tmpfs PATH                 Mount a tmpfs on this path within the target
    -p, --pool POOL                  Include this pool in the snapshot
    -P, --passthrough PATH           Pass-through a location from the host
    -h, --help                       Display usage and exit

The `mount` and `umount` subcommands simply create or tear down the requested mountpoints.

`execute` is equivalent to `mount`, executing the given command, followed by `umount`.  In this case `zfsnapr` will exit with the same return code as the executing command.

## Examples

    zfsnapr execute /mnt/backup \
        borg create backup@backup0:/backups::my-backup /mnt/backup

Mounts snapshots of the system into the `/mnt/backup` directory and uses [Borg](https://borgbackup.readthedocs.io/en/stable/) to create a remote backup from it.

    zfssnapr -eD -T /tmp -P /var/cache/backup execute /mnt/backup \
        chroot /mnt/backup /root/backup.sh

Create a chroot environment with executable mounts, a usable `/dev`, pristine tmpfs `/tmp`, and the host `/var/cache/backup` mounted read-write, and execute the script `/root/backup.sh` from within it.

    zfsnapr -r /home -E /home/sekrit mount /mnt/backup

Mount a snapshot of all datasets mounted under `/home`, except for `/home/sekrit`.

## Requirements

`zfsnapr` should work on any officially-supported Ruby version without additional dependancies, and any ZFS version which supports `zfs list -p`.

`zfsnapr` will abort on any error and may require manual intervention to cleanup mountpoints and snapshots - though normally a `zfsnapr umount` should suffice.
