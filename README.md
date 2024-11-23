## Prerequisites 
- A clone of the provided Git repository 
- nginx and ufw packages installed

<br>

## Creating the System User and Directory Structure

1. Create the system user with home directory at /var/lib/webgen and a non-login shell:

`sudo useradd -r -d /var/lib/webgen -s /usr/sbin/nologin webgen` 

<br>

**What does this do?**

`-r`: Creates a system user.

`-d /var/lib/webgen`: Specifies the home directory for the user.

`-s /usr/sbin/nologin`: Sets a non-login shell to prevent direct login.
<br>

The benefit of creating webgen as a system user because it has limited privileges and is used for running system services or background tasks. Now our user is set up without requiring direct login or unnecessary privileges.
<br>

2. Create the Home Directory Structure

`mkdir /var/lib/webgen/bin`
`mkdir /var/lib/webgen/HTML` 

<br>

3. Move the `generate_index` script into the bin folder

`mv generate_index /var/lib/webgen/bin`

<br>

4. Give ownership to webgen and make generate_index executable 

`sudo chown -R webgen:webgen /var/lib/webgen`

`sudo chmod 700 /var/lib/webgen/bin/generate_index` 

<br>

## Setting up the `.timer` and `.service` Files for generate-index

1. Move both the provided `generate-index.service` and `generate-index.timer` files to `/etc/systemd/system`

`sudo mv ~/Assignment3_P1/generate-index.service /etc/systemd/system`

`sudo mv ~/Assignment3_P1/generate-index.timer /etc/systemd/system`
<br>

2. Reload Systemd After the Changes

`sudo systemctl daemon-reload` 
<br>

3. Enable and Start the Timer

`sudo systemctl enable generate-index.timer`

`sudo systemctl start generate-index.timer`
<br>

4. Verify the Timer and Service Run Successfully

- To check when the timer last ran and when it will next run: `systemctl list-timers --all | grep generate-index.timer`

- To check if the service ran successfully: `sudo systemctl status generate-index.service`

- View logs for generate-index.service: `sudo journalctl -u generate-index.service`

- View logs for the generate-index.timer: `sudo journalctl -u generate-index.timer`

<br>

## Setting Up `nginx`

1. Open the `nginx.conf` file 

`sudo nvim /etc/nginx/nginx.conf`
<br>

2. Edit it to have `user webgen`
<br>

3. Make folders for the server block 

`mkdir /etc/nginx/sites-available`

`mkdir /etc/nginx/sites-enabled`
<br>

4. Add the provided server block file `sites.conf` to `/etc/nginx/sites-available`
<br>

5. Create a symbolic link 

`ln -s /etc/nginx/sites-available/sites.conf /etc/nginx/sites-enabled/sites.conf`
<br>

6. Start and enable nginx

`sudo systemctl start nginx`

`sudo systemctl enable nginx`
<br>

We created a seperate server block file instead of putting it in nginx.conf because it's easier to maintain. We can add sites by creating new server block files into the `/etc/nginx/sites-available` folder and creating symbolic links. 

- Use `sudo systemctl status nginx` to check the nginx status and verify it's running. 
- Use `sudo systemctl reload nginx` to reload nginx to apply any changes you've made to the configuration.
- Use `sudo nginx -t` to check for errors in the nginx configuration. 

## Setting Up `ufw`

1. Allow SSH connection in our firewall

`sudo ufw allow ssh`
<br>

2. Limit the rate for ssh connections

`sudo ufw limit ssh`
<br>

3.  Allow http connections

`sudo ufw allow http`
<br>

4. Enable the firewall

`sudo systemctl enable --now ufw.service`
<br>

5. Check the status of the firewall to confirm everything is working correctly

`sudo ufw status verbose`

<br>

## Your Web Server is Now Set Up!

With everything complete your final page should look like this: 

![Image of system information on webpage](/Success_Screenshot.png)









