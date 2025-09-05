# cyborg

cyborg is a POSIX-compatible shell wrapper around borg that is:

- **Easy to set up** - use a configuration template to configure borg quickly.
- **Powerful** - `borg` can be invoked with the desired settings at any time.
- **Easy to use** - regular `borg` use is scripted, and does the right thing.

## Installation

`cyborg` assumes a repository has been already created and can be accessed from
the device to back up. Consult [`borg init` documentation][1] to learn how to
create a repository.

### Workstation

1. Copy `cyborg` to `/usr/local/bin` or another location on your `PATH`.
2. Create a directory (e.g. `~/.config/cyborg`) for cyborg configuration.
3. Copy `cyborg.conf` and `borg.patterns` to `~/.config/cyborg`. `cyborg.conf`
   can be copied under multiple names to perform backups to different
   repositories or using different rules (e.g. `onsite.conf` and `offsite.conf`).
4. Ensure the repository passphrase can be retrieved by borg using the method
   specified in the configuration.
5. Run the first backup by calling `cyborg ~/.config/cyborg/cyborg.conf auto`.
6. Schedule `cyborg /etc/cyborg/cyborg.conf auto` to be run periodically:
  - On Linux and BSDs: add the command to your user's crontab.
  - On MacOS: edit and copy `examples/cyborg.plist` to `~/Library/LaunchAgents`.

### Server

1. Copy `cyborg` to `/usr/local/bin` or another location on your `PATH`.
2. Create a directory (e.g. `/etc/cyborg`) for cyborg configuration.
3. Copy `cyborg.conf` and `borg.patterns` to `/etc/cyborg`. `cyborg.conf` can be
   copied under multiple names to perform backups to different repositories or
   using different rules (e.g. `onsite.conf` and `offsite.conf`).
4. Ensure the repository passphrase can be retrieved by borg using the method
   specified in the configuration.
5. Consider creating `/var/borg` and pointing the configuration to that
   directory to prevent borg from creating its files in a user home directory.
6. Run the first backup by calling `sudo cyborg /etc/cyborg/cyborg.conf auto`.
7. If the backup was successful add `cyborg /etc/cyborg/cyborg.conf auto`
   to cron.

## Usage

The first argument to `cyborg` is the name of the configuration file to load.
The remaining arguments are forwarded to `borg` without changes except for the
additional `auto` command explained in the next subsection. For example, in
order to list all archives in the repository configured in `onsite.conf` call
`cyborg onsite.conf list`. `list` and any subsequent arguments will be forwarded
to `borg`.

### Auto-mode

`cyborg` implements automatic mode to make it easy to do the right thing. It
consists of running a sequence of three borg operations:

1. Creating a new backup using the rules set in the configuration file.
2. Pruning old backups using the rules set in the configuration file.
3. Compacting the repository to free disk space.

The auto-mode is invoked via `cyborg <config file> auto`. It's **the only
command not forwarded to `borg`**.

The auto-mode is configured via `CYBORG_AUTO_CREATE` and `CYBORG_AUTO_PRUNE`
which should be set to parameters to be forwarded directly to `borg create` and
`borg prune` respectively.

## Author

cyborg is developed and maintained by [Greg Navis][2].

[1]: https://borgbackup.readthedocs.io/en/stable/usage/init.html
[2]: https://www.gregnavis.com/
