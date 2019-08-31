# Docker DOMJudge Setup

## DOMServer
- Create new Digital Ocean one-click Docker host (recommend 1 CPU, large storage) called `domjudge-domserver`
- (Optional) Assign `domjudge-domserver` a floating IP
- SSH into `domjudge-domserver`
- `git clone https://github.com/gozzarda/domjudge_docker_compose.git`
- `cd domjudge_docker_compose/domserver`
- Edit `domserver.env` and **change the root and user passwords**
- `docker-compose up --detach`
- Navigate to `domjudge-domserver` in a web browser, and log into DOMJudge with admin/admin
  - **Change the admin password**
  - **Change the judgehost password**
- (Optional) create a Digital Ocean snapshot so you can restore later

## Judgehost
As of 2019-08-31, to set up a minimal judgehost is relatively simple:
- Spin up a new Debian host (e.g: debian-10 droplet on Digital Ocean)
- Install the `domjudge-judgehost` package as per the instructions on the [DOMjudge download page](https://www.domjudge.org/download):
  - `curl -o - https://www.domjudge.org/repokey.asc | sudo apt-key add -`
  - Create the file `/etc/apt/sources.list.d/domjudge.list` containing:
    ```
    deb     https://domjudge.org/debian unstable/
    deb-src https://domjudge.org/debian unstable/
    ```
  - `apt update`
  - `apt install domjudge-judgehost`
  - Follow the prompts to configure API access
- Enable cgroups as per the [DOMjudge Administrator's Manual Section 3.7](https://www.domjudge.org/docs/admin-manual-3.html#ss3.7)
  - Edit `/etc/default/grub` to add `cgroup_enable=memory swapaccount=1` to the end of `GRUB_CMDLINE_LINUX_DEFAULT`
  - `update-grub`
  - Will need to reboot for changes to take effect (see below)
- `dj_make_chroot` 
- `reboot`
- `judgedaemon -d` to start and daemonize a judgehost (Note it will not automatically restart, see below for that)

### Judgehost systemd service
DOMjudge provides an init script for the judgehost `/etc/init.d/domjudge-judgehost`.
Systemd uses this to automatically generate a systemd service (even if you try to run the script directly).
The script (currently) has a bug that causes it to fail to start.
You can fix `/etc/init.d/domjudge-judgehost` by replacing `CGROUPDIR=/cgroup` with `CGROUPDIR=/sys/fs/cgroup`.
You can now start the judgehost using `systemctl start domjudge-judgehost` and configure it to automatically restart with `systemctl enable domjudge-judgehost`.

### Judgehost package management
If you want to add packages (e.g. compilers) to the judgehost, they will need to be added in the chroot.
DOMjudge provides a script `dj_run_chroot` to get a shell in the chroot, or to directly run individual commands, e.g: `dj_run_chroot "apt install gcc=4:8.3.0-1"`.
If you really want, you can also add packages by editing `INSTALLDEBS` in `dj_make_chroot`, and rerunning `dj_make_chroot`. This is advisable if you think you will need to rebuild the chroot ever.
