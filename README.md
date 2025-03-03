# Description:
A Telegram Bot written in Python language to mirror files on the internet to Google Drive.

## Credits

This repository is a fork-clone of [lzzy12's python-aria-mirror-bot](https://github.com/lzzy12/python-aria-mirror-bot) and [afdulfauzan's python-aria-mirror-bot](https://github.com/afdulfauzan/python-aria-mirror-bot) repositories.

# Supported Features:
- Mirror Torrents
- Mirror Direct Links
- Mirror Telegram Files
- Download and Upload Progress, Speeds and ETAs
- Docker Support
- Uploading To Team Drives
- Index Link Support
- Service Account Support
- Mirror 'youtube-dl' Supported Links

# Deploying:

## Prerequisites

- Install Python3 (debian based distros).
```
sudo apt install python3
```

- Install Docker by following the [Official Docker Docs](https://docs.docker.com/engine/install/debian/).

- Install dependencies for running the setup scripts.
```
pip3 install -r requirements-cli.txt
```

## Cloning and Setting Up Config File

- Clone the Repo:
```
git clone https://github.com/ksssomesh12/tg-mirror-bot
cd tg-mirror-bot
```

- Copy and Edit the Config file:
```
cp config_sample.env config.env
nano config.env
```

- Remove the first line saying:
```
_____REMOVE_THIS_LINE_____=True
```
Fill up rest of the fields. Meaning of each field are discussed below:
- **BOT_TOKEN** : The telegram bot token that you get from [@BotFather](https://t.me/botfather).
- **GDRIVE_FOLDER_ID** : This is the folder ID of the Google Drive folder to which you want to upload all the mirrors.
- **IS_TEAM_DRIVE** : (Optional field) Set to "true" if GDRIVE_FOLDER_ID is from a Team Drive else set to "false" or leave it empty.
- **DOWNLOAD_DIR** : The path to the folder where the downloads will be downloaded locally, before uploading to Google Drive.
- **OWNER_ID** : The Telegram user ID (not username) of the owner of the bot. DO NOT put this in double quotes.
- **DOWNLOAD_STATUS_UPDATE_INTERVAL** : A short interval of time in seconds after which the Mirror progress message is updated (5 seconds at least).
- **AUTO_DELETE_MESSAGE_DURATION** : Interval of time (in seconds), after which the bot deletes its message (and command message) which is expected to be viewed instantly. Note: Set to -1 to never automatically delete messages.
- **INDEX_URL** : (Optional field) Refer to [GDIndex repo](https://github.com/maple3142/GDIndex/). The URL should not have any trailing '/'.
- **TELEGRAM_API** : This is to authenticate to your telegram account for downloading Telegram files. You can get this from [here](https://my.telegram.org). DO NOT put this in double quotes.
- **TELEGRAM_HASH** : This is to authenticate to your telegram account for downloading Telegram files. You can get this from [here](https://my.telegram.org). DO put this in double quotes.
- **USE_SERVICE_ACCOUNTS**: (Optional field) (Leave empty if unsure) Whether to use service accounts or not. For this to work see  "Using service accounts" section below.
- **USER_SESSION_STRING** : Session string generated by running:
```
python3 generate_string_session.py
```
**NOTE:**
- You can limit the maximum concurrent downloads by changing the value of 'MAX_CONCURRENT_DOWNLOADS' in 'aria.sh'. By default, it's set to '3'.
- You can limit the maximum download speed by changing the value of 'MAX_DOWNLOAD_SPEED' in 'aria.sh'. By default, it's set to '0' (unlimited).

## Getting Google OAuth API Credential File

- Visit the [Google Cloud Console](https://console.developers.google.com/apis/credentials).
- Go to the OAuth Consent tab, fill it, and save.
- Go to the Credentials tab and click Create Credentials -> OAuth Client ID
- Choose Other and Create.
- Use the download button to download your credentials.
- Move that file to the root of mirror-bot, and rename it to 'credentials.json'.
- Visit [Google API page](https://console.developers.google.com/apis/library)
- Search for Drive and enable it if it is disabled.
- Finally, run the script to generate the token file (token.pickle) for Google Drive:
```
pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib
python3 generate_drive_token.py
```
## Deploying Locally using Docker

- Start Docker Daemon (skip if already running):
```
sudo dockerd
```
- Build Docker Image:
```
sudo docker build . -t tg-mirror-bot
```
- Run the Image:
```
sudo docker run tg-mirror-bot
```

# Deploy to Heroku Manually:

## Creating a Private Repo

- Create your GitHub account (skip if you already have one).
- Open [this link](https://github.com/new/import) to import this repo to a new repo that will be created and owned by you.
- Clone this existing repo to your new private repo by pasting [this repo's url](https://github.com/ksssomesh12/tg-mirror-bot) in the clone url field and enter any name for your repo (for our reference, your-private-repo).
- Clone your private repo locally and delete the '.gitignore' file:
```
git clone https://github.com/username/your-private-repo
cd your-private-repo
rm -rfv .gitignore
```

**NOTE:** Replace 'username' with your GitHub username and 'your-private-repo' with the name of your private repo. Authenticate into GitHub, if prompted. If you have enabled 2FA in GitHub, you may not be able to authenticate. In that case, create a Personal Access Token, with 'repo-only' access, under Developer Settings in GitHub Settings and use it in the password field to authenticate in git.
- Commit and push the changes made to the remote GitHub repo:
```
git add .
git commit -m "removed gitignore file"
git push -f origin master
```
- Now, set up the config file by following [this section](https://github.com/ksssomesh12/tg-mirror-bot#Cloning-and-Setting-Up-Config-File) and get the OAuth credential file by following [this section](https://github.com/ksssomesh12/tg-mirror-bot#Getting-Google-OAuth-API-Credential-File).

**NOTE:** The cloned repo's visibility must be set to private, as this method requires the sensitive files (credentials.json, config.env, token.pickle, authorized_chats.txt, log.txt) containing personal data to be in the repo to be successfully deployed on Heroku; setting repo's visibility to 'public' might compromise personal data. Alternatively, you can set up a git repo on your local machine by cloning this repo and sync it with Heroku Git remote repo.

## Creating a Heroku App

- Create a [free Heroku account](https://id.heroku.com/signup/login).
- Install Heroku CLI:
```
sudo snap install --classic heroku
```
- Login into your Heroku account:
```
heroku login
```
- Visit [Heroku Dashboard](https://dashboard.heroku.com) and create a new app with any name(for our reference, your-mirror-bot) and with any region you prefer, for your app to be served from.
- In the Deploy tab of your app dashboard, select  GitHub in 'Deployment Method' and connect and authorize your Heroku account to access your public and private repos on GitHub.
- Search and select your private repo to connect to your Heroku app.
- (Optional) In auto deploy section, select a repo branch (master) to auto deploy the app from, if any new commit is made to the selected branch on GitHub.
- In manual deploy section, select a repo branch (master) to deploy the app and hit the Deploy button.
- Wait for the build to finish and ignore any errors that popped up during the build process.
- Change the stack for the app using Heroku CLI:
```
heroku stack:set container --app your-mirror-bot
```
- Now, go to the manual deploy section in the Deploy tab of your Heroku app dashboard and hit the Deploy button.
- Check for any errors in the build process and wait until it completes.

## Run/Terminate the App
You can run/terminate the app by allocating/deallocating dynos to the app.

- Using Dashboard:
In the app dashboard, under resources tab, use the 'Edit dyno formation' button in Dynos section to change the working state of the app.

- Using CLI:

To Run:
```
heroku ps:scale worker=1 --app your-mirror-bot
```
To Terminate:
```
heroku ps:scale worker=0 --app your-mirror-bot
```
To Check Status:
```
heroku ps --app your-mirror-bot
```

# Bot Commands:
- **help** - To get the help message
- **mirror** - Mirror the provided link to Google Drive
- **unzipmirror** - Mirror the provided link and if the file is in archive format, it is extracted and then uploaded to Google Drive
- **tarmirror** - Mirror the provided link and upload in archive format (.tar) to Google Drive
- **watch** - Mirror through 'youtube-dl' to Google Drive
- **tarwatch** - Mirror through 'youtube-dl' and upload in archive format (.tar) to Google Drive
- **cancel** - Reply with this command to the source message, and the download will be cancelled
- **status** - Shows the status of all downloads and uploads in progress
- **list** - Searches the Google Drive folder for any matches with the search term and replies with the links of the matches found, if any
- **stats** - Shows the stats of the machine that the bot is hosted on
- **authorize** - Authorize a group chat or, a specific user to use the bot (can only be used by the bot owner)
- **log** - Sends the log file of the bot (can be used to analyse crash reports, if any)

**NOTE:** The above listed command descriptions can be copied and pasted in 'edit bot commands' section during editing with [@BotFather](https://t.me/botfather).

# Using service accounts for uploading to avoid user rate limit
For Service Account to work, you must set USE_SERVICE_ACCOUNTS="True" in config file or environment variables
Many thanks to [AutoRClone](https://github.com/xyou365/AutoRclone) for the scripts.

**NOTE:** Using service accounts is only recommended while uploading to a team drive.

## Generating Service Accounts
Step 1. Generate service accounts [What is service account](https://cloud.google.com/iam/docs/service-accounts)
---------------------------------
Let us create only the service accounts that we need.

**Warning:** abuse of this feature is not the aim of this project, and we do **NOT** recommend that you make a lot of projects, just one project and 100 sa allow you plenty of use, it is also possible that over abuse might get your projects banned by google.

**NOTE:** 1 service account can copy around 750 GB/day. 1 project can make 100 service accounts so that's 75 TB/day, for most users this should easily suffice.
```
python3 gen_sa_accounts.py --quick-setup 1 --new-only
```
A folder named accounts will be created which will contain keys for the service accounts

**NOTE:** If you have created SAs in past from this script, you can also just re-download the keys by running:
```
python3 gen_sa_accounts.py --download-keys project_id
```

### Add all the service accounts to the Team Drive
- Run:
```
python3 add_to_team_drive.py -d SharedTeamDriveSrcID
```

# 'youtube-dl' Authentication using '.netrc' file:
For using your premium accounts in youtube-dl, edit the netrc file (in the root directory of this repository) according to following format:
```
machine host login username password my_youtube_password
```
where host is the name of extractor (e.g. youtube, twitch). Multiple accounts of different hosts can be added each separated by a new line.
