
To compile `mesa` driver with `iris` support, use the command below:
```
sudo meson setup --wipe build-support/ -Dgallium-drivers=iris -Ddri-drivers=i965
sudo ninja -C build-support/
sudo ninja -C build-support/ install
```

For `Ubuntu 16.04`, add the following repos to build latest `mesa`:
```
sudo add-apt-repository ppa:savoury1/build-tools
```

Add the following line to ```/etc/environment```:

```
MESA_LOADER_DRIVER_OVERRIDE=iris
```

To test from command line, use the below:
```
export MESA_LOADER_DRIVER_OVERRIDE=iris
glxinfo |grep OpenGL
```
To revert to old intel driver, use below:
```
unset MESA_LOADER_DRIVER_OVERRIDE
```
Links for reference:
https://www.mesa3d.org/install.html

https://lists.freedesktop.org/archives/mesa-dev/2019-February/215576.html
