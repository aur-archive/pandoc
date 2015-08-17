# Maintainer: Nicolas Reynolds <fauno@kiwwwi.com.ar>
# Based on haskell-pandoc

# Run `makepkg -sp SRCBUILD` if you want to update the source tarball

pkgname=pandoc
pkgver=1.11.1
pkgrel=1
pkgdesc='Conversion between markup formats (no Haskell libs)'
url='http://johnmacfarlane.net/pandoc/'
license=('GPL')
arch=('i686' 'x86_64')
makedepends=('ghc' 'sh' 'cabal-install')
options=(strip !makeflags !distcc !emptydirs)
source=(https://repo.parabolagnulinux.org/other/${pkgname}-${pkgver}-$pkgrel-any.src.tar.xz{,.sig}
        SRCBUILD)
#        citeproc-hs-pre-0.3.7.patch)
conflicts=('haskell-pandoc')
optdepends=('texlive-most: for pdf creation')

# PKGBUILD functions
build() {
    mkdir -p ${srcdir}/{build,${pkgname}-${pkgver}}
    cd ${srcdir}/${pkgname}-${pkgver}

# Patches from the next citeproc-hs release
# These are needed because 0.3.6 with embed_data_files is broken
# darcs diff --from-tag=0.3.6
#   pushd ${srcdir}/${pkgname}-${pkgver}/citeproc-hs-0.3.6
#   patch -Np1 -i ${srcdir}/citeproc-hs-pre-0.3.7.patch
#   popd

   while read _hkpkg; do
# Skip already built
       [ -d ${srcdir}/build/usr/lib/$_hkpkg ] && continue

       pushd ${srcdir}/${pkgname}-${pkgver}/${_hkpkg} >/dev/null

       msg2 "Building $_hkpkg"

       case $_hkpkg in
        $pkgname-$pkgver)
         HOME=${srcdir}/${pkgname}-${pkgver} \
         cabal configure --flags='-library blaze_html_0_5' \
                         --prefix=/usr \
                         --libdir=${srcdir}/build/usr/lib -v

         HOME=${srcdir}/${pkgname}-${pkgver} \
         cabal build
         ;;

        citeproc-hs-*)
         HOME=${srcdir}/${pkgname}-${pkgver} \
         cabal install --flags='embed_data_files' \
                       --prefix=${srcdir}/build/usr -v
         ;;

        *)
         HOME=${srcdir}/${pkgname}-${pkgver} \
         cabal install --prefix=${srcdir}/build/usr
         ;;
       esac

       popd >/dev/null
   done <BUILDORDER

}

package() {
  cd ${srcdir}/${pkgname}-${pkgver}/${pkgname}-${pkgver}

  runghc Setup.hs copy --destdir=${pkgdir}/

# For some reason the library is installed anyway
# Remove all files and !emptydirs takes care of the rest
  msg2 "Removing lib files..."
  find ${pkgdir} -iname lib -print0 | xargs -0 rm -rvf

# EC is unfree and makes Parabola TeXLive cry
# besides, it's unneeded
  sed "/fontenc/d" -i ${pkgdir}/usr/share/${pkgname}-${pkgver}/data/templates/default.latex

  find ${pkgdir}/usr/share -type f -exec chmod 644 {} \;
  find ${pkgdir}/usr/share -type d -exec chmod 755 {} \;

  msg2 "Installing licenses"
  install -d ${pkgdir}/usr/share
  cp -rv ${srcdir}/build/usr/share/doc ${pkgdir}/usr/share/
}
md5sums=('74acd042fcd85bee9edb093cc3fe5c2e'
         'SKIP'
         '0d5b6d7adf6fdcea52dcbb7b62368b19')
