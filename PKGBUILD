# Maintainer: Rudra Saraswat <rs2009@ubuntu.com>

pkgbase=blend-git
pkgname=('blend-git' 'blend-settings-git')
pkgver=r50.2e0016f
pkgrel=1
pkgdesc="A package manager for blendOS"
arch=('x86_64' 'i686')
url="https://github.com/blend-os/blend"
license=('GPL3')
makedepends=("electron" 'git' 'npm' 'base-devel')
source=('git+file://[BASE_ASSEMBLE_PATH]/projects/blend'
        'blend-settings.desktop'
        'blend-package-installer.desktop'
        'blend-settings'
        'blend.sh')
sha256sums=('SKIP'
            'a605d24d2fa7384b45a94105143db216db1ffc0bdfc7f6eec758ef2026e61e54'
            '23decd858ab49e860999bba783da78f43adc7b678065057cadfc2eeaefb2e870'
            '73cb7c39190d36f233b8dfbc3e3e6737d56e61e90881ad95f09e5ae1f9b405a8' 'SKIP')

pkgver() {
    cd "${srcdir}/${pkgbase%-git}"
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
    cd "${srcdir}/${pkgbase%-git}/${pkgbase%-git}-settings"
    npm config set cache "${srcdir}/npm-cache"
    npm install
}

build() {
    cd "${srcdir}/${pkgbase%-git}/${pkgbase%-git}-settings"
    npm config set cache "${srcdir}/npm-cache"
    export NODE_ENV=production
    electronDist="/usr/lib/electron"
    electronVer="$(sed s/^v// /usr/lib/electron/version)"
    npm run icons
    npm run pack -- -c.electronDist=${electronDist} \
        -c.electronVersion=${electronVer} --publish never
    cd ../overlayfs-tools; make
}

package_blend-git() {
    depends=('bash' 'blend-settings-git' 'podman' 'python' 'python-pexpect')
    provides=("${pkgname%-git}")
    conflicts=("${pkgname%-git}")

    cd "${srcdir}/${pkgbase%-git}"
    install -Dm755 \
        "${pkgname%-git}" \
        "init-${pkgname%-git}" \
        "host-${pkgname%-git}" \
        "${pkgname%-git}-files" \
        -t "${pkgdir}"/usr/bin/
    install -Dm755 "overlayfs-tools/overlayfs-tools" -t \
        "${pkgdir}/usr/bin/"
    install -Dm644 ../"${pkgname%-git}.sh" -t \
        "${pkgdir}"/etc/profile.d/
    install -Dm644 "${pkgname%-git}-files.service" -t \
        "${pkgdir}"/usr/lib/systemd/user/
    install -Dm644 "${pkgname%-git}.hook" \
        "${pkgdir}/usr/lib/initcpio/hooks/${pkgname%-git}"
    install -Dm644 "${pkgname%-git}.install" \
        "${pkgdir}/usr/lib/initcpio/install/${pkgname%-git}"
}

package_blend-settings-git() {
    pkgdesc="blendOS Settings"
    depends=("electron${_electronversion}")
    provides=(blend-settings)
    conflicts=(blend-settings)

    cd "${srcdir}/${pkgbase%-git}/${pkgbase%-git}-settings"

    local _arch
    case ${CARCH} in
    i686)
        _arch=linux-ia32-unpacked
        ;;
    x86_64)
        _arch=linux-unpacked
        ;;
    *)
        _arch=linux-${CARCH}-unpacked
        ;;
    esac

    install -Dm644 "dist/${_arch}/resources/app.asar" \
        "$pkgdir/usr/lib/${pkgname%-git}/${pkgname%-git}.asar"

    for icon_size in 16 24 32 48 64 128 256 512; do
        install -Dm644 "build/icons/png/${icon_size}x${icon_size}.png" \
            "${pkgdir}/usr/share/icons/hicolor/${icon_size}x${icon_size}/apps/${pkgname%-git}.png"
    done

    install -Dm644 "${srcdir}/${pkgname%-git}.desktop" -t \
        "${pkgdir}"/usr/share/applications/

    install -Dm644 "${srcdir}/blend-package-installer.desktop" -t \
        "${pkgdir}"/usr/share/applications/

    install -Dm755 "${srcdir}/${pkgname%-git}" -t "${pkgdir}"/usr/bin/
}
