# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: David Runge <dvzrv@archlinux.org>

pkgname=sequoia-sq
pkgver=0.34.0
_commit=1dd63bece8dea0072f736d5b2db5dd92320d4ef1  # refs/tags/v0.34.0
pkgrel=1
pkgdesc='Command-line frontends for Sequoia'
url='https://sequoia-pgp.org/'
arch=('x86_64')
license=('LGPL-2.0-or-later')
groups=('sequoia')
replaces=('sequoia')
depends=(
  'bzip2' 'libbz2.so'
  'gcc-libs'
  'glibc'
  'gmp'
  'nettle' 'libnettle.so' 'libhogweed.so'
  'openssl'
  'sqlite'
)
makedepends=(
  'capnproto'
  'cargo'
  'clang'
  'git'
)
options=('!lto')
source=("git+https://gitlab.com/sequoia-pgp/sequoia-sq.git#tag=$_commit?signed")
sha512sums=('SKIP')
validpgpkeys=(
  D2F2C5D45BE9FDE6A4EE0AAF31855247603831FD  # Justus Winter (Code Signing Key) <justus@sequoia-pgp.org>
  8F17777118A33DDA9BA48E62AACB3243630052D9  # Neal H. Walfield <neal@sequoia-pgp.org>
)

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g;s/v//g'
}

prepare() {
  cd $pkgname
  export RUSTUP_TOOLCHAIN=stable
  cargo fetch --locked --target "$CARCH-unknown-linux-gnu"
}

build() {
  cd $pkgname
  export CARGO_TARGET_DIR=../target
  export RUSTUP_TOOLCHAIN=stable
  export ASSET_OUT_DIR=../target
  # NOTE: we select specific (default) features, as there are multiple crypto backends
  cargo build --release --frozen --features 'default'
}

check() {
  cd $pkgname
  # NOTE: we use a different target dir, as otherwise cargo test --release alters the sq binary
  # https://gitlab.com/sequoia-pgp/sequoia-sq/-/issues/96
  export CARGO_TARGET_DIR=../target-test
  export RUSTUP_TOOLCHAIN=stable
  cargo test --release --frozen --features 'default'
}

package() {
  install -vDm 755 target/release/sq -t "${pkgdir}/usr/bin"

  install -vDm 644 target/shell-completions/sq.bash "${pkgdir}/usr/share/bash-completion/completions/sq"
  install -vDm 644 target/shell-completions/_sq -t "${pkgdir}/usr/share/zsh/site-functions"
  install -vDm 644 target/shell-completions/sq.fish -t "${pkgdir}/usr/share/fish/vendor_completions.d"
  install -vDm 644 target/man-pages/*.1 -t "${pkgdir}/usr/share/man/man1/"
}

# vim: ts=2 sw=2 et:
