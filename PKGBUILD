# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=('libpamac' 'libpamac-snap-plugin' 'libpamac-flatpak-plugin')
#         'libpamac-aur-plugin' 'libpamac-appstream-plugin')
pkgver=11.6.2+6+ge74fe0e
pkgrel=3
_sover=11.6
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('x86_64' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL3')
depends=('dbus-glib' 'git' 'glib2' 'json-glib' 'libalpm.so'
         'libsoup3' 'pacman-mirrors' 'polkit')
makedepends=('asciidoc' 'flatpak' 'gobject-introspection' 'meson' 'snapd'
             'snapd-glib' 'vala' 'appstream')
options=('debug')
_commit=e74fe0e1c15f4fd14d02ff12650be3fde47287d7  # branch/master
source=("git+https://gitlab.manjaro.org/applications/libpamac.git#commit=$_commit"
        meson.patch appstream-1.0.patch)
sha256sums=('SKIP'
            '7d0401199ddae978f32f4180afaf920b213059e0300af66ca2fc9cef1d04db0e'
            'bcfe09aada61907b7bc037ba0440dcc3db3ac9c814148c0495b6b725bf5f8d10')

create_links() {
  # create soname links
  find "$pkgdir" -type f -name '*.so*' ! -path '*xorg/*' -print0 | while read -d $'\0' _lib; do
      _soname=$(dirname "${_lib}")/$(readelf -d "${_lib}" | grep -Po 'SONAME.*: \[\K[^]]*' || true)
      _base=$(echo ${_soname} | sed -r 's/(.*)\.so.*/\1.so/')
      [[ -e "${_soname}" ]] || ln -s $(basename "${_lib}") "${_soname}"
      [[ -e "${_base}" ]] || ln -s $(basename "${_soname}") "${_base}"
  done
}

pkgver() {
  cd "$srcdir/$pkgbase"
  git describe --tags | sed 's/^v//;s/-/+/g'
}

prepare() {
  cd "$srcdir/$pkgbase"
  patch -p1 -i ../meson.patch
  patch -p1 -i ../appstream-1.0.patch
}

build() {
  arch-meson "$pkgbase" build \
    -Denable-appstream=true \
    -Denable-snap=true \
    -Denable-flatpak=true
  meson compile -C build
}

package_libpamac() {
  backup=('etc/pamac.conf')
  install="$pkgname.install"
  depends+=('appstream')
  provides=('libpamac.so=11' 'pamac-common'
            'libpamac-appstream.so=11' 'libpamac-appstream-plugin'
            'libpamac-aur.so=11' 'libpamac-aur-plugin')
  conflicts=('libpamac-aur-plugin' 'libpamac-appstream-plugin')
  replaces=('pamac-common')

  meson install -C build --destdir "$pkgdir"

  cd "$srcdir/$pkgbase"

  # remove libpamac-snap
  rm "$pkgdir/usr/lib/$pkgbase-snap".{so,so.*}

  # remove libpamac-flatpak
  rm "$pkgdir/usr/lib/$pkgbase-flatpak".{so,so.*}

  # remove libpamac-appstream
  #rm "$pkgdir/usr/lib/$pkgbase-appstream".{so,so.*}
 
  # remove libpamac-aur
  #rm "$pkgdir/usr/lib/$pkgbase-aur".{so,so.*}
}

package_libpamac-snap-plugin() {
  pkgdesc="Snap plugin for Pamac"
  depends=('snapd' 'snapd-glib' 'libpamac')
  provides=('libpamac-snap.so=11' 'pamac-snap-plugin')
  replaces=('pamac-snap-plugin')

  install -Dm644 "build/src/$pkgbase-snap.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-snap.so.${_sover}"

  create_links
}

package_libpamac-flatpak-plugin() {
  pkgdesc="Flatpak plugin for Pamac"
  depends=('flatpak' 'libpamac')
  provides=('libpamac-flatpak.so=11' 'pamac-flatpak-plugin')
  replaces=('pamac-flatpak-plugin')
  install="$pkgname.install"

  install -Dm644 "build/src/$pkgbase-flatpak.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-flatpak.so.${_sover}"

  create_links
}

package_libpamac-appstream-plugin() {
  pkgdesc="Appstream plugin for Pamac"
  depends=('appstream' 'libpamac')
  provides=('libpamac-appstream.so=11')

  install -Dm644 "build/src/$pkgbase-appstream.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-appstream.so.${_sover}"

  create_links
}

package_libpamac-aur-plugin() {
  pkgdesc="AUR plugin for Pamac"
  depends=('libpamac' 'base-devel')
  provides=('libpamac-aur.so=11')

  install -Dm644 "build/src/$pkgbase-aur.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-aur.so.${_sover}"

  create_links
}
