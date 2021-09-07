Install `intel-media-driver`.  

|Key|Value|
|:---|---:|
|security.sandbox.content.level|`0`|
|gfx.webrender.all|`true`|
|media.ffmpeg.vaapi.enabled|`true`|
|media.ffvpx.enabled|`false`|
|media.rdd-vpx.enabled|`false`|

```
security.sandbox.content.level = 0
gfx.webrender.all = true
media.ffmpeg.vaapi.enabled = true
media.ffvpx.enabled = false
media.rdd-vpx.enabled = false
```
```
MOZ_DISABLE_RDD_SANDBOX=1 MOZ_ENABLE_WAYLAND=1 firefox &
```
https://wiki.archlinux.org/title/Firefox#Hardware_video_acceleration
