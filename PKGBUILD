# Maintainer: xduugu
# Contributor: nh2
# Contributor: Steven Davidovitz <steviedizzle ð gmail đ com>
# Contributor: Nick B <Shirakawasuna ð gmail đ com>
# Contributor: Christof Musik <christof ð senfdax đ de>
# Contributor: Stefan Rupp <archlinux ð stefanrupp đ de>
# Contributor: Ignas Anikevicius <anikevicius ð gmail đ com>

_pkgname=tp_smapi

# make the AUR parser think the following lines are comments
# to bypass the buggy parser check
[[ "#" ]] && _kernext=${_kernext:-}
[[ "#" ]] && _kernver=${_kernver:-$(uname -r)}
[[ "#" ]] && _extramodules=$(readlink -f "/usr/lib/modules/$_kernver/extramodules" || true)
[[ "#" ]] && _kernver=$(< $_extramodules/version)

pkgname=$_pkgname$_kernext
pkgver=0.41
pkgrel=3
pkgdesc="Modules for ThinkPad's SMAPI functionality"
[[ -n $_kernext ]] && pkgdesc+=" (for linux$_kernext)"
arch=('i686' 'x86_64')
url="https://github.com/evgeni/tp_smapi"
license=('GPL')
depends=("linux$_kernext")
makedepends=("linux$_kernext-headers")
install=$_pkgname.install
source=(https://github.com/downloads/evgeni/$_pkgname/$_pkgname-$pkgver.tar.gz)
md5sums=('63c683415c764568f6bf17c7eabe4752')
sha256sums=('6aef02b92d10360ac9be0db29ae390636be55017990063a092a285c70b54e666')

build() {
  cd "$srcdir/$_pkgname-$pkgver"
  sed -ri -e "s/^(KVER\s*:=).*$/\1 $_kernver/" \
	  -e "s#^(KBASE\s*:=).*\$#\1 ${_extramodules%/*}/$_kernver#" Makefile
  make HDAPS=1
}

package() {
  make -C "${_extramodules%/*}/$_kernver/build" \
    INSTALL_MOD_PATH="$pkgdir/${_extramodules%%/lib/*}" M="$srcdir/$_pkgname-$pkgver" modules_install

  cd "$pkgdir/${_extramodules%/*}"
  mv "$_kernver/extra" "${_extramodules##*/}"
  rmdir "$_kernver"

  # compress kernel modules
  find "$pkgdir" -name "*.ko" -exec gzip -9 {} +

  # load module on startup
  echo tp_smapi > "$srcdir/$pkgname.conf"
  install -Dm644 "$srcdir/$pkgname.conf" "$pkgdir/usr/lib/modules-load.d/$pkgname.conf"

  # update kernel version in install file
  sed -ri "s#^(extramodules=).*\$#\1$_extramodules#" "$startdir/$_pkgname.install"
}
