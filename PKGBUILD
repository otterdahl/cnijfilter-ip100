# Maintainer: David Otterdahl <david.otterdahl [at] gmail [dot] org>
# Modified original script from: Alessio Fachechi and Daniel Hillenbrand

pkgname=cnijfilter-ip100
pkgver=3.70
pkgsubver=1
pkgrel=1
pkgdesc="Canon IJ Printer Driver (iP100 series)"
url="http://www.canon.se/support/consumer_products/products/printers/inkjet/pixma_ip_series/ip100.aspx"
arch=('i686' 'x86_64')
license=('custom')
depends=('patch' 'libcups' 'cups' 'popt' 'ghostscript' 'gsfonts' 'atk>=1.9.0' 'gtk2>=2.8.0' 'pango>=1.12.3' 'libpng>=1.2.8' 'libtiff' 'cairo>=1.0.2' 'libxml2>=2.6.24' 'fontconfig>=2.3.0' 'libxinerama')
makedepends=('autoconf>=2.13' 'automake>=1.6' 'tar' 'make' 'gcc')
conflicts=('cnijfilter-common')
install=cnijfilter-ip100.install

source=(http://gdlp01.c-wss.com/gds/8/0100004118/01/cnijfilter-source-3.70-1.tar.gz
        grayscale.patch
        cups.patch
        libpng15.patch
        cnij.patch)

md5sums=('0f23002a95c4dc9e8b85fe612d0cff0b'
         'ac067f9de34057bac77856694fa1661f'
         '4887266c7c386e56f23fb4dbd9744d06'
         '571948d108244440a6203bd475f900c4'
         'ec35486603599a69f2d6a782e72481bd')

prepare() {

  patch -p0 -d ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/ppd < grayscale.patch

  cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}
  patch -p1 -i ${srcdir}/cups.patch
  patch -p1 -i ${srcdir}/libpng15.patch
  patch -p1 -i ${srcdir}/cnij.patch
  
} 

build() {
  
  cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/libs
  ./autogen.sh --prefix=/usr  --program-suffix=ip100
  make 

  for _dir in cngpij cnijfilter pstocanonij lgmon backend backendnet cngpijmon/cnijnpr
    do 
      cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/${_dir}
      ./autogen.sh --prefix=/usr --program-suffix=ip100 --enable-progpath=/usr/bin
      make LIBS=-ldl
    done

  cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/ppd
  ./autogen.sh --prefix=/usr --program-suffix=ip100
  make 
   
} 

package() {

  cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/libs
  make install DESTDIR=${pkgdir} 

  for _dir in cngpij cnijfilter pstocanonij lgmon backend backendnet cngpijmon/cnijnpr
    do 
      cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/${_dir}
      make install DESTDIR=${pkgdir}
    done

  cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}/ppd
  make install DESTDIR=${pkgdir}

  LNGBITS=`getconf LONG_BIT`
  if [ $LNGBITS -eq 32 ]; then
      _arc=32
  else
      _arc=64
  fi

  cd ${srcdir}/cnijfilter-source-${pkgver}-${pkgsubver}
  install -d ${pkgdir}/usr/lib/bjlib
  install -m 755 303/database/* ${pkgdir}/usr/lib/bjlib
  install -s -m 755 303/libs_bin${_arc}/*.so.* ${pkgdir}/usr/lib
  install -s -m 755 com/libs_bin${_arc}/*.so.* ${pkgdir}/usr/lib
  install -D LICENSE-cnijfilter-${pkgver}EN.txt ${pkgdir}/usr/share/licenses/${pkgname}/LICENSE-cnijfilter-${pkgver}EN.txt
  
}
