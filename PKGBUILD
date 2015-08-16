pkgname=lib32-mingw-w64-atkmm
pkgver=2.22.6
pkgrel=2
pkgdesc="C++ bindings for atk (mingw-w64, 32-bit)"
arch=(any)
url="http://gtkmm.sourceforge.net"
license=("LGPL")
makedepends=(mingw-w64-gcc mingw-w64-pkg-config)
depends=(mingw-w64-crt mingw-w64-libsigc++ mingw-w64-atk mingw-w64-glibmm)
options=(!libtool !strip !buildflags)
source=("http://ftp.gnome.org/pub/GNOME/sources/atkmm/${pkgver%.*}/atkmm-$pkgver.tar.xz")
md5sums=('30f8e34c92b14f17ba3029937b2f218a')

_architectures="i686-w64-mingw32"

_optimal_make_jobs() {
  if [ -r /proc/cpuinfo ]; then
    local core_count=$(grep -Fc processor /proc/cpuinfo)
  else
    local core_count=0
  fi
  if [ $core_count -gt 1 ]; then
    echo -n $[$core_count-1]
  else
    echo -n 1
  fi
}

build() {
  for _arch in ${_architectures}; do
    export CFLAGS="-O2 -pipe"
    export CXXFLAGS="$CFLAGS"
    export CPPFLAGS="$CPPFLAGS -D_REENTRANT"
    unset LDFLAGS
    mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    ${srcdir}/${pkgname#lib32-mingw-w64-}-${pkgver}/configure \
      --prefix=/usr/${_arch} \
      --build=$CHOST \
      --host=${_arch} \
      --enable-static \
      --enable-shared \
      --disable-documentation
    make -j$(_optimal_make_jobs)
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    make DESTDIR="$pkgdir" install
    find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*.bat' -o -name '*.def' -o -name '*.exp' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip -x
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
  done
}
