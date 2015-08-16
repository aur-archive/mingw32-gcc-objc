
pkgname=mingw32-gcc-objc
pkgver=4.5.2
pkgrel=1
arch=(i686 x86_64)
pkgdesc="C, C++ and Objective C cross-compilers for building Windows executables on Linux"
depends=(mingw32-runtime mingw32-binutils mingw32-w32api libmpc elfutils cloog-ppl gmp)
replaces=(mingw32-gcc-base)
provides=(mingw32-gcc-base mingw32-gcc)
conflicts=(mingw32-gcc-base mingw32-gcc)
options=(!strip)
url="http://mingw.sf.net"
license=(GPL LGPL)
source=(gcc-$pkgver-1-mingw32-src.tar.xz::http://downloads.sourceforge.net/project/mingw/MinGW/BaseSystem/GCC/Version4/gcc-$pkgver-1/gcc-$pkgver-1-mingw32-src.tar.lzma)
md5sums=('6df4174d78285ec58910f86217b8acb1')

build()
{
  [ $NOEXTRACT -eq 1 ] || tar xJf gcc-$pkgver-1-mingw32-src.tar.xz
  [ $NOEXTRACT -eq 1 ] || tar xjf gcc-$pkgver.tar.bz2

  mkdir -p $srcdir/build
  cd $srcdir/build

  unset CFLAGS CXXFLAGS

  chmod ugo+x $srcdir/gcc-$pkgver/configure
  chmod ugo+x $srcdir/gcc-$pkgver/move-if-change

  [ $NOEXTRACT -eq 1 ] || $srcdir/gcc-$pkgver/configure \
     --target=i486-mingw32 \
     --host=$CHOST \
     --build=$CHOST \
     --prefix=/usr \
     --enable-languages=c,c++,objc,obj-c++ \
     --enable-shared \
     --enable-sjlj-exceptions \
     --enable-hash-synchronization \
     --disable-nls \
     --disable-libssp \
    || return 1

  make || return 1
}

package()
{
  cd $srcdir/build

  unset CFLAGS CXXFLAGS

  make install DESTDIR=$pkgdir || return 1

  make -j1 -C i486-mingw32/libgcc DESTDIR=${pkgdir} libgcc_eh.a install || return 1
  make -j1 -C i486-mingw32/libstdc++-v3 DESTDIR=${pkgdir} install || return 1

  mv -f $pkgdir/usr/bin/*.dll $pkgdir/usr/i486-mingw32/lib/ || return 1

  cd $pkgdir
  rm -rf usr/bin/i486-mingw32-{gcov,gccbug,gcc-*} \
    usr/{include,lib/libiberty.a} \
    usr/share/{info,man} \
    usr/share/gcc-$pkgver/python \
    libgcc_s_sjlj-1.dll

  strip usr/bin/*
  strip usr/libexec/gcc/i486-mingw32/$pkgver/{cc1*,collect2}
  i486-mingw32-strip -g usr/lib/gcc/i486-mingw32/$pkgver/*.a
}
