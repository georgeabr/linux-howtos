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
