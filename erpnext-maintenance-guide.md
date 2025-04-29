# Stop production (if production setup is used)
1. `sudo service nginx stop`
2. `sudo service supervisor stop`
	- If supervisor cannot stop
        - `sudo supervisorctl`
        - `stop all`
3. Directory logs has some files that are owned by root
	- `cd $HOME/erp && sudo chown -R $USER$:$USER$ logs`
4. `bench start`

# Update
> Make sure to stop production and have another terminal is opened with `bench start`
- `bench update --reset`

# Start production
- Setting
    - `SITE_NAME=site_name`
- Command
    - `sudo systemctl start nginx`
    - `sudo service supervisor start`
        - (Check status) `sudo supervisorctl status`
    - `bench --site $SITE_NAME enable-scheduler`
    - `bench --site $SITE_NAME set-config pause_scheduler 0`
    - `bench --site $SITE_NAME set-maintenance-mode off`

# Backup site
- Setting
    - `SITE_NAME=site_name`
    - `BACKUP_FOLDER=~/backup`
- Command
    - `bench --site $SITE_NAME backup --backup-path $BACKUP_FOLDER --with-files --compress`

# Restore site
- Setting
    - `SITE_NAME=site_name`
    - `DB_FILE=~/x-database.sql.gz`
    - `PRIVATE_FILE=~/x-private-files.tar`
    - `PUBLIC_FILE=~/x-files.tar`
- `bench --site $SITE_NAME --force restore $DB_FILE --with-private-files $PRIVATE_FILE  --with-public-files ./ $PUBLIC_FILE`
- `bench migrate`

# Drop site
`bench drop-site site_name --no-backup`

# Reinstall site
- `bench --site site_name reinstall`

# Others
- Check nginx status
    - `sudo systemctl status nginx`
- Check supervisor status
    - `sudo supervisorctl status`
- Check ports
    - `sudo ss -tunlp`