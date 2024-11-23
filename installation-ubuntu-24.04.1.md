- Assume that the software is install in the folder `erp` (`$HOME/erp`)
- Assume that username is `USERNAME`
- Tested with `Ubuntu 24.04.1 LTS`

# Prereq

- `sudo apt-get update -y`
- `sudo apt-get upgrade -y`
- `sudo apt install supervisor`

# Installation

### Setup correct date and timezone

- `date`
- `sudo timedatectl set-timezone "Asia/Bangkok"`

### Python stuffs

- Install `uv` package manager to create virtual environment separated from OS's env.
  - `curl -LsSf https://astral.sh/uv/install.sh | sh`
  - Restart shell if needed.
- `uv venv` (Create virtual environment)
- In `~/.profile` insert `source $HOME/.venv/bin/activate` at the end. (Make sure to use python exe in the virtual environment, not from the OS.)
- `sudo apt install python3.12-venv python3-dev build-essential` (For compiling python wheel)

### Database

- `sudo apt-get install software-properties-common`
- `sudo apt install mariadb-server mariadb-client`
- `sudo apt-get install libmysqlclient-dev`
- `sudo mysql_secure_installation`
  - Disallow root login remotely? [Y/n]: N
- `sudo nano /etc/mysql/my.cnf`
- `sudo vi /etc/mysql/my.cnf`

(Put the following lines at the end)

```
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```

- `sudo service mysql restart`

### Others

- `sudo apt-get install redis-server`
- `sudo apt-get install xvfb libfontconfig wkhtmltopdf`

### Node

- `curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash`
- `source ~/.profile`
- `nvm install 22`
- `nvm use 22`
- `npm install -g yarn`

### Frappe

- `uv pip install frappe-bench pip`
  - `pip` is used during production setup.

### Init bench project

- `cd ~ && bench init erp --frappe-branch version-15`

### Install ERPNext

- `bench get-app erpnext --branch version-15`

### Install HRMS (Optional)

- `bench get-app hrms --branch version-15`

### Bench start

- `bench start`

### Create site (Another tab while bench is running on another tab)

- `cd ~/erp && bench new-site [SITENAME]`

### Linking app to site

Perform this steps in another terminal white the `bench start` terminal is open.

- `bench --site [SITENAME] install-app erpnext`
- `bench --site [SITENAME] install-app hrms`

#### Automated creating and linking apps

```bash
SITENAME="tech08" &&
printf "\nCreating site: $SITENAME\n"
bench new-site $SITENAME &&
printf "\nInstall app for $SITENAME \n" &&
bench --site $SITENAME install-app erpnext &&
bench --site $SITENAME install-app hrms
```

# Production setup

### Bench setup

I need to become root user but also as access to the python virtual environment.

- `APP_USER=USERNAME`
- `sudo -i`
- `source /home/$APP_USER/.venv/bin/activate`
- `cd /home/$APP_USER/erp`
- `bench setup production $APP_USER`

### nginx

- `sudo vi /etc/nginx/nginx.conf`
- Change to `USER` specified in `$APP_USER`
- `bench config dns_multitenant off`
- `bench set-nginx-port [SITENAME] [PORT]`
- `sudo service nginx reload`

### Supervisor

- `sudo service supervisor start` (If the service was stopped previously)
- `bench setup supervisor`
- `sudo ln -sf $HOME/erp/config/supervisor.conf /etc/supervisor/conf.d/frappe-bench.conf`
- `sudo supervisorctl reread`
- `sudo supervisorctl update`
- `sudo supervisorctl status` - Should see all services running like this
- `sudo supervisorctl restart` (Can try this one if not working)

# Stop production

- `sudo service nginx stop`
- `sudo service supervisor stop`
  - If supervisor cannot stop
  - `sudo supervisorctl`
  - `stop all`
- Directory logs has some files that are owned by root
  - `sudo chown -R USERNAME:USERNAME logs`
- `bench start`

# Start production

- `sudo systemctl start nginx`
- `sudo service supervisor start`
  - `sudo supervisorctl status`
- `bench --site all enable-scheduler`
- `bench --site all set-maintenance-mode off`

#### Enable scheduler

- I have to do this in order to be able to import data.
  - `bench --site site_name set-config pause_scheduler 0`
  - https://discuss.frappe.io/t/data-import-the-scheduler-is-inactive/104708/3

#### Disable maintenance mode

- Make sure to set in `erp/sites/common_site_config.json`

```json
{
  "maintenance_mode": 0,
  "pause_scheduler": 0
}
```

```bash
bench --site SITENAME set-maintenance-mode off
```
