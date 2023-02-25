# Download Yle Areena/YouTube media file and upload it to cloud storage

## Introduction

This repo contains two GitHub Actions Workflow files. 

One of those (_areena_download_and_store.yml_) is used to download Yle Areena media file and upload it to cloud storage. 

Another one (_youtube_download_and_store.yml_) does the same thing for a YouTube video.

### Yle Areena functionality

 If that Yle Areena specific GitHub action is run, it 
1. takes as a parameter an Areena program URL (e.g. https://areena.yle.fi/1-50250719), 
1. clones [yle-dl](https://github.com/aajanki/yle-dl.git) repo and checkouts branch/tag/commit specified in environment variable CHECKOUT_REF (default value = master),
1. installs yle-dl locally using pip,
1. downloads and installs required dependencies (ffmpeg),
1. downloads the Areena program that was given as a parameter,
1. installs mkvtoolnix and uses *extract_subtitles.sh* script to extract subtitles as individual srt files,
1. installs rclone and writes its configuration file (*.rclone.conf*) using value from environment variable RCLONE_CONF (which refers to a repo secret called RCLONE_CONF),
1. uploads the downloaded media file (and its possible srt files) to cloud storage using rclone

### YouTube functionality

If that YouTube specific GitHub action is run, it 
1. takes as a parameter:
* YouTube video URL (e.g. https://www.youtube.com/watch?v=U7-dxzp6Jvs), 
* quality and formation selection parameters for yt-dlp ([see more info here](https://github.com/yt-dlp/yt-dlp#format-selection)),
* subtitle selection and download specific parameters for yt-dlp ([see more info here](https://github.com/yt-dlp/yt-dlp#subtitle-options)),
2. installs yt-dlp using pip,
1. downloads and installs required dependencies (ffmpeg),
1. downloads the YouTube video (and its possible subtitles) that was given as a parameter,
1. installs rclone and writes its configuration file (*.rclone.conf*) using value from environment variable RCLONE_CONF (which refers to a repo secret called RCLONE_CONF),
1. uploads the downloaded media file (and its possible subtitle files) to cloud storage using rclone

## Configuration

Configuration is done via environment variables and repo secrets

* CHECKOUT_REF
  * Parameter for _yle-dl_ git checkout (branch/tag/commit reference). This is included in case the current master branch is buggy, and you need to checkout e.g. the last working tag.
  * Default value = **master**

* RCLONE_CONF
  * Refers to a repo secret called RCLONE_CONF
  * Configure rclone locally to use your preferred cloud storage, then copy the content of *.rclone.conf* to a repo secret called RCLONE_CONF

* RCLONE_REMOTE_REF
  * Remote target (cloud storage) reference in your .rclone.conf file
  * Make sure to name your rclone remote storage reference as "remote" during rclone configuration, or change the default value.
  * Default value = **remote**
  
* RCLONE_REMOTE_CLOUD_PATH
  * Path in the cloud storage where the video file will be uploaded
  * Default value = **media_dls**

* RCLONE_OPTS: 
  * Additional parameters for rclone
  * Default value = **-P --create-empty-src-dirs**  (i.e. progress indicator, create empty dirs on remote storage)

## yle-dl configuration

When downloading media files, yle-dl uses parameters specified in the config file [.yledl.conf](.yledl.conf). Feel free to modify it to your liking.

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

That config is for OneDrive, but if you're using another cloud storage, your config probably looks different.
 

