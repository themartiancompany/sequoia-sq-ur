# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2023, 2024, 2025  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.  If not, see <https://www.gnu.org/licenses/>.

# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Truocolo <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
# Maintainer: Pellegrino Prevete (dvorak) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: David Runge <dvzrv@archlinux.org>

if [[ ! -v "_git" ]]; then
  _git="false"
fi
if [[ ! -v "_offline" ]]; then
  _offline="false"
fi
_os="$( \
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _cargo="false"
  _cc="clang"
  _libc="ndk-sysroot"
  _rust="true"
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _cargo="true"
  _cc="gcc"
  _libc="glibc" 
  _rust="false"
fi
_proj="sequoia"
_pkg="${_proj}"
_module="ipc"
pkgname="${_pkg}-sq"
pkgver=0.34.1
_sq_pkgver=0.34.0
_commit="fd270aeedfffc7d03f8bd61bcf0842a831ec7ded"
# refs/tags/v0.34.1
_sq_commit="696bf3a677fe8a34ed02c6e44afa1cc3c1e273df"
# refs/tags/v0.34.0
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
  'bzip2'
  'libbz2.so'
  'gcc-libs'
  "${_libc}"
  'gmp'
  'nettle'
  'libnettle.so'
  'libhogweed.so'
  'openssl'
  'sqlite'
)
makedepends=(
  'capnproto'
  "${_cc}"
)
if [[ "${_cargo}" == "true" ]]; then
  makedepends+=(
    "cargo"
  )
fi
if [[ "${_rust}" == "true" ]]; then
  makedepends+=(
    "rust"
  )
fi
options=(
  '!lto'
)
_http="https://gitlab.com"
_ns="${_pkg}-pgp"
_url="${_http}/${_ns}/${_pkg}"
_sq_url="${_http}/${_ns}/${pkgname}"
_tarname="${_pkg}-${_module}-v${pkgver}"
_sq_tarname="${pkgname}-v${_sq_pkgver}"
if [[ "${_offline}" == "true" ]]; then
  _url="file://${HOME}/${_pkg}" \
  _sq_url="file://${HOME}/${pkgname}"
fi
if [[ "${_git}" == true ]]; then
  makedepends+=(
    "git"
  )
  source+=(
    "${_tarname}::git+${_url}#tag=${pkgver}"
    "${_sq_tarname}::git+${_url}#tag=${_sq_pkgver}"
  )
  sha256sums+=(
    "SKIP"
    "SKIP"
  )
elif [[ "${_git}" == false ]]; then
  source+=(
    # Gitlab
    "${_tarname}.tar.gz::${_url}/-/archive/${_module}/v${pkgver}/${_tarname}.tar.gz"
    "${_sq_tarname}.tar.gz::${_sq_url}/-/archive/v${_sq_pkgver}/${_sq_tarname}.tar.gz"
    # Github
    # "${_tarname}.tar.gz::${_url}/archive/refs/tags/${pkgver}.tar.gz"
  )
  sha256sums+=(
    'ef28a21f6d240eb76c2b03699054b6d55a34a9b01746a2da33864cc60e4371f6'
    '6458274008ef06362c912eb67e285b734906acdb5c56e8490144f45bc1b81d51'
  )
fi
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

_usr_get() {
  local \
    _bin
  _bin="$( \
    dirname \
      "$(command \
           -v \
           "clang" \
           "cxx" \
           "g++" | \
           head \
	     -n \
	       1)")"
  echo \
    "$(dirname \
         "${_bin}")"
}

_prepare() {
  local \
    _dir="${1}" \
    _target \
    _pwd \
    _rust_android_targets=() \
    _rust_arch_targets=() \
    _msg=() \
    _arch
  _arch="$( \
    uname \
      -m)"
  _msg=(
    "Preparing '${_dir}'."
  )
  msg \
    "${_msg[*]}"
  _rust_android_targets+=( $( \
    rustc \
      --print \
        "target-list" | \
          grep \
            "androideabi")
  )
  _rust_arch_targets+=( $( \
    rustc \
      --print \
        "target-list" | \
          grep \
            "${_arch}")
  )
  _msg=(
    "Available Rust Android targets:"
    "${_rust_android_targets[*]}"
  )
  msg \
    "${_msg[*]}"
  _msg=(
    "Available Rust architecture targets:"
    "${_rust_arch_targets[*]}"
  )
  msg \
    "${_msg[*]}"
  _rust_target_native="$( \
    find \
      -type \
        "d" \
      "${_usr}/lib/rustlib" | \
      head \
        -n \
          1)"
  _msg=(
    "Rust native architecture target:"
    "${_rust_target_native}"
  )
  msg \
    "${_msg[*]}"
  _pwd="$( \
    pwd)"
  cd \
    "${_dir}"
  export \
    RUSTUP_TOOLCHAIN="stable"
  if [[ "${_os}" == "Android" ]]; then
    _target="${CARCH}-linux-androideabi"
  elif [[ "${_os}" == "GNU/Linux" ]]; then
    _target="${CARCH}-unknown-linux-gnu"
  fi
  cargo \
    fetch \
      --locked \
      --target \
        "${_target}"
  cd \
    "${_pwd}"
}

prepare() {
  _prepare \
    "${srcdir}/${_tarname}"
  _prepare \
    "${srcdir}/${_sq_tarname}"
}

cargo_opts=(
  --release
  --frozen
)

_build() {
  local \
    _dir="${1}" \
    _cargo_opts=() \
    _pwd
  echo \
    "building ${_dir}"
  _pwd="$( \
    pwd)"
  shift \
    1
  _cargo_opts=(
    "${cargo_opts[@]}"
    "$@"
  )
  cd \
    "${_dir}"
  export \
    CARGO_TARGET_DIR=../target \
    RUSTUP_TOOLCHAIN=stable \
    ASSET_OUT_DIR=../target
  # NOTE:
  #   we select specific (default) features,
  #   as there are multiple crypto backends
  cargo \
    build \
      "${_cargo_opts[@]}" \
      --all
  cd \
    "${_pwd}"
}

build() {
  _build \
    "${_tarname}" \
      --features \
        'default'
  _build \
    "${_sq_tarname}" \
      --features \
        'default'
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
