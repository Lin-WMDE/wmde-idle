# Maintainer: WMDE <https://wmde.fun>
# Contributor: System76 <info@system76.com> (original cosmic-idle)
#
# Builds our fork Lin-WMDE/wmde-idle (branch wmde). The idle daemon wmde-session starts:
# fades and turns the outputs off after the screen-off timeout, then locks the session
# (loginctl lock-session, answered by the wmde-greeter locker), suspends after the AC or
# battery timeout, and owns org.freedesktop.ScreenSaver for idle inhibition. Timeouts are
# the fun.wmde.Idle config that the Power page of wmde-settings writes.
pkgname=wmde-idle
pkgver=0.1.0
pkgrel=1
pkgdesc="WMDE idle daemon: screen off, lock and suspend on inactivity (fork of cosmic-idle)"
arch=('x86_64')
url="https://wmde.fun"
license=('GPL-3.0-only')
# upower: on-battery state, which picks the battery suspend timeout over the AC one.
depends=('glibc' 'gcc-libs' 'wayland' 'upower')
makedepends=('rust' 'cargo' 'just' 'git' 'clang' 'lld' 'pkgconf')
source=("$pkgname::git+https://github.com/Lin-WMDE/wmde-idle.git#branch=wmde")
sha256sums=('SKIP')

pkgver() {
  cd "$srcdir/$pkgname"
  # WMDE version: <COSMIC major.minor>.<WMDE release>.<commits since the nearest tag>.g<short>.
  local desc
  desc=$(git describe --long --tags --abbrev=7 2>/dev/null || true)
  if [ -n "$desc" ]; then
    printf '1.10.3.%s.g%s' "$(printf '%s' "$desc" | sed -E 's/.*-([0-9]+)-g[0-9a-f]+$/\1/')" "$(git rev-parse --short=7 HEAD)"
  else
    printf '1.10.3.%s.g%s' "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
  fi
}

build() {
  cd "$srcdir/$pkgname"
  # x86-64-v3 (AVX2/BMI2) baseline for the WMDE repo; runs on Haswell+ (and the VM).
  export RUSTFLAGS="${RUSTFLAGS:+$RUSTFLAGS }-C target-cpu=x86-64-v3"
  just build-release
}

package() {
  cd "$srcdir/$pkgname"
  # installs /usr/bin/wmde-idle (name from justfile)
  just rootdir="$pkgdir" prefix=/usr install
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
