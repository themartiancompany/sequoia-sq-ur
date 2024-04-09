# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: David Runge <dvzrv@archlinux.org>

_git="false"
_offline="false"
_os="$( \
  uname \
    -o)"
_pkg='sequoia'
_module="ipc"
pkgname="${_pkg}-sq"
pkgver=0.34.1
_commit=fd270aeedfffc7d03f8bd61bcf0842a831ec7ded 
# refs/tags/v0.34.1
pkgrel=1
pkgdesc='Command-line frontends for Sequoia'
url="https://${_pkg}-pgp.org"
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'i686'
)
license=(
  'LGPL-2.0-or-later'
)
groups=(
  "${_pkg}"
)
replaces=(
  "${_pkg}"
)
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
options=(
  '!lto'
)
_http="https://gitlab.com"
_ns="${_pkg}-pgp"
_url="${_http}/${_ns}/${_pkg}"
_tarname="${_pkg}-${_module}-v${pkgver}"
[[ "${_offline}" == "true" ]] && \
  url="file://${HOME}/${pkgname}"
[[ "${_git}" == true ]] && \
  makedepends+=(
    "git"
  ) && \
  source+=(
    "${_tarname}::git+${_url}#tag=${pkgver}"
  ) && \
  sha256sums+=(
    SKIP
  )
[[ "${_git}" == false ]] && \
  source+=(
    # Gitlab
    "${_tarname}.tar.gz::${_url}/-/archive/${_module}/v${pkgver}/${_tarname}.tar.gz"
    # Github
    # "${_tarname}.tar.gz::${_url}/archive/refs/tags/${pkgver}.tar.gz"
  ) && \
  sha256sums+=(
    '5fe7d9402532ebfcbec3cb3999d083105338a0afc52456fa4ee4032403fc762b'
  )
validpgpkeys=(
  D2F2C5D45BE9FDE6A4EE0AAF31855247603831FD 
  # Justus Winter (Code Signing Key) <justus@sequoia-pgp.org>
  8F17777118A33DDA9BA48E62AACB3243630052D9 
  # Neal H. Walfield <neal@sequoia-pgp.org>
)

[[ "${_git}" == true ]] && \
  pkgver() {
    cd \
      "${_tarname}"
    git \
      describe \
        --tags | \
      sed \
        's/\([^-]*-g\)/r\1/;s/-/./g;s/v//g'
  }

prepare() {
  local \
    _target
  _target="${CARCH}-unknown-linux-gnu"
  cd \
    "${_tarname}"
  export \
    RUSTUP_TOOLCHAIN=stable 
  [[ "${_os}" == "Android" ]] && \
    _target="${CARCH}-linux-androideabi"
  cargo \
    fetch \
      --locked \
      --target \
        "${_target}"
}

build() {
  local \
    _cargo_opts=()
  _cargo_opts=(
    --release
    --frozen
    --features
      'default'
    --all
  )
  cd \
    "${_tarname}"
  export \
    CARGO_TARGET_DIR=../target \
    RUSTUP_TOOLCHAIN=stable \
    ASSET_OUT_DIR=../target
  # NOTE:
  #   we select specific (default) features,
  #   as there are multiple crypto backends
  cargo \
    build \
      "${_cargo_opts[@]}"
}

check() {
  cd \
    "${_tarname}"
  # NOTE: we use a different target dir,
  # as otherwise cargo test --release alters the sq binary
  # https://gitlab.com/sequoia-pgp/sequoia-sq/-/issues/96
  export \
    CARGO_TARGET_DIR=../target-test \
    RUSTUP_TOOLCHAIN=stable
  cargo \
    test \
    --release \
    --frozen \
    --features \
      'default'
  true
}

package() {
  install \
    -vDm 55 \
    target/release/sq \
    -t \
    "${pkgdir}/usr/bin"
  install \
    -vDm644 \
    target/shell-completions/sq.bash \
    "${pkgdir}/usr/share/bash-completion/completions/sq"
  install \
    -vDm644 \
    target/shell-completions/_sq \
    -t \
    "${pkgdir}/usr/share/zsh/site-functions"
  install \
    -vDm644 \
    target/shell-completions/sq.fish \
    -t \
    "${pkgdir}/usr/share/fish/vendor_completions.d"
  install \
    -vDm644 \
    target/man-pages/*.1 \
    -t \
    "${pkgdir}/usr/share/man/man1/"
}

# vim:set sw=2 sts=-1 et:
