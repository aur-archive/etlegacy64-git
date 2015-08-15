# Maintainer: Spyhawk
#
# Full 64 bit ET:Legacy clients can only connect to ET:Legacy servers running mods providing a 64 bit version.
# If you want to connect to existing  32 bit only mods, install the cross-compiled "etlegacy-git" package instead.
# Omni-Bot server support requires the 32 bit cross-compiled version.
# Other optional deps: 'openal' 'ncurses' 'jansson'

pkgbase=etlegacy64-git
pkgname=('etlegacy64-git' 'etlegacy64-mod-git')
pkgver=2.72.428.gc4e238d
pkgrel=1
arch=('x86_64')
url="http://www.etlegacy.com/"
license=('GPL3')
makedepends=('git' 'cmake' 'zip' 'alsa-lib' 'curl' 'freetype2' 'glew' 'libjpeg-turbo' 'libvorbis' 'lua' 'sdl2')
options=(!strip)
source=('git://github.com/etlegacy/etlegacy.git')
md5sums=('SKIP')
_gitname=etlegacy

pkgver() {
    cd "$srcdir/$_gitname"
    git describe --always | sed -r 's/^v//;s/-/./g;'
}

prepare() {
    # cleanup
    [[ -e "$HOME/.etlegacy/legacy" ]] && cd $HOME/.etlegacy/legacy && rm -f *.so && rm -f *.dat
    cd "$srcdir/$_gitname"
    rm -rf CMakeFiles include legacy glsl
    rm -f etl etlded *.so *.a *.cmake CMakeCache.txt install_manifest.txt
}

build() {
    cd "$srcdir/$_gitname"

    # build type and path
    cmakeopts+=(
        "-D CMAKE_BUILD_TYPE=Debug"
        "-D CMAKE_INSTALL_PREFIX=/usr"
        "-D INSTALL_DEFAULT_BASEDIR=/usr/lib/etlegacy"
        "-D INSTALL_DEFAULT_BINDIR=bin"
        "-D INSTALL_DEFAULT_MODDIR=lib/etlegacy"
    )
    # arch
    cmakeopts+=(
        "-D CMAKE_LIBRARY_PATH=/usr/lib"
        "-D CMAKE_INCLUDE_PATH=/usr/include"
        "-D CROSS_COMPILE32=0"
    )
    # options
    cmakeopts+=(
        "-D BUILD_SERVER=1"
        "-D BUILD_CLIENT=1"
        "-D BUILD_MOD=1"
    )
    # packing
    cmakeopts+=(
        "-D BUILD_MOD_PK3=1"
        "-D BUILD_PAK3_PK3=1"
    )
    # bundled libs
    cmakeopts+=(
        "-D BUNDLED_LIBS=0"
        "-D BUNDLED_SDL=0"
        "-D BUNDLED_CURL=0"
        "-D BUNDLED_JPEG=0"
        "-D BUNDLED_LUA=0"
        "-D BUNDLED_OGG_VORBIS=0"
        "-D BUNDLED_GLEW=0"
        "-D BUNDLED_FREETYPE=0"
        "-D BUNDLED_JANSSON=0"
    )
    # features
    cmakeopts+=(
        "-D FEATURE_CURL=1"
        "-D FEATURE_OGG_VORBIS=1"
        "-D FEATURE_OPENAL=0"
        "-D FEATURE_FREETYPE=1"
        "-D FEATURE_TRACKER=0"
        "-D FEATURE_LUA=1"
        "-D FEATURE_MULTIVIEW=0"
        "-D FEATURE_ANTICHEAT=1"
        "-D FEATURE_CURSES=0"
        "-D FEATURE_AUTOUPDATE=0"
        "-D FEATURE_RENDERER2=0"
        "-D FEATURE_RENDERER_GLES=0"
        "-D FEATURE_IPV6=0"
        "-D FEATURE_IRC_CLIENT=0"
        "-D RENDERER_DYNAMIC=0"
        "-D FEATURE_WINDOWS_CONSOLE=1"
        "-D FEATURE_GETTEXT=1"
        "-D FEATURE_JANSSON=0"
        "-D FEATURE_SERVERMDX=1"
        "-D FEATURE_LIVEAUTH=1"
    )
    # omnibot
    cmakeopts+=(
        "-D FEATURE_OMNIBOT=1"
        "-D INSTALL_OMNIBOT=0"
    )

    cmake ${cmakeopts[@]} .
    make
}

package_etlegacy64-git() {
    pkgdesc="Wolfenstein: Enemy Territory 2.60b compatible client/server (etlegacy engine, 64 bit)"
    depends=('etlegacy64-mod-git' 'alsa-lib' 'curl' 'freetype2' 'gcc-libs' 'glew' 'libjpeg' 'libvorbis' 'lua' 'sdl2')
    provides=('etlegacy64' 'etlegacy' 'etlegacy-git')
    conflicts=('etlegacy64' 'etlegacy' 'etlegacy-git')
    backup=('etc/xdg/etlegacy/etmain/etl_server.cfg'
            'etc/xdg/etlegacy/etmain/legacy.cfg'
            'etc/xdg/etlegacy/etmain/campaigncycle.cfg'
            'etc/xdg/etlegacy/etmain/lmscycle.cfg'
            'etc/xdg/etlegacy/etmain/mapvotecycle.cfg'
            'etc/xdg/etlegacy/etmain/objectivecycle.cfg')
    install=etlegacy.install

    cd "$srcdir/$_gitname"

    # engine
    make DESTDIR="$pkgdir/" install

    # mod
    rm -rf $pkgdir/usr/lib/$_gitname/legacy

    # assets
    ln -s /usr/share/enemy-territory/etmain/{pak0,pak1,pak2}.pk3 $pkgdir/usr/lib/$_gitname/etmain/

    # doc
    mkdir -p $pkgdir/usr/share/doc/$_gitname
    mv $pkgdir/usr/lib/$_gitname/INSTALL.txt $pkgdir/usr/share/doc/$_gitname/
    install -m 644 docs/game/anticheat.html $pkgdir/usr/share/doc/$_gitname/

    # license
    mkdir -p $pkgdir/usr/share/licenses/$_gitname
    mv $pkgdir/usr/lib/$_gitname/COPYING.txt $pkgdir/usr/share/licenses/$_gitname/

    # systemd
    mkdir -p $pkgdir/etc/xdg/$_gitname/etmain
    mkdir -p $pkgdir/usr/lib/systemd/system
    install -m 644 misc/etlegacy.conf $pkgdir/etc/xdg/$_gitname/
    install -m 644 misc/etlegacy.service $pkgdir/usr/lib/systemd/system/

    # config
    mv $pkgdir/usr/lib/$_gitname/etmain/*.cfg $pkgdir/etc/xdg/$_gitname/etmain/
    ln -s /etc/xdg/$_gitname/etmain/{etl_server,legacy,{campaign,lms,mapvote,objective}cycle}.cfg $pkgdir/usr/lib/$_gitname/etmain/

    # server
    mkdir -p $pkgdir/var/lib/$_gitname-server
    ln -s /etc/xdg/$_gitname/etmain $pkgdir/var/lib/$_gitname-server/
}

package_etlegacy64-mod-git() {
    pkgdesc="Wolfenstein: Enemy Territory 2.60b compatible client/server (etlegacy mod, 64 bit)"
    depends=('enemy-territory-data' 'geoip-database')
    optdepends=('etlegacy-omnibot: artificial intelligence (AI) controlled opponent')
    provides=('etlegacy64-mod' 'etlegacy-mod' 'etlegacy-mod-git')
    conflicts=('etlegacy64-mod' 'etlegacy-mod' 'etlegacy-mod-git')

    cd "$srcdir/$_gitname"

    # mod
    mkdir -p $pkgdir/usr/lib/$_gitname/legacy
    install legacy/{*.pk3,qagame.*.so} -t $pkgdir/usr/lib/$_gitname/legacy

    # geoip
    ln -s /usr/share/GeoIP/GeoIP.dat $pkgdir/usr/lib/$_gitname/legacy
}

# vim:set ts=4 sw=2 et:
