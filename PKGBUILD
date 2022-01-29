# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=('libpamac' 'libpamac-snap-plugin' 'libpamac-flatpak-plugin')
pkgver=11.2.0
pkgrel=5
_commit=3947669ad41aaa1ebd9a8acb68624796f565a498
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('i686' 'pentium4' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL3')
depends=('glib2' 'json-glib' 'libsoup' 'dbus-glib' 'polkit' 'appstream-glib' 'libalpm.so>=13' 'pacman-mirrors>=4.9.1' 'git')
makedepends=('gettext' 'vala' 'meson' 'ninja' 'gobject-introspection' 'snapd' 'snapd-glib' 'flatpak' 'asciidoc')
replaces=('pamac-common')
options=(!emptydirs !strip)
source=(#https://gitlab.manjaro.org/applications/libpamac/-/archive/$pkgver/libpamac-$pkgver.tar.bz2)
        https://gitlab.manjaro.org/applications/libpamac/-/archive/$_commit/libpamac-$_commit.tar.bz2)
sha256sums=('25d6075302bd76ee213950391e4f1ed20956ed9ba72c257e52874bcf1709ea5d')

prepare() {
  mv $pkgname-$_commit $pkgname-$pkgver
}

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
  provides=('pamac-common')
  conflicts=('pamac-common')
  replaces=('pamac-common')  
  cd "$srcdir/libpamac-$pkgver"
  cd builddir
  DESTDIR="$pkgdir" meson install
  # remove pamac-snap
  rm "$pkgdir/usr/share/vala/vapi/pamac-snap.vapi"
  rm "$pkgdir/usr/include/pamac-snap.h"
  rm "$pkgdir/usr/lib/libpamac-snap.so"
  rm "$pkgdir/usr/lib/libpamac-snap.so.11"
  rm "$pkgdir/usr/lib/libpamac-snap.so.11.1"
  # remove pamac-flatpak
  rm "$pkgdir/usr/share/vala/vapi/pamac-flatpak.vapi"
  rm "$pkgdir/usr/include/pamac-flatpak.h"
  rm "$pkgdir/usr/lib/libpamac-flatpak.so"
  rm "$pkgdir/usr/lib/libpamac-flatpak.so.11"
  rm "$pkgdir/usr/lib/libpamac-flatpak.so.11.1"
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
  install -Dm755 "builddir/src/libpamac-snap.so.11.1" "$pkgdir/usr/lib/libpamac-snap.so.11.1"
  ln -sr "$pkgdir/usr/lib/libpamac-snap.so.11.1" "$pkgdir/usr/lib/libpamac-snap.so.11"
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
  install -Dm755 "builddir/src/libpamac-flatpak.so.11.1" "$pkgdir/usr/lib/libpamac-flatpak.so.11.1"
  ln -sr "$pkgdir/usr/lib/libpamac-flatpak.so.11.1" "$pkgdir/usr/lib/libpamac-flatpak.so.11"
  ln -sr "$pkgdir/usr/lib/libpamac-flatpak.so.11" "$pkgdir/usr/lib/libpamac-flatpak.so"
}
