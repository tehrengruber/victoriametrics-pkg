# Maintainer: Till Ehrengruber <till@ehrengruber.ch>
#
# The upstream release binary, packaged here rather than taken from the AUR.
#
# The AUR has three VictoriaMetrics packages and only one of them was current
# when this was written: the from-source build had been flagged out of date for
# a fortnight, and the agent package for ten months, both still looking like
# maintained packages from the outside. The maintained one has two votes and a
# single maintainer. Packaging the release binary here costs a pkgver and two
# checksums per release (see ./update.sh), which is cheaper than carrying that
# risk.

pkgname=victoriametrics
pkgver=1.151.0
pkgrel=1
pkgdesc="Fast, cost-effective and scalable time series database"
arch=('x86_64' 'aarch64')
url="https://docs.victoriametrics.com/"
license=('Apache-2.0')
backup=('etc/default/victoriametrics')
conflicts=('victoriametrics-bin')

_url="https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/v${pkgver}"
source=('victoriametrics.service'
        'victoriametrics.default')
source_x86_64=("victoria-metrics-${pkgver}-amd64.tar.gz::${_url}/victoria-metrics-linux-amd64-v${pkgver}.tar.gz")
source_aarch64=("victoria-metrics-${pkgver}-arm64.tar.gz::${_url}/victoria-metrics-linux-arm64-v${pkgver}.tar.gz")
sha256sums=('c562ba07e430c1ec2977318c36f4af690adafea8c1321628eff112403d749d0e'
            '92c2727cb6832a68e558ee0a201967876781a02f4a8351969bf4b808afb244bc')
sha256sums_x86_64=('629bd538bdccaae6cb6c33fd6d387387abf5b6c00f2a99667407ad6085db1c91')
sha256sums_aarch64=('4c9236165fdbe8d3175103cf59f0179cfbf49355a39f9959febfde877e6f0a08')

package() {
  # Upstream names the binary victoria-metrics-prod inside the tarball.
  install -Dm755 "${srcdir}/victoria-metrics-prod" "${pkgdir}/usr/bin/victoria-metrics"
  install -Dm644 "${srcdir}/victoriametrics.service" "${pkgdir}/usr/lib/systemd/system/victoriametrics.service"
  install -Dm644 "${srcdir}/victoriametrics.default" "${pkgdir}/etc/default/victoriametrics"
}
