# Maintainer: Alexander F. Rødseth <xyproto@archlinux.org>
# Contributor: loqs
# Contributor: Jorge Araya Navarro <jorgejavieran@yahoo.com.mx>
# Contributor: Cristian Porras <porrascristian@gmail.com>
# Contributor: Matthew Bentley <matthew@mtbentley.us>

pkgname=godot-mine
pkgver=3.4.2
pkgrel=2.1
pkgdesc='Advanced cross-platform 2D and 3D game engine'
url='https://godotengine.org'
license=(MIT)
arch=(x86_64)
makedepends=(gcc scons yasm alsa-lib pulseaudio)
depends=(embree freetype2 libglvnd libtheora libvorbis libvpx libwebp
         libwslay libxcursor libxi libxinerama libxrandr mbedtls miniupnpc opusfile)
optdepends=(alsa-lib pulseaudio)
provides=('godot')
conflicts=('godot')
source=("$pkgname-$pkgver.tar.gz::https://github.com/godotengine/godot/archive/$pkgver-stable.tar.gz"
        "enable_fwidth_gles2.patch")
b2sums=('6bdc40acda57b038ef9fe4b579ac070001a09b4309a288859a5bb1a35b664257941a832c42d11eda6e3837c65689d6e96b4adf9131b6e040661a2b26c19a7d43'
        '9ffe16a9aca4e750716a51e593e5b1d13e33cf897afb9dc41a55b11bba71ae2eb5bcf21725fe9d9bdb499f75232b114dac80ee75de477d572e47c05f9c9e62c0')
prepare() {
  mv godot-$pkgver-stable $pkgname-$pkgver-stable
  # Disable the check that adds -no-pie to LINKFLAGS, for gcc != 6
  sed -i 's,0] >,0] =,g' $pkgname-$pkgver-stable/platform/x11/detect.py

  patch --strip=1 --directory=$pkgname-$pkgver-stable --input="$srcdir/enable_fwidth_gles2.patch"
}

build() {
  # Not unbundled (yet):
  #  bullet (FS#72924, https://github.com/godotengine/godot/issues/55599)
  #  certs (FS#72762)
  #  enet (contains no upstreamed IPv6 support)
  #  libsquish, recast, xatlas
  #  AUR: libwebm, squish
  local to_unbundle="embree freetype libogg libpng libtheora libvorbis libvpx libwebp mbedtls miniupnpc opus pcre2 wslay zlib zstd"
  local system_libs=""
  for _lib in $to_unbundle; do
    system_libs+="builtin_"$_lib"=no "
    rm -rf thirdparty/$_lib
  done

  cd $pkgname-$pkgver-stable
  export BUILD_NAME=arch_linux_mine_$pkgrel
  scons -j16 \
    bits=64 \
    colored=yes \
    platform=x11 \
    pulseaudio=yes \
    system_certs_path=/etc/ssl/certs/ca-certificates.crt \
    target=release_debug \
    tools=yes \
    use_llvm=no \
    CFLAGS="$CFLAGS -fPIC -Wl,-z,relro,-z,now" \
    CXXFLAGS="$CXXFLAGS -fPIC -Wl,-z,relro,-z,now" \
    LINKFLAGS="$LDFLAGS" \
    $system_libs
}

package() {
  cd $pkgname-$pkgver-stable
  install -Dm644 misc/dist/linux/org.godotengine.Godot.desktop \
    "$pkgdir/usr/share/applications/godot.desktop"
  install -Dm644 icon.svg "$pkgdir/usr/share/pixmaps/godot.svg"
  install -Dm755 bin/godot.x11.opt.tools.64 "$pkgdir/usr/bin/$pkgname"
  install -Dm644 LICENSE.txt "$pkgdir/usr/share/licenses/godot/LICENSE"
  install -Dm644 misc/dist/linux/godot.6 "$pkgdir/usr/share/man/man6/godot.6"
  install -Dm644 misc/dist/linux/org.godotengine.Godot.xml \
    "$pkgdir/usr/share/mime/packages/org.godotengine.Godot.xml"
}
