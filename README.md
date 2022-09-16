# Install PostgreSQL on Arch Linux / Manjaro

Source: https://wiki.archlinux.org/index.php/PostgreSQL

1. Specification-OS:
```bash
lsb_release -cd ; getconf LONG_BIT ; lsb_release -a
```
2. Install postgresql package:
```bash
sudo pamac update ; sudo pamac upgrade ; yay -Syyuu
```
```bash
sudo pacman -Syyuu ; sudo pacman -S postgresql ; postgres --version
```
3. Switch to the postgres user account and initialize the database cluster:
```bash
sudo -iu postgres
```
```bash
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data/'
```
```bash
exit
```

**Options for initdb are as follows:**

- `--locale` is the one defined in /etc/locale.conf.
- `-E` is the default encoding for new databases.
- `-D` is the default location for storing the database cluster.

4. Start and enable the postgresql service:
```bash
sudo systemctl enable --now postgresql.service 
```
```bash
sudo systemctl start --now postgresql.service 
```
```bash
sudo systemctl status --now postgresql.service 
```

5. Create a Database and Database User
```bash
sudo -u postgres createuser "username"
```
```bash
sudo -u postgres createdb "database name"
```
```bash
sudo -u postgres psql
```
