# Contributor: Michael Serpieri <mickybart@pygoscelis.org>
pkgname=libhybris-28-glvnd
provides=('libhybris')
_pkgbase=libhybris
pkgver=1533.fa2bb0c
pkgrel=1
arch=('armv7h' 'aarch64' 'x86' 'x86_64')
url="https://github.com/libhybris/libhybris"
license=('Apache')
depends=('wayland' 'libglvnd')
makedepends=('git' 'mesa' 'android-headers')
conflicts=('ocl-icd')
source=("libhybris::git+https://github.com/manjaro-libhybris/libhybris")
md5sums=('SKIP')
options=(debug !strip)

pkgver() {
  cd "${srcdir}/${_pkgbase}"
  echo $(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

prepare() {
  cd "${_pkgbase}"

}

build() {
  cd "${srcdir}/${_pkgbase}/hybris"

  ./autogen.sh \
    --prefix=/usr \
    --enable-wayland \
    --enable-trace \
    --enable-debug \
    --enable-experimental \
    --with-android-headers=/usr/include/android \
    --enable-arch=arm64 \
    --with-default-hybris-ld-library-path=/usr/libexec/droid-hybris/system/lib64:/system/lib64:/vendor/lib64 \
    --enable-property-cache \
    --enable-glvnd
  make
}

package() {
  cd "${srcdir}/${_pkgbase}/hybris"

  # lib dependency issue: workaround with -j1
  make -j1 DESTDIR="${pkgdir}" install

  rm -f ${pkgdir}/usr/lib/pkgconfig/{egl,glesv1_cm,glesv2,wayland-egl}.pc

  cd "${pkgdir}/usr/include"
  # Move libhybris-provided headers into hybris dir
  mv CL EGL GLES GLES2 GLES3 KHR VG hybris

  # Symlink eglhybris.h
  mkdir -p EGL
  cd EGL
  ln -s ../hybris/EGL/eglhybris.h .

  install -m755 -d "${pkgdir}/usr/lib/libhybris-egl"

  for i in $(find ${pkgdir}/usr/lib/ -name 'libEGL.so*' -or -name 'libGLES*' -or -name 'libwayland-egl.so*'); do
    mv "$i" "${pkgdir}/usr/lib/libhybris-egl/"
  done
}
