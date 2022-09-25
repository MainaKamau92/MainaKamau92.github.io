---
layout: post
title:  "Running crons on an Android device"
date:   2022-09-25 15:52:47 +0300
categories: jekyll update
---
I recently started working with the Termux app. It provides some cool features that make it possible to do some nifty stuff on the Android that would normally require you to root your device. I'll provide a step-by-step way of running a cron using a simple example.

## Install Termux
First things first, download and install the Termux app on your device. You can find it on the Google play store but it'll probably be outdated a better alternative is advised on the Termux's team GitHub [page](https://github.com/termux/termux-app).

## Install dependencies 
Once the app is ready and running we need to install some dependencies for our cron action, these are:
- cronie
- termux-services

Install these by running the below command in the app.
`pkg install cronie termux-services`. Once done restart the app.

## Script to be executed
Now that we have housekeeping in order, we can proceed to create a script to test out the cron mechanism. From the `home` folder create a folder labelled `cron` and in it, a sh file labelled `cron_test.sh`. In the test file add the below contents:
```
#!/data/data/com.termux/files/usr/bin/bash
echo "Current datetime is `data`" >> cron_log.log
```
A simple script that logs the DateTime every time it gets executed.

## Add the cron schedule.
The final step is to add the cron schedule, the command for that is similar to the crontab unix command, run `crontab -e`, this should open the cron file using the default editor, add below cron schedule and point it to the location of your script:

- `* * * * * /data/path/to/script # this runs every minute` 

You can run a tail log to see the change in the `cron_log.log` file.