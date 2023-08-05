# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=('libpamac' 'libpamac-snap-plugin' 'libpamac-flatpak-plugin')
pkgver=11.6.0
pkgrel=0
_sover=11.6
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('x86_64' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL3')
depends=('appstream' 'dbus-glib' 'git' 'glib2' 'json-glib' 'libalpm.so'
         'libsoup3' 'pacman-mirrors' 'polkit')
makedepends=('asciidoc' 'flatpak' 'gobject-introspection' 'meson' 'snapd'
             'snapd-glib' 'vala')
options=('debug')
_commit=88fd49cbf6c8769ea92ca119e96c36cb21781748  # tags/11.6.0^0
source=("git+https://gitlab.manjaro.org/applications/libpamac.git#commit=$_commit")
sha256sums=('SKIP')

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
  rm "$pkgdir/usr/lib/$pkgbase-snap".{so,so.*}

  # remove libpamac-flatpak
  rm "$pkgdir/usr/lib/$pkgbase-flatpak".{so,so.*}
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
