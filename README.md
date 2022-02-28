# zfsnapr

`zfsnapr` is an experimental tool to mount a snapshot of a ZFS system onto an alternative location.

This is intended for use in making consistent backups using tools that are otherwise not ZFS-aware.

It works by creating a recursive snapshot of all ZFS pools, and then recursively mounting snapshots of all datasets with a mountpoint into the target location. On umount, these are recursively unmounted and the snapshots destroyed.

## Usage

    zfsnapr [options] mount mountpoint
    zfsnapr [options] umount mountpoint
    zfsnapr [options] execute mountpoint command [args...]

    -s, --suid                       Do not mount nosuid
    -e, --exec                       Do not mount noexec
    -V, --version                    Display program version and exit
    -h, --help                       Display usage and exit

## Examples

    zfsnapr execute /mnt/backup \
        borg create backup@backup0:/backups::my-backup /mnt/backup

Mounts snapshots of the system into the `/mnt/backup` directory and uses [Borg](https://borgbackup.readthedocs.io/en/stable/) to create a remote backup.

## Requirements

`zfsnapr` should work on any officially-supported Ruby version, and any ZFS version which supports `zfs list -p`.

`zfsnapr` is intended for use on pure ZFS systems, and assumes that the full filesystem hierarchy can be mounted through ZFS mountpoints only.

`zfsnapr` will abort on any error and may require manual intervention to cleanup mountpoints and snapshots.
