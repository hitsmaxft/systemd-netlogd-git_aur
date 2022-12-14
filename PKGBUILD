# Maintainer: hitsmaxft <mfthits at gmail.com>
_name=systemd-netlogd

pkgname=$_name
pkgver=1.2
pkgrel=1
pkgdesc="Forwards messages from the journal to other hosts over the network using syslog format RFC 5424 "
arch=('x86_64')
url="https://github.com/systemd/systemd-netlogd"
license=('LGPL')
depends=('libsystemd')
makedepends=('gperf' 'meson' 'python-sphinx')

#options=(!emptydirs)

pkgver_git() {
	cd "${srcdir}/$pkgname"
	git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
	( set -o pipefail
	git describe --long 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
		printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
	)
}

source_git=( "${pkgname}::git+https://github.com/hitsmaxft/systemd-netlogd.git#branch=config_install_prefix" 
	"systemd-netlogd.sysusers")

source=( "${pkgname}-${pkgver}.tar.gz::${url}/archive/v${pkgver}.tar.gz" 
	"systemd-netlogd.sysusers"
	"lib_path.patch"
)

md5sums_git=('SKIP'
	'4c2740c72e455d37f1304c4a1e1c3952'
	'f62b0f23529966a0eb69f4087216b177')

md5sums=('54308f7b8cbdc4452a538397a6dce100'
	'4c2740c72e455d37f1304c4a1e1c3952'
	'f62b0f23529966a0eb69f4087216b177')

prepare(){
	cd "$pkgname-$pkgver"
	patch --strip=1 --input="${srcdir}/lib_path.patch"
}

build() {
	arch-meson "${srcdir}/${pkgname}-${pkgver}" build --prefix='/usr/lib/systemd/' --sysconfdir='/etc/systemd/'
	ninja -C build
}

check() {
	cd build
	meson test
}

package() {
	DESTDIR="${pkgdir}" ninja -C build install

	install -Dm644 "$srcdir"/systemd-netlogd.sysusers "${pkgdir}/usr/lib/sysusers.d/systemd-netlogd.conf"

	mv "${pkgdir}/etc/systemd/systemd-netlogd.conf" "${pkgdir}/etc/systemd/systemd-netlogd.conf.example"
}
