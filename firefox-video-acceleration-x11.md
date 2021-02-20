## How to enable X11 GPU acceleration in Firefox (intel)

#### Install the below in Debian:
```
sudo apt-get install i965-va-driver vainfo
```
#### Set the below in `about:config`:

If your CPU supports `AVC1/H264` video acceleration:
```
media.ffvpx.enabled = false
media.ffmpeg.vaapi.enabled = true
gfx.webrender.all = true
```

If your CPU supports `VP9` video acceleration:
```
gfx.webrender.all = true
gfx.webrender.compositor.force-enabled = true
media.ffmpeg.vaapi-drm-display.enabled = true
media.ffmpeg.vaapi.enabled = true
media.ffvpx.enabled = false
```

Install the `ffmpeg` package for your distro.  
Install `h264ify`or `enhanced-h264ify` Firefox extension.  
Untick what codecs you would like to use in `enhanced-h264ify` extension.  

Run `MOZ_X11_EGL=1 firefox`.  

Run this to debug: 
```
MOZ_X11_EGL=1 MOZ_LOG="PlatformDecoderModule:5" firefox 2>&1
```  
Use `sudo intel_gpu_top`, check `Video/0` field, it should display 8-10% utilisation.  
<pre><code>
          ENGINE      BUSY           MI_SEMA MI_WAIT
       Render/3D/0    11.15% |    |      0%      0%
         Blitter/0     0.00% |    |      0%      0%
           <b>Video/0    11.00%</b> |    |      0%      0%
    VideoEnhance/0     0.00% |    |      0%      0%      
  </pre></code>

More info:  
- [mastransky.wordpress.com](https://mastransky.wordpress.com/2020/09/29/firefox-81-on-fedora-with-va-api-webrtc-and-x11/)  
- [bugzilla.mozilla.org](https://bugzilla.mozilla.org/show_bug.cgi?id=1619523)  
- https://askubuntu.com/questions/1291279/ubuntu-20-10-firefox-82-intel-hd-500-vaapi-hardware-acceleration
