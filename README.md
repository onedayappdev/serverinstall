
![OneDayLogo](https://i.imgur.com/NauA27C.png)

Ansible scripts to install PostgreSQL, Postgrest, Glewlwyd, Nodered, RSS-Bridge, Nginx, phpPgAdmin, Apticron, and UFW on a new Ubuntu 20.04 LTS server.


## What is this?

It's an automated deployment that follows the video tutorial series I've started here:

[![IMAGE ALT TEXT](http://img.youtube.com/vi/DXmrK0M5sn8/0.jpg)](http://www.youtube.com/watch?v=DXmrK0M5sn8 "DNS and a Server")

This is all of the back-end requried to set up a database, an API that talks to that database, an authentication server, and some scraping and low-code development tools that might be useful to go with them for fetching data out of remote services.

## How do I use it?

Install Ansible (`brew install ansible` on a Mac after you've [installed Homebrew](https://brew.sh/) or `sudo apt install ansible` if you're running Ubuntu within Windows). Download the repo, unzip it, cd into the directory in a terminal and run the `serverinstall` shell script from an OSX terminal or Ubuntu command prompt like...

```
$ sh serverinstall
```

It will ask you for a fully qualified domain name, an email address and password (one without 2fa, this is for the server to use to send notifications by itself, make an ["app password"](https://support.google.com/accounts/answer/185833?hl=en)), whether you'd like a firewall or not (say yes...), and if you put in an email from a custom domain, the SMTP server settings as you would put in an email client on your laptop/desktop.  It knows the settings for common email services like Yahoo, Gmail, Hotmail, and iCloud if you give it an address from one of those, so you may want to use one of those free email services to make it a bit easier.

It presumes your SSH config logs into your server with the root account, which is the default on Digital Ocean.  If you need to log into another account and use sudo, uncomment the lines atop `main.yml` in the includes directory and specify the settings you need.  On a default AWS EC2 Ubuntu instance this would be `remote_user: ubuntu` and `become: True`, for example.

## Then what?

The install will take some time, but when it finishes you'll have a server up and running with lots of spiffy new tools that my videos will explain how to use for fun and profit!  The links to the things it installs are...

* https://your.server.net/pgadmin/ for phpPgAdmin (database admin)
* https://your.server.net/nodered/ for Nodered (a low-code dev platform)
* https://your.server.net/rssbridge/ for RSSBridge (scrape without APIs)
* https://your.server.net/authsrv/ for Glewlwyd (Oauth/OpenID server)
* https://your.server.net/api/ for Postgrest, a server that automatically turns your PostgreSQL database into an advanced REST API.

The username to log into all of these is `admin`, and the password will be in your home folder in a file called `ansible-admin-password.txt`, Ansible created a random password for you when installing everything.

If you told it "yes" on the firewall option it will install UFW and restrict incoming traffic to ports 80, 443, and 22 (web and ssh) only.

## Are there any other things I need to do after it finishes?

1. Download [Postman](https://www.postman.com/) if you don't have it, it's very useful for testing APIs.

2. Log into phpPgAdmin, click on the API database, and then click "SQL" and paste/run the contents of the `permissions_setup.sql` file that you downloaded from this repository. It sets the proper permissions on the database for the API.

3. Log into your Glewlwyd instance and click `Users`, then the pencil to edit the `Administrator` user, and add the scope `openid` to the admin account, as well as the role `web_admin`.

There is a Postman collection and environment in this repo that you can import for some basic API interaction with the services installed in this deployment. If you've never used Postman before, when you open it up you can click the import button to add the files, like so:

![Postman Import](https://i.imgur.com/HaSsllY.png)
 
Postgrest controls access by database roles claimed in the authentication token that the client/user passes with a request. In this deployment, `web_anon` is the default unprivileged user, `web_user` has permissions to READ, INSERT, and UPDATE in the API database, and `web_admin` has all privileges.  You'll want to do a lot of your testing with the `web_admin` account to make sure things work in your apps, in all likelihood.  These permissions can be changed, added, deleted, etc of course.

## Help! Something didn't work, I got a red error from Ansible

Feel free to leave an [issue](https://github.com/onedayappdev/serverinstall/issues), and I'll help you sort it out.  If something randomly failed because of your connection going down or the server rebooting or some other random occurrence, try to re-run the scripts and it may fix whatever is broken (within reason). Of course you can simply delete and create a new server instance as well to start from scratch, that's the beauty of cloud computing ;).



