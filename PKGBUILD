# Maintainer: Rudra Saraswat <rs2009@ubuntu.com>

pkgbase=blend
pkgname=(blend blend-settings)
pkgver=2.0.0
pkgrel=1
pkgdesc='A package manager for blendOS'
url='https://github.com/blend-os/blend'
source=("git+https://github.com/blend-os/blend.git"
        "blend-settings.desktop"
        "blend-settings")
sha256sums=('SKIP'
            'a605d24d2fa7384b45a94105143db216db1ffc0bdfc7f6eec758ef2026e61e54'
            '73cb7c39190d36f233b8dfbc3e3e6737d56e61e90881ad95f09e5ae1f9b405a8')
arch=('x86_64')
makedepends=(electron)
license=('GPL-3.0-or-later')

build() {
    cd "${srcdir}"/blend/blend-settings
    npm install
    export NODE_ENV=production
    npm run icons
    npm run pack -- -c.electronDist=/usr/lib/electron -c.electronVersion=22 --publish never
}

package_blend() {
    depends=('python3' 'podman' 'python-pexpect' 'blend-settings' 'bash')
    cd "${srcdir}"/blend
    mkdir -p "${pkgdir}"/usr/bin "${pkgdir}"/usr/lib/systemd/system "${pkgdir}"/usr/lib/systemd/user "${pkgdir}"/usr/lib/initcpio/install "${pkgdir}"/usr/lib/initcpio/hooks
    cp blend init-blend host-blend blend-system blend-files "${pkgdir}"/usr/bin
    cp blend-system.service "${pkgdir}"/usr/lib/systemd/system
    cp blend-files.service "${pkgdir}"/usr/lib/systemd/user
    cp blend.hook "${pkgdir}"/usr/lib/initcpio/hooks/blend
    cp blend.install "${pkgdir}"/usr/lib/initcpio/install/blend
}

package_blend-settings() {
    # https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=kuro was really helpful

    depends=('electron')
    cd "${srcdir}"/blend/blend-settings
    local _arch
    case $CARCH in
    i686)
        _arch=linux-ia32-unpacked
        ;;
    x86_64)
        _arch=linux-unpacked
        ;;
    *)
        _arch=linux-$CARCH-unpacked
        ;;
    esac
    install -Dm644 "dist/${_arch}/resources/app.asar" "$pkgdir/usr/lib/blend-settings/blend-settings.asar"
    for icon_size in 16 24 32 48 64 128 256 512; do
        install -Dm644 \
            "build/icons/png/${icon_size}x${icon_size}.png" \
            "${pkgdir}/usr/share/icons/hicolor/${icon_size}x${icon_size}/apps/blend-settings.png"
    done
    install -Dm644 -t "${pkgdir}/usr/share/applications" "../../blend-settings.desktop"
    install -Dm755 -t "${pkgdir}/usr/bin" "../../blend-settings"
    install -Dm644 "../LICENSE.md" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
