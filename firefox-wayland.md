1. Install `intel-media-driver`.  

2. Modify the following values in `about:config`:  

|Key|Value|
  |:---|---:|
  |security.sandbox.content.level|`0`|
  |gfx.webrender.all|`true`|
|media.ffmpeg.vaapi.enabled|`true`|
|media.ffvpx.enabled|`false`|
|media.rdd-vpx.enabled|`false`|

3. Run the below command from the shell:
```
MOZ_DISABLE_RDD_SANDBOX=1 MOZ_ENABLE_WAYLAND=1 firefox &
```
https://wiki.archlinux.org/title/Firefox#Hardware_video_acceleration
