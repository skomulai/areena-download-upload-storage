# Download Yle Areena program and upload it to a cloud storage

## Introduction

This repo contains a GitHub Actions Workflow file. If that action is run, it 
1. takes as a parameter an Areena program URL (e.g. https://areena.yle.fi/1-50250719), 
1. clones [yle-dl](https://github.com/aajanki/yle-dl.git) repo and checkouts branch/tag/commit specified in environment variable CHECKOUT_REF (default value = master)
1. installs yle-dl locally using pip,
1. downloads and installs required dependencies (ffmpeg),
1. installs rclone and writes its configuration file (*.rclone.conf*) using value from environment variable RCLONE_CONF (which refers to a repo secret called RCLONE_CONF)
1. downloads an Areena program that was given as a parameter
1. uploads the downloaded video file to a cloud storage using rclone

## Configuration

Configuration is done via environment variables and repo secrets

* CHECKOUT_REF
  * Parameter for git checkout (branch/tag/commit reference). This is included in case the current master branch is buggy, and you need to checkout e.g. the last working tag.
  * Default value = **master**

* YLE_DL_OPTS:
  * Additional parameters for yle-dl
  * Default value = **--vfat --sublang fin --maxbitrate best**  (i.e. Windows-compatible filenames, Finnish subtitles, best quality)

* RCLONE_CONF
  * Refers to a repo secret called RCLONE_CONF
  * Configure rclone locally to use your preferred cloud storage, then copy the content of *.rclone.conf* to a repo secret called RCLONE_CONF

* RCLONE_REMOTE_REF
  * Remote target (cloud storage) reference in your .rclone.conf file
  * Make sure to name your rclone remote storage reference as "remote" during rclone configuration, or change the default value.
  * Default value = **remote**
  
* RCLONE_REMOTE_CLOUD_PATH
  * Path in the cloud storage where the video file will be uploaded
  * Default value = **areena_dls**

* RCLONE_OPTS: 
  * Additional parameters for rclone
  * Default value = **-P --create-empty-src-dirs**  (i.e. progress indicator, create empty dirs on remote storage)

## rclone configuration

See https://rclone.org/commands/rclone_config/

Your *.rclone.conf* (or *rclone.conf* in Windows) should look like this:

```config
[remote]
type = <<.e.g onedrive>>
token = {<<accesstoken>>}
drive_id = <<12345678>>
drive_type = personal
```

That is for OneDrive, so if you're using another cloud storage, your config may look different.
 

