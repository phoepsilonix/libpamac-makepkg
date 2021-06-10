# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=('libpamac' 'libpamac-snap-plugin' 'libpamac-flatpak-plugin')
pkgver=11.0.0
pkgrel=3
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('i686' 'pentium4' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL3')
depends=('glib2' 'json-glib' 'libsoup' 'dbus-glib' 'polkit' 'appstream-glib' 'libalpm.so>=13' 'pacman-mirrors>=4.9.1' 'git')
makedepends=('gettext' 'vala' 'meson' 'ninja' 'gobject-introspection' 'snapd' 'snapd-glib' 'flatpak' 'asciidoc')
options=(!emptydirs !strip)

source=(https://gitlab.manjaro.org/applications/libpamac/-/archive/$pkgver/libpamac-$pkgver.tar.bz2)
sha256sums=('d4fa2fadffac7a811f9708d724797833377986d2f8c1bf9cc6a24f301783dc78')

build() {
  cd $pkgname-$pkgver
  mkdir -p builddir
  cd builddir
  meson setup --prefix=/usr \
              --sysconfdir=/etc \
              -Denable-snap=true \
              -Denable-flatpak=true \
              --buildtype=release
  meson compile
}

package_libpamac() {
  optdepends=('libpamac-snap-plugin' 'libpamac-flatpak-plugin' 'archlinux-appstream-data')
  backup=('etc/pamac.conf')
  install=libpamac.install
  cd "$srcdir/libpamac-$pkgver"
  cd builddir
  DESTDIR="$pkgdir" meson install
  # remove pamac-snap
  rm "$pkgdir/usr/share/vala/vapi/pamac-snap.vapi"
  rm "$pkgdir/usr/include/pamac-snap.h"
  rm "$pkgdir/usr/lib/libpamac-snap.so"
  rm "$pkgdir/usr/lib/libpamac-snap.so.11"
  # remove pamac-flatpak
  rm "$pkgdir/usr/share/vala/vapi/pamac-flatpak.vapi"
  rm "$pkgdir/usr/include/pamac-flatpak.h"
  rm "$pkgdir/usr/lib/libpamac-flatpak.so"
  rm "$pkgdir/usr/lib/libpamac-flatpak.so.11"
}

package_libpamac-snap-plugin() {
  pkgdesc="Snap plugin for Pamac"
  depends=('snapd' 'snapd-glib' 'libpamac')
  provides=('pamac-snap-plugin')
  conflicts=('pamac-snap-plugin')
  replaces=('pamac-snap-plugin')
  cd "$srcdir/libpamac-$pkgver"
  install -Dm644 "builddir/src/pamac-snap.vapi" "$pkgdir/usr/share/vala/vapi/pamac-snap.vapi"
  install -Dm644 "builddir/src/pamac-snap.h" "$pkgdir/usr/include/pamac-snap.h"
  install -Dm755 "builddir/src/libpamac-snap.so.11" "$pkgdir/usr/lib/libpamac-snap.so.11"
  ln -sr "$pkgdir/usr/lib/libpamac-snap.so.11" "$pkgdir/usr/lib/libpamac-snap.so"
}

package_libpamac-flatpak-plugin() {
  pkgdesc="Flatpak plugin for Pamac"
  depends=('flatpak' 'libpamac')
  provides=('pamac-flatpak-plugin')
  conflicts=('pamac-flatpak-plugin')
  replaces=('pamac-flatpak-plugin')
  install=libpamac-flatpak-plugin.install
  cd "$srcdir/libpamac-$pkgver"
  install -Dm644 "builddir/src/pamac-flatpak.vapi" "$pkgdir/usr/share/vala/vapi/pamac-flatpak.vapi"
  install -Dm644 "builddir/src/pamac-flatpak.h" "$pkgdir/usr/include/pamac-flatpak.h"
  install -Dm755 "builddir/src/libpamac-flatpak.so.11" "$pkgdir/usr/lib/libpamac-flatpak.so.11"
  ln -sr "$pkgdir/usr/lib/libpamac-flatpak.so.11" "$pkgdir/usr/lib/libpamac-flatpak.so"
}

#vim:set ts=2 sw=2 et:
