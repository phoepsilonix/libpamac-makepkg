# Maintainer: Mark Wagie <mark at manjaro dot org>
# Maintainer: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult

pkgbase=libpamac
pkgname=(
  'libpamac'
  'libpamac-snap-plugin'
  'libpamac-flatpak-plugin'
#  'libpamac-aur-plugin'
#  'libpamac-appstream-plugin'
)
pkgver=11.7.3
pkgrel=3
_sover=11.7
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('x86_64' 'aarch64')
url="https://github.com/manjaro/libpamac"
license=('GPL-3.0-or-later')
depends=(
  'appstream'
  'dbus-glib'
  'git'
  'glib2'
  'json-glib'
  'libalpm.so=15'
  'libsoup3'
  'pacman-mirrors'
  'polkit'
)
makedepends=(
  'appstream'
  'asciidoc'
  'flatpak'
  'git'
  'gobject-introspection'
  'meson'
  'snapd'
  'snapd-glib'
  'vala'
)
options=('debug')
source=("git+https://github.com/manjaro/libpamac.git#tag=$pkgver")
sha256sums=('7a0e6abfa5f1ea1f1530301566aacb0acffaf95d93d36a6811dcd874460ca57d')

create_links() {
  # create soname links
  find "$pkgdir" -type f -name '*.so*' ! -path '*xorg/*' -print0 | while read -d $'\0' _lib; do
      _soname=$(dirname "${_lib}")/$(readelf -d "${_lib}" | grep -Po 'SONAME.*: \[\K[^]]*' || true)
      _base=$(echo ${_soname} | sed -r 's/(.*)\.so.*/\1.so/')
      [[ -e "${_soname}" ]] || ln -s $(basename "${_lib}") "${_soname}"
      [[ -e "${_base}" ]] || ln -s $(basename "${_soname}") "${_base}"
  done
}

prepare() {
  cd "$pkgbase"

  # https://github.com/manjaro/libpamac/issues/3
  git cherry-pick -n 087af94264cf1e79a809a2f5dbb570a617e09d45
}

build() {
  arch-meson "$pkgbase" build \
    -Denable-appstream=true \
    -Denable-snap=true \
    -Denable-flatpak=true
  meson compile -C build
}

package_libpamac() {
  provides=(
    'libpamac.so=11'
    'pamac-common'
    'libpamac-appstream.so=11'
    'libpamac-appstream-plugin'
    'libpamac-aur.so=11'
    'libpamac-aur-plugin'
  )
  conflicts=(
    'libpamac-aur-plugin'
    'libpamac-appstream-plugin'
  )
  replaces=(
    'pamac-common'
  )
  backup=('etc/pamac.conf')
  install="$pkgname.install"

  meson install -C build --no-rebuild --destdir "$pkgdir"

  cd "$pkgbase"

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
  depends=(
    'libpamac'
    'snapd'
    'snapd-glib'
  )
  provides=(
    'libpamac-snap.so=11'
    'pamac-snap-plugin'
  )
  replaces=('pamac-snap-plugin')

  install -Dm644 "build/src/$pkgbase-snap.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-snap.so.${_sover}"

  create_links
}

package_libpamac-flatpak-plugin() {
  pkgdesc="Flatpak plugin for Pamac"
  depends=(
    'flatpak'
    'libpamac'
  )
  provides=(
    'libpamac-flatpak.so=11'
    'pamac-flatpak-plugin'
  )
  replaces=(
    'pamac-flatpak-plugin'
  )
  install="$pkgname.install"

  install -Dm644 "build/src/$pkgbase-flatpak.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-flatpak.so.${_sover}"

  create_links
}

package_libpamac-appstream-plugin() {
  pkgdesc="Appstream plugin for Pamac"
  depends=(
    'appstream'
    'libpamac'
  )
  provides=(
    'libpamac-appstream.so=11'
  )

  install -Dm644 "build/src/$pkgbase-appstream.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-appstream.so.${_sover}"

  create_links
}

package_libpamac-aur-plugin() {
  pkgdesc="AUR plugin for Pamac"
  depends=(
    'libpamac'
  )
  provides=(
    'libpamac-aur.so=11'
  )

  install -Dm644 "build/src/$pkgbase-aur.so.${_sover}" \
    "$pkgdir/usr/lib/$pkgbase-aur.so.${_sover}"

  create_links
}
