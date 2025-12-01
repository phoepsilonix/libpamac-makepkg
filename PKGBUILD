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
pkgver=11.7.4
pkgrel=1
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
sha256sums=('13f531db76921740e7d5b2478cc774d29757313ff4a85befe5c683c6b0cd0d47')

_backports=(
)

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

  local _c _l
  for _c in "${_backports[@]}"; do
    if [[ "${_c}" == *..* ]]; then _l='--reverse'; else _l='--max-count=1'; fi
    git --no-pager log --oneline "${_l}" "${_c}"
    git cherry-pick --mainline 1 --no-commit "${_c}"
  done
  for _c in "${_reverts[@]}"; do
    if [[ "${_c}" == *..* ]]; then _l='--reverse'; else _l='--max-count=1'; fi
    git log --oneline "${_l}" "${_c}"
    git revert --mainline 1 --no-commit "${_c}"
  done
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
