# restic-repos

This is more or less my scrappy method of managing multiple restic repositories with different files to regularly backup, potentially over multiple hosts. 

## TODOS
- [ ] Write installer for new hosts
- [ ] Handle PGP-encrypted password files using `$RESTIC_PASSWORD_COMMAND`
- [ ] Write systemd and cron schedulers for autobackup
- [ ] Compatibility with non-Linux hosts

## Installation (manual)

- You'll obviously need [restic](https://github.com/restic/restic) installed first, as well as `gpg` to automatically manage password files.
- Make `restic-repos` script executable and put it in your PATH.
- Place the `restic-repos.d` folder somewhere and edit the `$RESTIC_REPOS_DIR` variable in `restic-repos` script to point here.

## Usage

Initialize a new repository:

```restic-repos NEWREPO init```

Edit config files (see *Configuration*):

```restic-repos REPO edit [env/excludes/includes]```

Manage restic repositories using standard restic arguments:

```restic-repos REPO [args]```

List all configured repositories with `lsrepo`:

```restic-repos lsrepo```

Put this command in [timers](https://wiki.archlinux.org/title/Restic#Scheduling) or cron jobs to use automated backups and snapshot management, or invoke it manually to create an autobackup snapshot:

```restic-repos REPO autobackup```

## Configuration

Repository configs are defined in the restic-repos folder (default is `/usr/local/etc`). Each repository is represented as a folder with the following files:

`env` - Defines `$RESTIC_REPOSITORY` along with any other environment variables needed for the operation of the repository such as API keys. Permissions are restricted to root since this could potentially contain sensitive credentials. `$RESTIC_PASSWORD_FILE` *can* be defined here but the script manages keys on its own by default, see `key` below.

`excludes.conf` - Directories and files to exclude from autobackup. See `--exclude-file` flag in [restic documentation](https://restic.readthedocs.io/en/latest/040_backup.html#excluding-files).

`includes.conf` - Likewise, the paths that autobackup should work on. See `--files-from-verbatim` in [restic documentation](https://restic.readthedocs.io/en/latest/040_backup.html#including-files).

`key` - GnuPG-generated key used as the password file. Restricted to root for obvious reasons and is used as the default password if `$RESTIC_PASSWORD_FILE` is left undefined in `env`.

## Credits/references/further reading
- https://wiki.archlinux.org/title/Restic
- https://forum.restic.net/t/recipes-for-managing-repository-environment-variables/1716/2
