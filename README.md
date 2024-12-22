# restic-repos

This is more or less my scrappy method of managing multiple restic repositories with different files to regularly backup, potentially over multiple hosts. 

## TODOS
- [ ] Write installer for new hosts
- [ ] Handle PGP-encrypted password files using `$RESTIC_PASSWORD_COMMAND` (e.g. for use with Yubikeys)
- [ ] Write systemd and cron schedulers for autobackup
- [ ] Compatibility with non-Linux hosts
- [ ] Improve cloud autobackup (specifically Backblaze b2)
- [ ] Add ability to run autobackups on all repos with one command

## Installation (manual)

- You'll obviously need [restic](https://github.com/restic/restic) installed first, as well as [GnuPG](https://github.com/gpg/gnupg) to automatically manage password files.
- Make `restic-repos` script executable and put it in your PATH.
- Place the `restic-repos.d` folder somewhere and edit the `$RESTIC_REPOS_DIR` variable in `restic-repos` script to point here.
- Schedule a cron job or timer to call `restic-repos REPO autobackup` every so often

## Usage

Output of `restic-repos help`:

```
restic-repos is a wrapper script for the restic backup program. It aims to help manage
multiple repositories (here shortened to just 'repos') by sourcing the unique environment and
configuration files associated with each repo managed by this script.

Usage:
  restic-repos REPO SCRIPT_COMMAND [OPTIONS]
  restic-repos REPO RESTIC_COMMAND [RESTIC_OPTIONS]...
  restic-repos SCRIPT_COMMAND
Run a script or restic command with specified repo, initialize a new repo, or run a script option.

Script commands:

  REPO specified:
  autobackup        Run backup with sourced repo files, intended mostly for use in cron/timer tasks
  edit              Edit configuration files for a repo. Must specify one of the following options:
    env, excludes, includes
  init              Initialize and configure a new repo using REPO as its name
  [restic commands] Pass commands and flags to restic with sourced repo files

  REPO not specified:
  lsrepo, -l        List all managed repository names
  help, -h          Print this help message

Also see official restic documentation for info on the actual program:
  "restic help", "man restic", or https://restic.readthedocs.io
```

## Configuration

Repository configs are defined in the restic-repos folder (default is `/usr/local/etc`). Each repository is represented as a folder with the following files:

`env` - Defines `$RESTIC_REPOSITORY` along with any other environment variables needed for the operation of the repository such as API keys. Permissions are restricted to root since this could potentially contain sensitive credentials. `$RESTIC_PASSWORD_FILE` *can* be defined here but the script manages keys on its own by default, see `key` below.

`excludes.conf` - Directories and files to exclude from autobackup. See `--exclude-file` flag in [restic documentation](https://restic.readthedocs.io/en/latest/040_backup.html#excluding-files).

`includes.conf` - Likewise, the paths that autobackup should work on. See `--files-from-verbatim` in [restic documentation](https://restic.readthedocs.io/en/latest/040_backup.html#including-files).

`key` - GnuPG-generated key used as the password file. Restricted to root for obvious reasons and is used as the default password if `$RESTIC_PASSWORD_FILE` is left undefined in `env`.

## Credits/references/further reading
- https://wiki.archlinux.org/title/Restic
- https://forum.restic.net/t/recipes-for-managing-repository-environment-variables/1716/2
