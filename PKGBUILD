# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://projects.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/modemmanager
# 						Maintainer: Ionut Biru <ibiru@archlinux.org>
# 						Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>

pkgbase=modemmanager
pkgname=(modemmanager libmm-glib)
pkgver=1.8rc1+6+g0f377f94
pkgrel=2
pkgdesc="Mobile broadband modem management service"
arch=(x86_64)
url="https://www.freedesktop.org/wiki/Software/ModemManager/"
license=(GPL2 LGPL2.1)
depends=('libgudev' 'polkit' 'ppp' 'libqmi' 'libmbim')
makedepends=(gtk-doc gobject-introspection vala autoconf-archive git)
_commit=0f377f943eeb81472fd73189f2c3d8fc65b8c609  # master
source=("git+https://anongit.freedesktop.org/git/ModemManager/ModemManager#commit=$_commit")
sha256sums=('SKIP')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

pkgver(){
	cd ModemManager
	git describe --tags | sed 's/-rc/rc/;s/-/+/g'
}

prepare() {
  cd ModemManager
  NOCONFIGURE=1 ./autogen.sh
}

build() {
  cd ModemManager
  export PYTHONPATH="/usr/share/glib-2.0"
  ./configure --prefix=/usr \
        --sysconfdir=/etc \
        --localstatedir=/var \
		--with-dbus-sys-dir=/usr/share/dbus-1/system.d \
        --with-udev-base-dir=/usr/lib/udev \
        --with-polkit=permissive \
        --with-systemd-journal=no \
        --with-systemd-suspend-resume=no \
        --enable-gtk-doc \
        --disable-static 

  # https://bugzilla.gnome.org/show_bug.cgi?id=655517
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool

  make
}

check() {
  # if the package is builded onto a container,
  # the dbus-daemon need to be running onto the container,
  # if not the check fail
  cd ModemManager
  make -k check
}

package_modemmanager() {
  depends+=(libmm-glib)
  optdepends=('usb_modeswitch: install if your modem shows up as a storage drive')
  options=(!emptydirs)

  cd ModemManager
  make DESTDIR="$pkgdir" install
  make DESTDIR="$pkgdir" -C libmm-glib uninstall
  make DESTDIR="$pkgdir" -C vapi uninstall

  # Some stuff to move is left over
  mv "$pkgdir/usr/include" ..
  mv "$pkgdir/usr/lib/pkgconfig" ..
}

package_libmm-glib() {
  pkgdesc="ModemManager library"
  depends=(glib2)

  install -d "$pkgdir/usr/lib"
  mv include "$pkgdir/usr"
  mv pkgconfig "$pkgdir/usr/lib"

  cd ModemManager
  make DESTDIR="$pkgdir" -C libmm-glib install
  make DESTDIR="$pkgdir" -C vapi install
}
