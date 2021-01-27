# This is an example PKGBUILD file. Use this as a start to creating your own,
# and remove these comments. For more information, see 'man PKGBUILD'.
# NOTE: Please fill out the license field for your package! If it is unknown,
# then please put 'unknown'.

# Maintainer: Thorben Krüger <thorben.krueger@ovgu.de>
pkgname=scionlab-git
pkgver=v2020.12.r5.ga90f354e
pkgrel=3
epoch=
pkgdesc="SCIONLab"
arch=('x86_64')
url="https://scion-architecture.net"
license=('Apache')
groups=('scion')
depends=('glibc' 'python')
makedepends=('git' 'go')
checkdepends=()
optdepends=('openvpn')
provides=()
conflicts=()
replaces=()
backup=()
options=()
install=scionlab.install
changelog=
source=("scionlab::git+https://github.com/netsec-ethz/scion.git#branch=scionlab"
	"scion-border-router@.service"
	"scion-control-service@.service"
	"scion-daemon.service"
	"scion-dispatcher.service"
	"scion-ip-gateway.service"
	"scionlab.target"
	"dispatcher.toml"
	"sciond.toml"
	"sig.toml"
	"https://raw.githubusercontent.com/netsec-ethz/scionlab/c29101a9d476920c491b30982f7f1a49b113f2a3/scionlab/hostfiles/scionlab-config"
	"https://golang.org/dl/go1.14.14.linux-amd64.tar.gz")
md5sums=('SKIP'
         '9202be206a0a2f30eb30d552efd6f9a6'
         '47dc3b0aae88537d388e9f1f28017320'
         '80a32802ea6c327633886680bfa48968'
         '7fb9f77cb907d11c904919f8820107a1'
         'ab42451f6421da4b1dfd525ed9317185'
         '9c41fd9c5286945e28112011e0d0fa87'
         'bc455aa2b37c6198f38e2d4c37f4e556'
         '7de52e9e95df78c9203014b66f91c57d'
         'a27e871f88ec0aae900eaa26c504771f'
         'e07ad88cfd36251ee87577defa4ea5c7'
         '973925f5d03d1f072a5588b8dd8581b9')
# go 1.14 is needed for now
	#"$pkgname-$pkgver.tar.gz"
        #"$pkgname-$pkgver.patch")
noextract=()
validpgpkeys=()

pkgver() {
  cd "${pkgname%-git}"
  git describe --long --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  #cd "$pkgname-$pkgver"
  cd "$srcdir/${pkgname%-git}"
  #patch -p1 -i "$srcdir/$pkgname-$pkgver.patch"
}

build() {
  cd "$srcdir/${pkgname%-git}"
  #cd "$pkgname-$pkgver"
  $srcdir/go/bin/go build -o ./bin/ -v \
     -ldflags="-s -w -X github.com/scionproto/scion/go/lib/env.StartupVersion=$(git describe --tags)-scionlab"\
     ./go/posix-router/\
     ./go/cs/\
     ./go/posix-gateway/\
     ./go/sciond/\
     ./go/dispatcher/\
     ./go/scion/\
     ./go/scion-pki/\
     ./go/tools/pathdb_dump/
}

check() {
  #cd "$pkgname-$pkgver"
  cd "$srcdir/${pkgname%-git}"
  #make -k check
}

package() {
  for SysFile in "scion-border-router@.service"\
		   "scion-control-service@.service"\
		   "scion-daemon.service"\
		   "scion-dispatcher.service"\
		   "scion-ip-gateway.service"\
		   "scionlab.target"
  do
    install -Dm644 $SysFile "$pkgdir/usr/lib/systemd/system/$SysFile"
  done
  for ConfFile in "dispatcher.toml"\
		    "sciond.toml"\
		    "sig.toml"
  do
    install -Dm644 -gscion -oscion $ConfFile "$pkgdir/etc/scion/$ConfFile"
  done
  install -Dm755 scionlab-config "$pkgdir/usr/bin/scionlab-config"
  cd "$srcdir/${pkgname%-git}"
  #cd "$pkgname-$pkgver"
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
  pushd "bin"
  install -Dm755 posix-router "$pkgdir/usr/bin/scion-border-router"
  install -Dm755 cs "$pkgdir/usr/bin/scion-control-service"
  install -Dm755 posix-gateway "$pkgdir/usr/bin/scion-ip-gateway"
  install -Dm755 dispatcher "$pkgdir/usr/bin/scion-dispatcher"
  for binary in $(find . -type f -name "scion*" -print)
  do
    install -Dm755 $binary "$pkgdir/usr/bin/$binary"
  done
  popd
  #make DESTDIR="$pkgdir/" install
}
