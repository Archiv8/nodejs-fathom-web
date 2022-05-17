#!/bin/bash

# Disable various shellcheck rules that produce false positives in this file.
# Repository rules should be added to the .shellcheckrc file located in the
# repository root directory, see https://github.com/koalaman/shellcheck/wiki
# and https://archiv8.github.io for further information.
# shellcheck disable=SC2034,SC2154
# [ToDo]: Add files: User documentation
# [ToDo]: Add files: Tooling
# [FixMe]: Namcap warnings and errors

# Maintainer: Ross Clark <https://github.com/orgs/Archiv8/fathom/discussions>
# Contributor: Ross Clark <https://github.com/orgs/Archiv8/fatho/discussionsm>

_langname="nodejs"
_relname="fathom-web"

pkgbase="${_relname}"
pkgname=(
  "${_langname}-${_relname}"
)
pkgver=3.7.3
pkgrel=1
pkgdesc="A supervised-learning system for recognizing parts of web pages—pop-ups, address forms, slideshows—or for classifying a page as a whole."
url="https://github.com/mozilla/fathom"
arch=(
  "any"
)
license=(
  "APACHE"
)
depends=(
  # Arch Linux Official Repositories
  "nodejs"
)
makedepends=(
  # Arch Linux Official Repositories
  "jq"
  "npm"
)
source=(
  "https://registry.npmjs.org/${_relname}/-/${_relname}-$pkgver.tgz"
)
sha512sums=(
  "e244f96346d95c0bba991dc19c3760d8c8b1769d6e7a306c5c24a9940c39a0f1085c87f6e5c1d46c0987c02ee847cfef56ff98f8d9c07aaacb580e898e92995f"
)

package() {

  # Ensure cache is set when install is run in order to avoid littering user's home directory
  printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
  printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"

  npm install --global \
    --cache "$srcdir/npm-cache" \
    --prefix "$pkgdir"/usr "$srcdir"/$_relname-$pkgver.tgz

  # Fix incorrect user / group as highlighted by namcap
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
  while IFS= read -r fsitem; do
    chown -h root:root "$fsitem"
  done < <(find "$pkgdir" -not -group root -not -user root)

  # Non-deterministic race in npm gives 777 permissions to random directories.
  # See https://github.com/npm/npm/issues/9359 for details.
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible permission issues on directories\e[0m\n"
  find "$pkgdir/usr" -type d -exec chmod 755 '{}' +

  # Remove references to $pkgdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$pkgdir\e[0m\n"
  find "$pkgdir" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

  # Remove references to $srcdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$srcdir \e[0m\n"
  local tmppackage="$(mktemp)"
  local pkgjson="$pkgdir/usr/lib/node_modules/$_relname/package.json"
  jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
  mv "$tmppackage" "$pkgjson"
  chmod 644 "$pkgjson"
}
