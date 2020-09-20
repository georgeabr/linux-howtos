### Set the below in `about:config`:

```
media.ffvpx.enabled = false

media.ffmpeg.vaapi-drm-display.enabled = true
media.ffmpeg.vaapi.enabled = true
widget.wayland-dmabuf-vaapi.enabled = true
gfx.webrender.all = true
```

Install `h264ify`or `enhanced-h264ify` Firefox extension.  
Run `MOZ_X11_EGL=1 firefox`.  
Run this to debug: `MOZ_X11_EGL=1 MOZ_LOG="PlatformDecoderModule:5" firefox` 2>&1  

More info:
```
https://bugzilla.mozilla.org/show_bug.cgi?id=1619523
```
