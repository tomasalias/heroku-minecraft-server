# Heroku Minecraft Server ![Icon](server-icon.png)
Runs a Free Minecraft Server within Heroku.

For the Buildpack, see [Here.](https://github.com/Epicfisher/heroku-buildpack-minecraft)

## Features

```
* No Server Idling (Uses a Worker Process over Web)
* Supports Paper, Purpur, Spigot & Other Servers
* Automatically Updates the Server with new Minecraft Versions
* Creates a Permanent Server Address using Dynu DNS from Ngrok
* World Saving/Loading with Dropbox or Amazon S3
```

## Missing Features

```
Empty List as of Now
```

## Notes

```
* Ngrok Addresses are easily Guessable/Bruteforcable: If you don't want Random Griefers joining your server, invest in a Whitelist/Server Security Plugins
* Store your World on Dropbox/Amazon S3 as a Compressed (.tar.gz) instead of Git, as using Git will dramatically increase your Upload Size to Heroku
```

## Setup

### Heroku Git Remote

First, clone this Repo

`git clone https://github.com/Epicfisher/heroku-minecraft-server`

Next, create a Heroku app, and link your Clone to use Heroku as your Remote using the [CLI](https://toolbelt.heroku.com/)

`heroku git:remote -a <Your-App-Name>`

You can now Freely Edit the Server Properties/Server Icon/OPs/Whitelist

### Config Variables

#### Minecraft Config Variables

You can customise your Minecraft Server using Config Variables

(Keep in Mind these Variables are set with Optimised Default Values. Only change these if you know what you're doing)

```
heroku config:set MINECRAFT_VERSION = "1.16.5" = Change the Minecraft Version your Server will Run. "latest" Downloads Newest Available Version for your Server. Some Servers do not Support Latest Version Downloading
heroku config:set SERVER="paper" = Change the Server Software used. Vanilla Server not Available due to Poor Heroku Performance. Some Servers may only operate with More RAM. "none" Downloads No Server. Server Options available:
"purpur"
"tuinity"
"paper"
"spigot"
"none"
heroku config:set MEMORY="384m" = Change Memory Allocated to the Java Server, can be in "(m)egabytes or (g)igabytes. Free Dyno can support up to 500m"
```

#### Additional Config Variables

You can also set Additional Optional Config Variables

```
heroku config:set SYNC_SAVE = "false" = Forcefully Disable World Saving through Dropbox/Amazon S3. Useful for Loading a Custom Word without Overwriting
heroku config:set SYNC_LOAD = "false" = Forcefully Disable World Loading through Dropbox/Amazon S3. Useful for Forcefully Saving over an Old World
```

### Ngrok (Creates a Temporary Joinable IP Address for your Server)

Create a New [Free Ngrok Account](https://ngrok.com/) and copy your Auth Token

Set the `NGROK_API_TOKEN` Config Variable to your Auth Token

`heroku config:set NGROK_API_TOKEN="Your-Auth-Token"`

### Dynu [OPTIONAL] (Creates a Permanent Joinable Address for your Server)

Dynu DNS requires an Ngrok account

Create a New [Free Dynu Account](https://dynu.com)

From the Control Panel, navigate to `DDNS Services`

Create your Domain Name. This will be the IP others will Connect To to Join your Server

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

Create a new [Free Dropbox Account](https://www.dropbox.com/basic)

Download `Dropbox-Uploader` from Github onto your local Linux / Cygwin Windows Machine

`curl "https://raw.githubusercontent.com/andreafabrizi/Dropbox-Uploader/master/dropbox_uploader.sh" -o dropbox_uploader.sh`

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

### Amazon S3 [OPTIONAL] (Paid Alternative to Permanently Save/Load your World)

Create an [AWS Account](https://aws.amazon.com/) and an S3 Bucket

Configure the Bucket and set your AWS Keys to the `AWS_BUCKET`, `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` Config Variables

```
heroku config:set AWS_BUCKET="Your-Bucket-Name"
heroku config:set AWS_ACCESS_KEY="Your-Access-Key"
heroku config:set AWS_SECRET_KEY="Your-Secret-Key"
```

### Upload the Server

Finally, Commit & Push the Repo to Heroku

```
git add .
git commit -am "Heroku Initial Upload"
git push heroku
```

## Usage

After Enabling your Worker/Web Dyno in the Resources tab, your Server should Start

If using Ngrok, your Server's IP Address can be found at `https://dashboard.ngrok.com/endpoints/status` 

If using Dynu, connect to your Created Domain Name (May Take up to 5 Minutes to Update)

### Connect to your Server Console

The Minecraft Server runs inside of a `screen` Session. You can use [Heroku Exec](https://devcenter.heroku.com/articles/heroku-exec) to Connect to your Server Console

```
heroku ps:exec
screen -r
```

**WARNING** You are now connected to the Minecraft server. Use `Ctrl-A Ctrl-D` to Suspend the Screen Session. (If you hit `Ctrl-C` while in the Session, you'll Terminate the Minecraft Server)
