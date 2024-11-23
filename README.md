## Prerequisites 
- A clone of the provided Git repository 
- nginx and ufw packages installed

## Creating the System User and Directory Structure

1. Create the system user with home directory at /var/lib/webgen and a non-login shell:

`sudo useradd -r -d /var/lib/webgen -s /usr/sbin/nologin webgen` 

**What does this do?**
`-r`: Creates a system user.
`-d /var/lib/webgen`: Specifies the home directory for the user.
`-s /usr/sbin/nologin`: Sets a non-login shell to prevent direct login.

The benefit of creating webgen as a system user because it has limited privileges and is used for running system services or background tasks. Now our user is set up without requiring direct login or unnecessary privileges.

2. Create the Home Directory Structure

`mkdir /var/lib/webgen/bin`
`mkdir /var/lib/webgen/HTML` 

3. Move the `generate_index` script into the bin folder

`mv generate_index /var/lib/webgen/bin`

4. Give ownership to webgen and make generate_index executable 

`sudo chown -R webgen:webgen /var/lib/webgen`

`sudo chmod 700 /var/lib/webgen/bin/generate_index` 

## Setting up the Timer and Service Files

1. Move both the provided `generate-index.service` and `generate-index.timer` files to `/etc/systemd/system`

`sudo mv ~/Assignment3_P1/generate-index.service /etc/systemd/system`

`sudo mv ~/Assignment3_P1/generate-index.timer /etc/systemd/system`

2. Reload Systemd After the Changes

`sudo systemctl daemon-reload` 

3. Enable and Start the Timer

`sudo systemctl enable generate-index.timer`

`sudo systemctl start generate-index.timer`

4. Verify the Timer and Service Run Successfully

- To check when the timer last ran and when it will next run: `systemctl list-timers --all | grep generate-index.timer`

- To check if the service ran successfully: `sudo systemctl status generate-index.service`

- View logs for generate-index.service: `sudo journalctl -u generate-index.service`

- View logs for the generate-index.timer: `sudo journalctl -u generate-index.timer`

## Setting Up Nginx

1. Open the `nginx.conf` file 

`sudo nvim /etc/nginx/nginx.conf`

2. Edit it to have `user webgen`

3. Make folders for the server block 

`mkdir /etc/nginx/sites-available`

`mkdir /etc/nginx/sites-enabled`

4. Add the provided server block file `sites.conf` to `/etc/nginx/sites-available`

5. Create a symbolic link `ln -s /etc/nginx/sites-available/sites.conf /etc/nginx/sites-enabled/sites.conf`

We create a seperate server block file instead of putting it in nginx.conf because it's easier to maintain. It's easier to add more sites by creatin new server block files into the `/etc/nginx/sites-available` folder and create symbolic links. 

- You can use `sudo systemctl status nginx` to check the status. 
- You can use `sudo nginx -t` to check the nginx configuration.








