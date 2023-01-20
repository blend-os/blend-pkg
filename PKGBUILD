# Maintainer: Rudra Saraswat <rs2009@ubuntu.com>

pkgname=blend
pkgver=1.0.4
pkgrel=1
pkgdesc='A package manager for blendOS'
url='https://github.com/blend-os/blend'
source=("git+https://github.com/blend-os/blend.git")
sha256sums=('SKIP')
arch=('x86_64')
license=('GPL-3.0-or-later')
depends=('python3' 'distrobox' 'podman')

package() {
	cd blend

	mkdir -p "${pkgdir}"/{usr/{bin,share/bash-completion/completions},blend,etc/profile.d}
	cp blend "${pkgdir}/usr/bin"
	cp blend-profiled.sh "${pkgdir}/etc/profile.d"
	cp completions/blend "${pkgdir}/usr/share/bash-completion/completions"
	cp -r pkgmanagers "${pkgdir}/blend"
}
