pkgname=qt6-qtwebengine
pkgver=6.9.2
pkgrel=1
pkgdesc="Provides support for web applications using the Chromium browser project"
arch=('x86_64')
url="https://www.qt.io"
license=(
    'GPL-3.0-only'
    'LGPL-3.0-only'
    'LicenseRef-Qt-Commercial'
    'Qt-GPL-exception-1.0'
)
depends=(
    'alsa-lib'
    'dbus'
    'expat'
    #'ffmpeg'
    'fontconfig'
    'freetype2'
    'gcc-libs'
    'glib2'
    'glibc'
    'harfbuzz'
    'icu'
    'lcms2'
    'libdrm'
    'libevent'
    'libjpeg-turbo'
    'libpng'
    'libtiff'
    'libwebp'
    'libx11'
    'libxcb'
    'libxcomposite'
    'libxfixes'
    'libxkbcommon'
    'libxkbfile'
    'libxdamage'
    'libxext'
    'libxml2'
    'libxrandr'
    'libxslt'
    'libxtst'
    'mesa'
    'minizip'
    'nspr'
    'nss'
    'openjpeg'
    'opus'
    'qt6-qtbase'
    'qt6-qtdeclarative'
    'qt6-qtpositioning'
    'qt6-qtwebchannel'
    'dejavu-fonts-ttf'
    're2'
    'snappy'
    'zlib'
)
makedepends=(
    'cmake'
    'git'
    'gperf'
    'jsoncpp'
    'libepoxy'
    'libpulse'
    'libva'
    'libxcursor'
    'ninja'
    'nodejs'
    'perf'
    'pipewire'
    'python-html5lib'
    'qt6-qttools'
    'qt6-qtwebsockets'
)
_chromium=20a5757595b39150c1cab9eccf04cc8057dd1462
source=(git+https://code.qt.io/qt/${pkgname#*-}#tag=v${pkgver}
    git+https://code.qt.io/qt/qtwebengine-chromium)
sha256sums=(81ce308ea2295797d0e9a65c72cb774b63f28dc87e2dd45631c8979d2143a192
    SKIP)

prepare() {
  cd ${pkgname#*-}

    git submodule init
    git submodule set-url src/3rdparty ${srcdir}/qtwebengine-chromium
    git -c protocol.file.allow=always submodule update

    # Fix rendering glitches (see https://bugreports.qt.io/browse/QTBUG-138641
    # and https://bugreports.qt.io/browse/QTBUG-139091).
    git cherry-pick -n 9dd5105673f30fa6e8dc9bb3ee5cf4f51cec6ed2

    # Revert commit that breaks GPU rendering https://bugreports.qt.io/browse/QTBUG-139424
    git revert -n ddcd30454aa6338d898c9d20c8feb48f36632e16

    # Bump chromium to head of stable branch
    cd src/3rdparty
    [[ -n ${_chromium} ]] && git checkout ${_chromium} || true
}

build() {
    cd ${pkgname#*-}

    local cmake_args=(
        -B flarebird-build
        -G Ninja
        -D CMAKE_BUILD_TYPE=Release
        -D INSTALL_LIBDIR=lib64
        -D CMAKE_MESSAGE_LOG_LEVEL=STATUS
        -D CMAKE_TOOLCHAIN_FILE=/usr/lib64/cmake/Qt6/qt.toolchain.cmake
        -D QT_FEATURE_webengine_system_ffmpeg=OFF
        -D QT_FEATURE_webengine_system_icu=ON
        -D QT_FEATURE_webengine_system_libevent=ON
        -D QT_FEATURE_webengine_system_re2=ON
        -D QT_FEATURE_webengine_proprietary_codecs=ON
        -D QT_FEATURE_webengine_webrtc_pipewire=ON
        -D QT_FEATURE_webengine_system_libxml=ON
        -D QT_FEATURE_webengine_system_opus=ON
        -D QT_FEATURE_webengine_kerberos=ON
        -D QT_FEATURE_webengine_system_libevent=ON
        -D QT_BUILD_EXAMPLES_BY_DEFAULT=OFF
        -D QT_GENERATE_SBOM=OFF
    )

    cmake "${cmake_args[@]}"

    cmake --build flarebird-build
}

package() {
    cd ${pkgname#*-}

    DESTDIR=${pkgdir} cmake --install flarebird-build
}
