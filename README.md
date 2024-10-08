# Heroku Minecraft Server ![Icon](server-icon.png)
Runs a Free Minecraft Server within Heroku.

For the Buildpack, see [Here.](https://github.com/Epicfisher/heroku-buildpack-minecraft)

## Features

* No Server Idling (Uses a Worker Process over Web)
* Supports Paper, Purpur, Spigot & Other Servers
* Compatible with Plugins/Mods (For Mods, a Custom Modded Server JAR is Needed)
* Automatically Updates the Server with new Minecraft Versions
* Creates a Permanent Server Address using Dynu DNS
* World Saving/Loading through Dropbox or Amazon S3

## Notes

* Ngrok Addresses are easily Guessable/Bruteforcable: If you don't want Random Griefers joining your server, invest in a Whitelist/Server Security Plugins
* Store your World on Dropbox/Amazon S3 as a Compressed (.tar.gz) file instead of Git, as using Git will dramatically increase your Upload Size to Heroku
* Plugins can't auto-update with the Server: If you use a lot of them, consider setting your `MINECRAFT_VERSION` to an older Version of Minecraft, until you can Manually Update your Plugins/Server to a newer Version

## Setup

### Heroku Git Remote

First, clone this Repo

```
git clone https://github.com/Epicfisher/heroku-minecraft-server
```

Next, create a Heroku app, and then link your Repo to use Heroku as your Remote using the [CLI](https://toolbelt.heroku.com/)

```
heroku git:remote -a <Your-App-Name>
```

You can now Freely Edit the Server Properties/Server Icon/OPs/Whitelist

### Optional Config Variables

#### Minecraft Optional Config Variables

You can customise your Minecraft Server using Config Variables

(Keep in Mind these Variables are set with Optimised Default Values. Only change these if you know what you're doing)

```
heroku config:set MINECRAFT_VERSION = "latest" = Change the Minecraft Version your Server will Run. "latest" Downloads Newest Available Version for your Server. Some Servers do not Support Latest Version Downloading
heroku config:set SERVER="purpur" = Change the Server Software used. Vanilla Server not Available due to Poor Heroku Performance. Some Servers may only operate with More RAM. "none" Downloads No Server. Server Options available:
"purpur"
"tuinity"
"paper"
"spigot"
"none"
heroku config:set MEMORY="500m" = Change Memory Allocated to the Java Server, can be in (m)egabytes or (g)igabytes. Free Dyno can support up to 500m
```

##### Additional Optional Config Variables

You can also set Additional Optional Config Variables. These are more situational in use

```
heroku config:set SYNC_SAVE = "false" = Forcefully Disable World Saving through Dropbox/Amazon S3. Useful for Loading a Custom Word without Overwriting it
heroku config:set SYNC_LOAD = "false" = Forcefully Disable World Loading through Dropbox/Amazon S3. Useful for Forcefully Saving over an Old World
```

### Ngrok (Creates a Temporary Joinable IP Address for your Server)

Create a New [Free Ngrok Account](https://ngrok.com/) and copy your Auth Token

Set the `NGROK_API_TOKEN` Config Variable to your Auth Token

```
heroku config:set NGROK_API_TOKEN="Your-Auth-Token"
```

### Dynu [OPTIONAL] (Creates a Permanent Joinable Address for your Server)

**NOTE: Dynu DNS requires an Ngrok account**

Create a New [Free Dynu Account](https://dynu.com)

From the Control Panel, navigate to `DDNS Services`

Create your Domain Name from `Option 1: One of Dynu's Free Shared Top Level Domains`. This will be the IP others will Connect To to Join your Server

Click `DNS Records` and Add a New DNS Record with the Following Information set. All other Fields can be Left as Default

```
Node Name = mcngrok
```

Click `Add DNS Record`

Now add another DNS Record and set the Following Information. All other Fields can be Left as Default
```
Node Name = _minecraft._tcp
Type = SRV
Target = mcngrok.<The-Rest-Of-Your-Domain-Here>
```

Click `Add DNS Record`

Copy your [OAuth Client ID and Secret](https://www.dynu.com/ControlPanel/APICredentials)

Set the `DYNU_CLIENT_ID` and `DYNU_SECRET` Config Variables to your Client ID and Secret

```
heroku config:set DYNU_CLIENT_ID="Your-Client-ID"
heroku config:set DYNU_SECRET="Your-Secret"
```

### Dropbox [OPTIONAL] (Free way to Permanently Save/Load your World)

Create a New [Free Dropbox Account](https://www.dropbox.com/basic)

Download `Dropbox-Uploader` from Github onto your local Linux / Cygwin Windows Machine:

```
curl "https://raw.githubusercontent.com/andreafabrizi/Dropbox-Uploader/master/dropbox_uploader.sh" -o dropbox_uploader.sh
```

Next, Run the Bash File and Follow it's Instructions

```
chmod +x dropbox_uploader.sh
./dropbox_uploader.sh -f Config.txt
```

Your Config file `Config.txt` should Contain the Following

```CONFIGFILE_VERSION=2.0
OAUTH_APP_KEY="Your-App-Key"
OAUTH_APP_SECRET="Your-App-Secret"
OAUTH_REFRESH_TOKEN="Your-Refresh-Token"
```

Set the `DROPBOX_APP_KEY`, `DROPBOX_APP_SECRET` and `DROPBOX_ACCESS_TOKEN` Config Variables to these Values appropriately

```
heroku config:set DROPBOX_APP_KEY="Your-App-Key"
heroku config:set DROPBOX_APP_SECRET="Your-App-Secret"
heroku config:set DROPBOX_ACCESS_TOKEN="Your-Refresh-Token"
```

<details><summary><b>Amazon S3 [OPTIONAL] (Paid Alternative to Permanently Save/Load your World)</b></summary>

Create an [AWS Account](https://aws.amazon.com/) and an S3 Bucket

Configure the Bucket and set your AWS Keys to the `AWS_BUCKET`, `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` Config Variables

```
heroku config:set AWS_BUCKET="Your-Bucket-Name"
heroku config:set AWS_ACCESS_KEY="Your-Access-Key"
heroku config:set AWS_SECRET_KEY="Your-Secret-Key"
```
</details>

### Upload the Server

Finally, Commit & Push the Repo to Heroku

```
git add .
git commit -am "Heroku Initial Upload"
git push heroku
```

## Usage

After Enabling your Worker/Web Dyno in the Resources tab, your Server should Start

If using **Ngrok**, your Server's IP Address will be found at `https://dashboard.ngrok.com/endpoints/status` 

If using **Dynu**, your Server's IP Address will be your Created Domain Name (May Take up to 5 Minutes to Update)

### Connect to your Server Console

The Minecraft Server runs inside of a `screen` Session. You can use [Heroku Exec](https://devcenter.heroku.com/articles/heroku-exec) to Connect to your Server Console

```
heroku ps:exec
screen -r
```

**WARNING** You are now connected to the Minecraft server. Use `Ctrl-A Ctrl-D` to Suspend the Screen Session. (If you hit `Ctrl-C` while in the Session, you'll Terminate the Minecraft Server)

## Contributors

This project contains work from the following contributors:

- [jkutner](https://github.com/jkutner) - For their [original Heroku Minecraft Server repo](https://github.com/jkutner/heroku-buildpack-minecraft)
- [onpat](https://github.com/onpat) and [gamer4life1](https://github.com/gamer4life1) - For their [Implementations of Dropbox Syncing](https://github.com/onpat/minecraftBuildpack)
