# $Id$
# Contributor: Daniel Micay <danielmicay@gmail.com>

pkgbase=termite
pkgname=('termite' 'termite-terminfo')
pkgver=13
pkgrel=3
pkgdesc="A simple VTE-based terminal"
url="https://github.com/thestinger/termite/"
license=('LGPL')
arch=('x86_64')
depends=('gtk3' 'pcre2' 'gnutls' 'vte-common')
makedepends=('git' 'ncurses' 'intltool' 'gperf' 'gtk-doc')
source=("git+https://github.com/amosbird/termite"
        "git+https://github.com/thestinger/vte-ng"
        "termite-util::git+https://github.com/thestinger/util")
sha256sums=('SKIP'
            'SKIP'
            'SKIP')

prepare() {
  cd vte-ng
  echo 'sources: $(BUILT_SOURCES)' >> src/Makefile.am 
  NOCONFIGURE=1 ./autogen.sh

  cd ../$pkgbase
  git submodule init
  git config --local submodule.util.url "$srcdir/termite-util"
  git submodule update
}

build() {
  cd vte-ng
  ./configure \
    --prefix="$srcdir/vte-static" \
    --localedir="/usr/share/$pkgbase/locale" \
    --enable-static \
    --disable-shared \
    enable_introspection=no \
    enable_vala=no \
    --disable-gtk-doc \
    --disable-glade-catalogue
  make -C src sources install-exec install-data
  make install-pkgconfigDATA

  cd ../$pkgbase
  export PKG_CONFIG_PATH="$srcdir/vte-static/lib/pkgconfig"
  make
}

package_termite() {
  depends+=('termite-terminfo')
  backup=(etc/xdg/termite/config)

  make -C vte-ng/po DESTDIR="$pkgdir" install-data
  make -C $pkgbase DESTDIR="$pkgdir" PREFIX=/usr install
  rm -r "$pkgdir/usr/share/terminfo"
}

package_termite-terminfo() {
  pkgdesc='Terminfo for Termite, a simple VTE-based terminal'
  depends=('ncurses')

  mkdir -p "$pkgdir/usr/share/terminfo"
  tic -x -o "$pkgdir/usr/share/terminfo" $pkgbase/termite.terminfo
}
