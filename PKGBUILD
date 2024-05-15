# Maintainer: Guillaume Benoit <guillaume@manjaro.org>
# Contributor: Philip Müller <philm@manjaro.org>
# Contributor: Helmut Stult <helmut@manjaro.org>

pkgbase=libpamac
pkgname=(
  'libpamac'
  'libpamac-snap-plugin'
  'libpamac-flatpak-plugin'
#  'libpamac-aur-plugin'
#  'libpamac-appstream-plugin'
)
pkgver=11.6.4+1+g3fe0f42
pkgrel=1
_sover=11.6
pkgdesc="Library for Pamac package manager based on libalpm"
arch=('x86_64' 'aarch64')
url="https://gitlab.manjaro.org/applications/libpamac"
license=('GPL-3.0-or-later')
depends=(
  'dbus-glib'
  'git'
  'glib2'
  'json-glib'
  'libalpm.so=14'
  'libsoup3'
  'pacman-mirrors'
  'polkit'
)
makedepends=(
  'appstream'
  'asciidoc'
  'flatpak'
  'gobject-introspection'
  'meson'
  'snapd'
  'snapd-glib'
  'vala'
)
options=('debug')
_commit=9108cba3ae01c60c198e996a4956474a66597a7b  # tags/11.6.4^0
_commit=3fe0f4230c1d57bf30ff233da94294bf8b58472b
source=("git+https://gitlab.manjaro.org/applications/libpamac.git#commit=$_commit"
        tmpdb_path-sync.patch
        manjaro_jp.patch
        )
sha256sums=('37e6168f2f65d7187ae364a84f3c570de9c95b887edfbbb7bb7f57035563e998'
            '200363034a6a3dc5a7e23483370de1c1988c6468bff9e9b3fe91896217ce4720'
            'dc74c3c18f1481b2f86fdaedc6f970378b697cbb53cf892aad2f08a776260855')

if [[ ! "$CC" =~ "gcc" ]];then
  echo "when using clang, LTO is disabled. prevent SEGV."
  options+=('!lto')
fi

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
  cd "$pkgbase"
  git describe --tags | sed 's/^v//;s/-/+/g'
}

prepare() {
  cd "$pkgbase"
  patch -p1 -i ../tmpdb_path-sync.patch
  patch -p1 -i ../manjaro_jp.patch
}

build() {
  arch-meson "$pkgbase" build \
    -Denable-appstream=true \
    -Denable-snap=true \
    -Denable-flatpak=true
  meson compile -C build
}

package_libpamac() {
  depends+=('appstream' 'pacman')
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

  meson install -C build --destdir "$pkgdir"

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
