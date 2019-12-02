
To compile `mesa` driver with `iris` support, use the command below:
`meson setup --wipe build-support/ -Dgallium-drivers=iris -Ddri-drivers=i965`
`ninja -C build-support/`
`sudo ninja -C build-support/ install`
