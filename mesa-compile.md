
To compile `mesa` driver with `iris` support, use the command below:
```
meson setup --wipe build-support/ -Dgallium-drivers=iris -Ddri-drivers=i965
ninja -C build-support/
sudo ninja -C build-support/ install
```

For `Ubuntu 16.04`, add the following repos to build latest `mesa`:
```
sudo add-apt-repository ppa:savoury1/build-tools
```
