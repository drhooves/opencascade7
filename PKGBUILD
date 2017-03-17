# Maintainer: Markus Hovorka <m.hovorka@live.de>

pkgbase='opencascade7'
pkgname=('opencascade7' 'opencascade7-docs')
pkgdesc="3D modeling & numerical simulation SDK"
pkgver=7.1.0
pkgrel=1
url="http://www.opencascade.org"
arch=('i686' 'x86_64')
license=('custom')
makedepends=('cmake' 'flex' 'bison' 'doxygen' 'graphviz' 'qt5-webkit' 'gl2ps'
             'freeimage' 'tk' 'glu' 'libxmu' 'vtk' 'mesa' 'intel-tbb')
source=("opencascade-$pkgver.tgz::http://git.dev.opencascade.org/gitweb/?p=occt.git;a=snapshot;h=refs/tags/V${pkgver//./_};sf=tgz"
        "fix-destdir.patch"
	"fix-absolute-path.patch")
md5sums=('12ff27bba1199a928aa6b2dfedbdde43'
         '1efcd714f9c1765b2918e412353dc92d'
         '13dec13d8423bbb399452a61d66ec78b')

prepare() {
    cd "occt-V${pkgver//./_}"
    patch -Np1 -i "$srcdir/fix-destdir.patch"
    patch -Np1 -i "$srcdir/fix-absolute-path.patch"
}

build() {
    cd "occt-V${pkgver//./_}"

    cmake \
        -Wno-dev \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX="/opt/${pkgbase}" \
        -DUSE_FREEIMAGE=ON \
        -DUSE_GL2PS=ON \
        -DUSE_TBB=ON \
        -DUSE_VTK=ON
    make

    # Documentation must be built separately.
    ./gendoc -html -overview
    ./gendoc -html -refman
}

package_opencascade7() {
    depends=('gl2ps' 'freeimage' 'tk' 'glu' 'libxmu' 'vtk' 'mesa' 'intel-tbb')
    cd "occt-V${pkgver//./_}"

    make DESTDIR="${pkgdir}" install

    # Fix permission of draw.sh script.
    chmod 755 "${pkgdir}/opt/${pkgbase}/bin/draw.sh"

    # Remove compile scripts. If they aren't removed opencascades
    # buildsystem tries to access (and modify?!) them in subsequent
    # build attempts. Also they are quite useless.
    rm "${pkgdir}/opt/${pkgbase}/bin/"custom*.sh

    # Remove doc folder with licence files (wrong location).
    rm -R "${pkgdir}/opt/${pkgbase}/share/doc"

    # Install license files into the right location.
    install -Dm644 -t "${pkgdir}/usr/share/licenses/${pkgbase}/" LICENSE_LGPL_21.txt
    install -Dm644 -t "${pkgdir}/usr/share/licenses/${pkgbase}/" OCCT_LGPL_EXCEPTION.txt
}

package_opencascade7-docs() {
    pkgdesc="3D modeling & numerical simulation SDK - documentation"
    cd "occt-V${pkgver//./_}"

    # Install separately built documentation and remove empty dir.
    install -d "${pkgdir}/usr/share/doc/${pkgbase}"
    cp -R doc/overview "${pkgdir}/usr/share/doc/${pkgbase}"
    cp -R doc/refman "${pkgdir}/usr/share/doc/${pkgbase}"
    rm -d "${pkgdir}/usr/share/doc/${pkgbase}/overview/latex"
}
