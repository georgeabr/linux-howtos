### Set the below in `about:config`:

```
media.ffvpx.enabled = false
media.ffmpeg.vaapi.enabled = true
gfx.webrender.all = true
```

Install `h264ify`or `enhanced-h264ify` Firefox extension.  
Install the `ffmpeg` package for your distro.  

Run `MOZ_X11_EGL=1 firefox`.  

Run this to debug: `MOZ_X11_EGL=1 MOZ_LOG="PlatformDecoderModule:5" firefox` 2>&1  
Use `intel_gpu_top`, check `Video/0` field, it should display 8-10% utilisation.  
<pre><code>

          ENGINE      BUSY           MI_SEMA MI_WAIT
       Render/3D/0    11.15% |    |      0%      0%
         Blitter/0     0.00% |    |      0%      0%
           <b>Video/0    11.00%</b> |    |      0%      0%
    VideoEnhance/0     0.00% |    |      0%      0%      
  </pre></code>

More info:
```
https://bugzilla.mozilla.org/show_bug.cgi?id=1619523
```
