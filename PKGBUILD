# Maintainer: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>
# Maintainer: Ionut Biru <ibiru@archlinux.org>
# Contributor: Michael Kanis <mkanis_at_gmx_dot_de>

pkgname=mutter
pkgver=3.34.1+26+gc0037305e
pkgrel=1
pkgdesc="A window manager for GNOME"
url="https://gitlab.gnome.org/GNOME/mutter"
arch=(x86_64)
license=(GPL)
depends=(dconf gobject-introspection-runtime gsettings-desktop-schemas libcanberra
         startup-notification zenity libsm gnome-desktop upower libxkbcommon-x11
         gnome-settings-daemon libgudev libinput pipewire xorg-server-xwayland)
makedepends=(gobject-introspection git egl-wayland meson xorg-server sysprof)
checkdepends=(xorg-server-xvfb)
groups=(gnome)
install=mutter.install
_commit=c0037305ebd9b742f8d133d810192022878efe8e  # gnome-3-34
source=("git+https://gitlab.gnome.org/GNOME/mutter.git#commit=$_commit"
         x11-Add-support-for-fractional-scaling-using-Randr.patch)
sha256sums=('SKIP'
            '6e022df5e5b2bb02ca3dedb5cc2895193297703888213764a2c4a2629d9d2272')

pkgver() {
  cd $pkgname
  git describe --tags | sed 's/-/+/g'
}

prepare() {
  cd $pkgname
  patch -p1 -i "${srcdir}/x11-Add-support-for-fractional-scaling-using-Randr.patch"
}

build() {
  arch-meson $pkgname build \
    -D egl_device=true \
    -D wayland_eglstream=true \
    -D installed_tests=false
  ninja -C build
}

check() (
  mkdir -p -m 700 "${XDG_RUNTIME_DIR:=$PWD/runtime-dir}"
  glib-compile-schemas "${GSETTINGS_SCHEMA_DIR:=$PWD/build/data}"
  export XDG_RUNTIME_DIR GSETTINGS_SCHEMA_DIR

  # Unexpected passes in conform test
  # Stacking test flaky
  dbus-run-session xvfb-run -s '+iglx -noreset' meson test -C build --print-errorlogs || :
)

package() {
  DESTDIR="$pkgdir" meson install -C build
}
