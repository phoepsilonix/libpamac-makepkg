# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=('libpamac' 'libpamac-snap-plugin' 'libpamac-flatpak-plugin')
pkgver=11.5.5+1+g4dbc56a
pkgrel=3
_sover=11.5
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('x86_64' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL3')
depends=('appstream-glib' 'dbus-glib' 'git' 'glib2' 'json-glib' 'libalpm.so'
         'libsoup3' 'pacman-mirrors' 'polkit')
makedepends=('asciidoc' 'flatpak' 'gobject-introspection' 'meson' 'snapd'
             'snapd-glib' 'vala')
_commit=4dbc56aaf7d16f30761d859f8d7cb59f9f9e03f1  # tags/11.5.5^0
source=("git+https://gitlab.manjaro.org/applications/libpamac.git#commit=$_commit"
        'partly-reverse-889aa1d7.patch')
sha256sums=('SKIP'
            '073b879b528f9acbd52e7a5a66a2fb401f1608ac1697f3064cad40d6b9b2e7f0')

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
  patch -p1 -i ../partly-reverse-889aa1d7.patch
}

build() {
  arch-meson "$pkgbase" build \
    -Denable-snap=true \
    -Denable-flatpak=true
  meson compile -C build
}

package_libpamac() {
  backup=('etc/pamac.conf')
  install="$pkgname.install"
  provides=('libpamac.so=11' 'pamac-common')
  replaces=('pamac-common')

  meson install -C build --destdir "$pkgdir"

  cd "$srcdir/$pkgbase"

  # remove libpamac-snap
  rm "$pkgdir/usr/share/vala/vapi/pamac-snap.vapi"
  rm "$pkgdir/usr/include/pamac-snap.h"
  rm "$pkgdir/usr/lib/$pkgbase-snap".{so,so.*}

  # remove libpamac-flatpak
  rm "$pkgdir/usr/share/vala/vapi/pamac-flatpak.vapi"
  rm "$pkgdir/usr/include/pamac-flatpak.h"
  rm "$pkgdir/usr/lib/$pkgbase-flatpak".{so,so.*}
}

package_libpamac-snap-plugin() {
  pkgdesc="Snap plugin for Pamac"
  depends=('snapd' 'snapd-glib' 'libpamac')
  provides=('libpamac-snap.so=11' 'pamac-snap-plugin')
  replaces=('pamac-snap-plugin')

  install -Dm644 "build/src/pamac-snap.vapi" \
    "$pkgdir/usr/share/vala/vapi/pamac-snap.vapi"
  install -Dm644 "build/src/pamac-snap.h" \
    "$pkgdir/usr/include/pamac-snap.h"
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

  install -Dm644 "build/src/pamac-flatpak.vapi" \
    "$pkgdir/usr/share/vala/vapi/pamac-flatpak.vapi"
  install -Dm644 "build/src/pamac-flatpak.h" \
    "$pkgdir/usr/include/pamac-flatpak.h"
  install -Dm644 "build/src/$pkgbase-flatpak.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-flatpak.so.${_sover}"

  create_links
}
