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
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Pellegrino Prevete (dvorak) <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>

if [[ ! -v "_offline" ]]; then
  _offline="false"
fi
if [[ ! -v "_git" || \
      "${_git}" == "" ]]; then
  if [[ "${_offline}" == "true" ]]; then
    _git="true"
  elif [[ "${_offline}" == "false" ]]; then
    _git="false"
  fi
else
  _git="false"
fi
_py="python"
_py2="${_py}2"
_os="$( \
  uname \
    -o)"
_pkgname=reallymakepkg
pkgname="${_pkgname}-git"
pkgver=.r.g
pkgrel=1
pkgdesc="System-independent makepkg"
arch=(
  'any'
)
_repo="https://github.com"
_ns="themartiancompany"
url="${_repo}/${_ns}/${_pkgname}"
license=(
  'AGPL3'
)
depends=(
  'binutils'
  'bash'
  'fakeroot'
  'libcrash-bash'
  'pacman'
)
if [[ "${_os}" == 'Android' ]]; then
  depends+=(
    'libandroid-complex-math'
    'libandroid-glob'
    'libandroid-nl-types'
    'libandroid-posix-semaphore'
    'libandroid-shmem'
    'libandroid-spawn'
    'libandroid-stub'
    'libandroid-support'
    'libandroid-sysv-semaphore'
    'libandroid-utimes'
    'libandroid-wordexp'
  )
fi
makedepends=(
  'make'
)
optdepends=(
  "${_py}-pygments: colorized output and syntax highlighting"
  "${_py2}-pygments: colorized output and syntax highlighting"
)
provides=(
  "${_pkgname}=${pkgver}"
)
conflicts=(
  "${_pkgname}"
)
_url="${url}"
[[ "${_offline}" == true ]] && \
  _url="${_local}"
source=()
_branch="main"
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    'git'
  ) 
  source+=(
    "${_pkgname}-${_branch}::git+${_url}#branch=${_branch}"
  )
elif [[ "${_git}" == "false" ]]; then
  makedepends+=(
    'curl'
    'jq'
  )
  source+=(
    "${_pkgname}-${_branch}.tar.gz::${_url}/archive/refs/heads/${_branch}.tar.gz"
  )
fi
sha256sums=(
  'SKIP'
)

_nth() {
  local \
    _str="${1}" \
    _n="${2}"
  echo \
    "${_str}" | \
    awk \
      -F '+' \
      '{print $'"${_n}"'}'
}

_jq_pkgver() {
  local \
    _version \
    _rev \
    _commit
  _version="$( \
    curl \
      --silent \
      "${_gh_api}/tags" | \
      jq \
        '.[0].name')"
  _version_commit="$( \
    curl \
      --silent \
      "${_gh_api}/tags" | \
      jq \
        '.[0].commit.sha')"
  _rev="$( \
    curl \
      --silent \
      "${_gh_api}/commits" | \
      jq \
        'map(.sha == "'${_version_commit}'" ) | index(true)')"
  _commit="$( \
    curl \
      --silent \
      "${_gh_api}/commits" | \
      jq \
        '.[0].sha')"
  printf \
    "%s.r%s.g%s" \
    "${_version}" \
    "${_rev}" \
    "${_commit}"
}

_parse_ver() {
  local \
    _pkgver="${1}" \
    _out="" \
    _ver \
    _rev \
    _commit
  _ver="$( \
    _nth \
      "${_pkgver}" \
      "1")"
  _rev="$( \
    _nth \
      "${_pkgver}" \
      "2")"
  _commit="$( \
    _nth \
      "${_pkgver}" \
      "3")"
  _out=${_ver}
  [[ "${_rev}" != "" ]] && \
    _out+=".r${_rev}"
  [[ "${_commit}" != "" ]] && \
    _out+=".${_commit}"
  echo \
    "${_out}"
}

_git_pkgver() {
  local \
    _pkgver
  _pkgver="$( \
    git \
      describe \
      --tags \
      --long | \
      sed \
        's/-/+/g')"
  _parse_ver \
    "${_pkgver}"
}

pkgver() {
  cd \
    "${_pkgname}-${_branch}"
  if [[ "${_git}" == true ]]; then
    _git_pkgver
  elif [[ "${_git}" == false ]]; then
    _jq_pkgver
  fi
}

_os="$( \
  uname \
    -o)"
_usr="$( \
  dirname \
    "$( \
      command \
        -v \
        "cc" \
        "gcc" | \
        head \
          -n \
            1)")/../"

package() {
  cd \
    "${_pkgname}-${_branch}"
  [[ "${_os}" != "GNU/Linux" ]] && \
    make \
      DESTDIR="${pkgdir}" \
      install
  [[ "${_os}" == "GNU/Linux" ]] && \
    make \
      DESTDIR="${pkgdir}" \
      PREFIX="${_usr}" \
      install || \
  true
}

# vim: ft=sh syn=sh et
