### Build `curl` for Fedora with `http3` support
First, create a file `curl-build.sh`:
```bash
#!/usr/bin/env bash
set -euo pipefail

WORKDIR=${1:-$HOME/curl-http3}
PREFIX=${2:-/usr/local}

export PATH="${PREFIX}/bin:${PATH}"
export PKG_CONFIG_PATH="${PREFIX}/lib/pkgconfig:${PKG_CONFIG_PATH:-}"
export CMAKE_PREFIX_PATH="${PREFIX}:${CMAKE_PREFIX_PATH:-}"

echo
echo "→ Workspace: $WORKDIR"
echo "→ Prefix:    $PREFIX"
echo

sudo dnf install -y \
  gcc gcc-c++ git make autoconf automake libtool \
  pkgconf-pkg-config openssl-devel \
  brotli-devel zlib-ng-compat-devel libev-devel \
  libevent-devel c-ares-devel libpsl-devel \
  liburing-devel cmake meson ninja-build perl-core


rm -rf "$WORKDIR"
mkdir -p "$WORKDIR"
cd "$WORKDIR"

# 1. OpenSSL with QUIC
git clone --depth 1 https://github.com/openssl/openssl.git
cd openssl
./config --prefix="$PREFIX" --openssldir="$PREFIX/ssl" enable-quic shared
make -j"$(nproc)"
sudo make install_sw
cd "$WORKDIR"

# 2. nghttp3
git clone --recursive https://github.com/ngtcp2/nghttp3.git
cd nghttp3
git submodule update --init --recursive
cmake -G Ninja -S . -B build \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX="$PREFIX"
ninja -C build
sudo ninja -C build install
cd "$WORKDIR"

# 3. ngtcp2
git clone --recursive https://github.com/ngtcp2/ngtcp2.git
cd ngtcp2
cmake -G Ninja -S . -B build \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX="$PREFIX" \
  -DOPENSSL_ROOT_DIR="$PREFIX" \
  -DOPENSSL_LIBRARIES="$PREFIX/lib/libssl.so;$PREFIX/lib/libcrypto.so"
ninja -C build
sudo ninja -C build install
cd "$WORKDIR"

# 4. nghttp2
git clone --recursive https://github.com/nghttp2/nghttp2.git
cd nghttp2
cmake -G Ninja -S . -B build \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX="$PREFIX"
ninja -C build
sudo ninja -C build install
cd "$WORKDIR"

# 5. curl
git clone --depth 1 https://github.com/curl/curl.git
cd curl
autoreconf -fi
./configure \
  --prefix="$PREFIX" \
  --with-ssl="$PREFIX" \
  --with-nghttp2 \
  --with-ngtcp2 \
  --with-nghttp3 \
  --enable-alt-svc \
  --enable-http3
make -j"$(nproc)"
sudo make install

echo
echo "Done! Check: curl -V"
echo "Test HTTP/3: curl --http3 https://cloudflare-quic.com"
```
Make the file executable and run it:
```bash
chmod +x curl-build.sh
./curl-build.sh
```
If compile is successful, add the compiled shared libraries to the shared libs config file:
```bash
echo '/usr/local/lib64' | sudo tee /etc/ld.so.conf.d/usr-local-lib64.conf; sudo ldconfig
```
Run `curl -V` to check, from a new shell:
```
curl 8.16.0-DEV (x86_64-pc-linux-gnu) libcurl/8.16.0-DEV OpenSSL/3.6.0 zlib/1.3.1.zlib-ng brotli/1.1.0 zstd/1.5.7 libpsl/0.21.5 nghttp2/1.66.90 ngtcp2/1.15.0-DEV nghttp3/1.1
Release-Date: [unreleased]
Protocols: dict file ftp ftps gopher gophers http https imap imaps ipfs ipns mqtt pop3 pop3s rtsp smb smbs smtp smtps telnet tftp ws wss
Features: alt-svc AsynchDNS brotli HSTS HTTP2 HTTP3 HTTPS-proxy IPv6 Largefile libz NTLM PSL SSL threadsafe TLS-SRP UnixSockets zstd
```
Test `http3` protocol with:
```bash
curl --http3 https://cloudflare-quic.com
```
Files will be build in your home folder; to rebuild, run:
```bash
rm -rf ~/curl-http3/; ./curl-build.sh
```
