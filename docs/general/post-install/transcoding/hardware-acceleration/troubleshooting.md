---
uid: admin-hardware-acceleration-troubleshooting
title: Hardware Acceleration Troubleshooting
---
# Hardware Acceleration Troubleshooting
This guide aims to give server administrators a quick and accessible way to diagnose and resolve issues with hardware acceleration. Please also feel encouraged to engage with our community troubleshooting channels on [Matrix and Discord](https://jellyfin.org/contact), or join the [forum](https://forum.jellyfin.org/f-troubleshooting).

## How do I know if my hardware acceleration is working?

Each GPU vendor and OS has its own way of checking for active sessions, and you may refer to the [verification section for your chosen platform first](https://jellyfin.org/docs/general/post-install/transcoding/hardware-acceleration/#configure--verify-hardware-acceleration), but this is offered as a GPU and OS agnostic way to verify.

### Iniating a HWA playback session
To begin, play any video in the web player, then select the settings cog in the bottom right of the player interface. Select "Playback Info". A semi-transparent window will open displaying the current playback session information. Look for the field labled "Play method". If you see anything other than "Transcoding", use the settings cog again, and this time select "Quality", and select different qualities until the Play method shows as "Transcoding". Allow the video to play like this for around 10 seconds, then close the playback.

### Verifying with FFmpeg logs
Navigate to the server dashboard > logs. You may see many entries here, but we only care about one. It will have a title in format ``ffmpeg.Transcode-$timestamp-uuid.log``, where $timestamp is a date and time that matches with the time you hit the play button on the video. As an example "FFmpeg.Transcode-2025-08-06_18-46-46_17f445791d3053c2ceadc5487a2f1205_7bfcd931.log" represents a playback started August 6th, 2025, at 18:46, or 6:46PM.

:::note

The timestamp of the log will be tied to your system or container timezone. If you see unusual dates and hours, but the minutes match up, you likely need to correct the entry on your host system or container definition

:::

Open the file, and search for the term ``Stream mapping:``. There are two possibilities here. If the line beneath this contains the words "setparams:default", you need to refer to one further line down, otherwise, take the line as is.

This line will contain the video encoder used. As an example, in this log ``stream #0:0 -> #0:0 (hevc (native) -> av1 (av1_qsv))``, the "qsv" refers to Intel Quick Sync Video, which indicates that hardware acceleration is working. The rest of the information on this line is not important for verifying hardware acceleration. The following entries indicate working HWA:
