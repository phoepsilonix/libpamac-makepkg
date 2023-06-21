# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=('libpamac' 'libpamac-snap-plugin' 'libpamac-flatpak-plugin')
pkgver=11.5.4
pkgrel=2
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('x86_64' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL3')
depends=('appstream-glib' 'dbus-glib' 'git' 'glib2' 'json-glib' 'libalpm.so'
         'libsoup3' 'pacman-mirrors' 'polkit')
makedepends=('asciidoc' 'flatpak' 'gobject-introspection' 'meson' 'snapd'
             'snapd-glib' 'vala')
_commit=99b67d6955bbd0ca7067263c8a5bd8f5a774f46f  # tags/11.5.4^0
source=("git+https://gitlab.manjaro.org/applications/libpamac.git#commit=$_commit")
sha256sums=('SKIP')

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
  install -Dm644 "build/src/$pkgbase-snap.so" \
    "$pkgdir/usr/lib/$pkgbase-snap.so"
  install -Dm644 "build/src/$pkgbase-snap.so.11" \
    "$pkgdir/usr/lib/$pkgbase-snap.so.11"
  install -Dm644 "build/src/$pkgbase-snap.so.11.5" \
    "$pkgdir/usr/lib/$pkgbase-snap.so.11.5"
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
  install -Dm644 "build/src/$pkgbase-flatpak.so" \
    "$pkgdir/usr/lib/$pkgbase-flatpak.so"
  install -Dm644 "build/src/$pkgbase-flatpak.so.11" \
    "$pkgdir/usr/lib/$pkgbase-flatpak.so.11"
  install -Dm644 "build/src/$pkgbase-flatpak.so.11.5" \
    "$pkgdir/usr/lib/$pkgbase-flatpak.so.11.5"
}
