changecom()dnl
dnl Process this file with autoconf to produce a configure script.
AC_PREREQ([2.68])
AC_INIT([zbar],[0.23.93],[mchehab+huawei@kernel.org])
m4_ifndef([AC_LANG_DEFINES_PROVIDED],
          [m4_define([AC_LANG_DEFINES_PROVIDED])])
AC_CONFIG_AUX_DIR(config)
AC_CONFIG_MACRO_DIR(config)
AM_INIT_AUTOMAKE([1.13 -Werror foreign subdir-objects std-options dist-bzip2])
m4_pattern_allow([AM_PROG_AR])
AC_CONFIG_HEADERS([include/config.h])
AC_CONFIG_SRCDIR(zbar/scanner.c)
LT_PREREQ([2.2])
LT_INIT([dlopen win32-dll])
LT_LANG([Windows Resource])
AM_SILENT_RULES([yes])

dnl update these just before each release (along w/package version above)
dnl   LIB_VERSION update instructions copied from libtool docs:
dnl   library version follows the form current:revision:age
dnl   - If the library source code has changed at all since the last update,
dnl     then increment revision (c:r:a becomes c:r+1:a).
dnl   - If any interfaces have been added, removed, or changed,
dnl     increment current, and set revision to 0.
dnl   - If any interfaces have been added since the last public release,
dnl     then increment age.
dnl   - If any interfaces have been removed since the last public release,
dnl     then set age to 0.
AC_SUBST([LIB_VERSION], [3:0:3])
AC_SUBST([RELDATE], [2017-04-11])

dnl widget libraries use their own versioning.
dnl NB pygtk wrapper is *unversioned*
AC_SUBST([ZGTK_LIB_VERSION], [0:2:0])
AC_SUBST([ZQT_LIB_VERSION], [1:2:1])

AC_DEFINE_UNQUOTED([ZBAR_VERSION_MAJOR],
  [[`echo "$PACKAGE_VERSION" | sed -e 's/\..*$//'`]],
  [Program major version (before the '.') as a number])
AC_DEFINE_UNQUOTED([ZBAR_VERSION_MINOR],
  [[`echo "$PACKAGE_VERSION" | sed -e 's/^[^\.]*\.\([^\.]*\).*/\1/'`]],
  [Program minor version (after '.') as a number])
AC_DEFINE_UNQUOTED([ZBAR_VERSION_PATCH],
  [[`echo "$PACKAGE_VERSION" | sed -e 's/^[^\.]*\.[^\.]*\.*//' | sed s,^$,0,`]],
  [Program patch version (after the second '.') as a number])

cur=`echo "$LIB_VERSION" | sed -e 's/:.*$//'`
age=`echo "$LIB_VERSION" | sed -e 's/^.*://'`
AC_DEFINE_UNQUOTED([LIB_VERSION_MAJOR], [[$(( $cur - $age ))]],
  [Library major version])
AC_DEFINE_UNQUOTED([LIB_VERSION_MINOR], [[$age]],
  [Library minor version])
AC_DEFINE_UNQUOTED([LIB_VERSION_REVISION],
  [[`echo "$LIB_VERSION" | sed -e 's/^[^:]*:\([^:]*\):.*$/\1/'`]],
  [Library revision])

AM_CPPFLAGS="-I\$(top_srcdir)/include"
AM_CFLAGS="-Wall -Wno-parentheses"
AM_CXXFLAGS="$AM_CFLAGS"
AC_SUBST([AM_CPPFLAGS])
AC_SUBST([AM_CFLAGS])
AC_SUBST([AM_CXXFLAGS])

dnl windows build

AC_CANONICAL_HOST
case $host_os in
  *cygwin* | *mingw* | *uwin* | *djgpp* | *ems* )
    win32="yes"
    with_dbus="no"
    AC_DEFINE([_WIN32_WINNT], [0x0500], [Minimum Windows API version])
    AC_FUNC_ALLOCA
    AC_FUNC_ERROR_AT_LINE
    AC_FUNC_FSEEKO
    AC_CHECK_HEADERS([arpa/inet.h malloc.h mntent.h netdb.h netinet/in.h shadow.h sys/file.h sys/mount.h sys/param.h sys/socket.h sys/statfs.h sys/statvfs.h sys/vfs.h unistd.h values.h])
  ;;
  * )
    win32="no"
  ;;
esac
AM_CONDITIONAL([WIN32], [test "x$win32" = "xyes"])

dnl programs

AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CXX

PKG_PROG_PKG_CONFIG

dnl symbologies

AC_ARG_ENABLE([codes],
  [AS_HELP_STRING([--enable-codes=SYMS],
    [select symbologies to compile [default=ean,databar,code128,code93,code39,codabar,i25,qrcode,sqcode]])],
  [],
  [enable_codes="ean,databar,code128,code93,code39,codabar,i25,qrcode,sqcode"])

AC_DEFUN([AC_DEFINE_SUBST],
   [AC_DEFINE($1,$2,$3)
    AC_SUBST($1,$2)])

AC_DEFUN([ZBAR_CHK_CODE], [
  AC_MSG_CHECKING([whether to build $2])
  enable_$1="no"
  AH_TEMPLATE([ENABLE_]translit($1, a-z, A-Z),
    [whether to build support for $2])
  AS_CASE([$enable_codes],
    [*$1* | *all*],
    [enable_$1="yes"
     enabled_codes="$enabled_codes $1"
     AC_DEFINE_SUBST([ENABLE_]translit($1, a-z, A-Z), [1])
    ], [
     disabled_codes="$disabled_codes $1"
     AC_DEFINE_SUBST([ENABLE_]translit($1, a-z, A-Z), [0])
  ])
  AM_CONDITIONAL([ENABLE_]translit($1, a-z, A-Z),
    [test "x$enable_$1" = "xyes"])
  AC_MSG_RESULT([$enable_$1])
])dnl

ZBAR_CHK_CODE([ean], [EAN symbologies])
ZBAR_CHK_CODE([databar], [DataBar symbology])
ZBAR_CHK_CODE([code128], [Code 128 symbology])
ZBAR_CHK_CODE([code93], [Code 93 symbology])
ZBAR_CHK_CODE([code39], [Code 39 symbology])
ZBAR_CHK_CODE([codabar], [Codabar symbology])
ZBAR_CHK_CODE([i25], [Interleaved 2 of 5 symbology])
ZBAR_CHK_CODE([qrcode], [QR Code])
ZBAR_CHK_CODE([sqcode], [SQ Code])
ZBAR_CHK_CODE([pdf417], [PDF417 symbology (incomplete)])

dnl libraries

AC_SEARCH_LIBS([clock_gettime], [rt pthread])
AM_ICONV()
AM_GNU_GETTEXT([external])
AM_GNU_GETTEXT_VERSION(0.20)
AM_GNU_GETTEXT_REQUIRE_VERSION(0.18)
AM_CONDITIONAL([USE_NLS], [test "x${USE_NLS}" = "xyes"])

dnl libraries linkage
AC_ARG_ENABLE([static_qt],
  [AS_HELP_STRING([--enable-static-qt],
    [Produce a static library for libzbarcam-qt])])

AS_IF([test x$enable_static_qt = xyes], [AC_SUBST([LIBQT_EXTRA_LDFLAGS], ["-static"])])


dnl poll support

AC_CHECK_HEADERS([poll.h], [have_poll="yes"], [have_poll="no"])
AM_CONDITIONAL([HAVE_POLL], [test "x$have_poll" = "xyes"])

dnl pthreads
dnl FIXME this doesn't port well, integrate something like this:
dnl     http://autoconf-archive.cryp.to/acx_pthread.html

AC_ARG_ENABLE([pthread],
  [AS_HELP_STRING([--disable-pthread],
    [omit support for threaded applications])],
  [],
  [AS_IF([test "x$win32" = "xno"],
   [enable_pthread="yes"],
   [enable_pthread="no"
])])

AS_IF([test "x$enable_pthread" != "xno"],
  [AC_CHECK_HEADERS([pthread.h], [],
     [AC_MSG_FAILURE([test for pthread support failed!
configure --disable-pthread to skip threaded support.])])
   AC_CHECK_LIB([pthread], [pthread_create], [],
     [AC_MSG_FAILURE([unable to link against -lpthread, although you
appear to have pthread.h? set LDFLAGS and/or LIBS to help the linker,
or configure --disable-pthread to skip threaded support.])])
   AC_DEFINE([__USE_UNIX98], [1], [used only for pthread debug attributes])
])

dnl doc
AC_ARG_ENABLE([doc],
  [AS_HELP_STRING([--disable-doc],
    [disable building docs])],
  [],
  [enable_doc="yes"])

AS_IF([test "x$enable_doc" != "xno"],
  [AC_ARG_VAR([XMLTO], [location of xmlto, used for optional documentation generation])
   AC_ARG_VAR([XMLTOFLAGS], [additional arguments for xmlto])
   AC_CHECK_PROGS([XMLTO], [xmlto])])

AS_IF([test "x$XMLTO" = "x"], enable_doc="no")

AM_CONDITIONAL([HAVE_DOC], [test "x$enable_doc" != "xno"])

dnl video
AC_ARG_ENABLE([video],
  [AS_HELP_STRING([--disable-video],
    [exclude video scanner features])],
  [],
  [enable_video="yes"])

dnl video, directshow
AC_ARG_WITH([directshow],
  [AS_HELP_STRING([--with-directshow],
     [compile directshow driver on windows instead of vfw,
available only when video support is enabled])],
  [],
  [with_directshow="no"])

have_v4l1="no"
have_v4l2="no"
have_libv4l="no"
AS_IF([test "x$enable_video" = "xno"],
  [],
  [test "x$win32" = "xno"],
  [AC_CHECK_HEADERS([linux/videodev.h], [have_v4l1="yes"])
   AC_CHECK_HEADERS([linux/videodev2.h], [have_v4l2="yes"])
   AC_CHECK_HEADERS([libv4l2.h], [have_libv4l="yes"])
   AS_IF([test "x$have_v4l2" = "xno" && test "x$have_v4l1" = "xno"],
         [AC_MSG_FAILURE([test for video support failed!
rebuild your kernel to include video4linux support or
configure --disable-video to skip building video support.])],
         [AS_IF([test "x$have_v4l2" = "xno"],
               [AC_MSG_WARN([v4l2 API not detected, upgrade your kernel!])])]
        )],
  [AS_IF([test "x$with_directshow" != "xno"],
    [with_video="directshow"],
    [AC_CHECK_HEADERS([vfw.h], [with_video="vfw"],
      [AC_MSG_FAILURE([test for VfW video support failed!
configure --disable-video to skip building video support.])],[#include <windows.h>])])])

AS_IF([test "x$have_libv4l" = "xyes"],
   [PKG_CHECK_MODULES([V4L2], [libv4l2], [],
     [AC_MSG_FAILURE([unable to find libv4l2.so])])],
  [AC_MSG_WARN([libv4l not detected. Install it to support more cameras!])])

AM_CONDITIONAL([HAVE_VIDEO], [test "x$enable_video" != "xno"])
AM_CONDITIONAL([HAVE_V4L1], [test "x$have_v4l1" != "xno"])
AM_CONDITIONAL([HAVE_V4L2], [test "x$have_v4l2" != "xno"])
AM_CONDITIONAL([HAVE_LIBV4L], [test "x$have_libv4l" != "xno"])
AM_CONDITIONAL([WITH_DIRECTSHOW], [test "x$with_directshow" != "xno"])

dnl X
AC_ARG_VAR([XSHM_LIBS], [linker flags for X shared memory extension])

AS_IF([test "x$win32" != "xno"],
  [have_x="no"],
  [AC_PATH_XTRA
   AH_BOTTOM([#ifndef X_DISPLAY_MISSING
# define HAVE_X
#endif
])])
AM_CONDITIONAL([HAVE_X], [test "x$have_x" = "xyes"])

AS_IF([test "x$XSHM_LIBS" = "x"], [XSHM_LIBS="-lXext"])
AC_ARG_WITH([xshm],
  [AS_HELP_STRING([--without-xshm],
    [disable support for X shared memory extension])],
  [],
  [with_xshm="check"])

AS_IF([test "x$with_xshm" != "xno"],
  [AC_CHECK_HEADERS([X11/extensions/XShm.h],
    [with_xshm="yes"],
    [AS_IF([test "x$with_xshm" = "xcheck"],
      [with_xshm="no"],
      [AC_MSG_FAILURE([test for X shared memory extension failed!
install the X shared memory extension, specify --x-includes or
configure --without-xshm to disable the extension])])],
    [[#include <X11/Xlib.h>
#include <sys/ipc.h>
#include <sys/shm.h>
]])
   AS_IF([test "x$with_xshm" != "xno"],
     [AC_CHECK_LIB([Xext], [XShmQueryVersion],
       [with_xshm="yes"],
       [AC_MSG_FAILURE([unable to find XShmQueryVersion in $XSHM_LIBS!
specify XSHM_LIBS or configure --without-xshm to disable the extension])],
       ["$X_LIBS" "$X_PRE_LIBS" -lX11 "$X_EXTRA_LIBS" "$XSHM_LIBS"])
   ])
])
AM_CONDITIONAL([HAVE_XSHM], [test "x$with_xshm" = "xyes"])

AC_ARG_VAR([XV_LIBS], [linker flags for XVideo extension])
AS_IF([test "x$XV_LIBS" = "x"], [XV_LIBS="-lXv"])
AC_ARG_WITH([xv],
  [AS_HELP_STRING([--without-xv],
    [disable support for XVideo extension])],
  [],
  [with_xv="check"])

AS_IF([test "x$with_xv" != "xno"],
  [AC_CHECK_HEADERS([X11/extensions/Xvlib.h],
    [with_xv="yes"],
    [AS_IF([test "x$with_xv" = "xcheck"],
      [with_xv="no"],
      [AC_MSG_FAILURE([test for XVideo extension failed!
install the XVideo extension, specify --x-includes or
configure --without-xv to disable the extension])])],
    [[#include <X11/Xlib.h>
]])
   AS_IF([test "x$with_xv" != "xno"],
     [AC_CHECK_LIB([Xv], [XvQueryExtension],
       [with_xv="yes"],
       [AC_MSG_FAILURE([unable to find XvQueryExtension in $XV_LIBS!
specify XV_LIBS or configure --without-xv to disable the extension])],
       ["$X_LIBS" "$X_PRE_LIBS" -lX11 "$X_EXTRA_LIBS" "$XV_LIBS"])
   ])
])
AM_CONDITIONAL([HAVE_XV], [test "x$with_xv" = "xyes"])

dnl dbus
AC_ARG_WITH([dbus],
  [AS_HELP_STRING([--without-dbus],
    [disable support for dbus])],
  [],
  [with_dbus="check"])

AS_IF([test "x$with_dbus" != "xno"],
  [PKG_CHECK_MODULES(DBUS, dbus-1 >= 1.0, have_dbus="yes", have_dbus="no")
  AS_IF([test "x$have_dbus$with_dbus" = "xnoyes"],
    [AC_MSG_FAILURE([DBus development libraries not found])],
    [with_dbus="$have_dbus"])
])
AM_CONDITIONAL([HAVE_DBUS], [test "x$with_dbus" = "xyes"])

AS_IF([test "x$with_dbus" = "xyes"],
  [CPPFLAGS="$CPPFLAGS $DBUS_CFLAGS"
  AC_ARG_VAR([DBUS_LIBS], [linker flags for building dbus])
  AC_DEFINE([HAVE_DBUS], [1], [Define to 1 to use dbus])
  AC_ARG_WITH(dbusconfdir, AS_HELP_STRING([--with-dbusconfdir=PATH],[path to D-Bus config directory]),
  [path_dbusconf=$withval],
  [path_dbusconf="`$PKG_CONFIG --variable=sysconfdir dbus-1`"])
  AS_IF([test -z "$path_dbusconf"],
    DBUS_CONFDIR="$sysconfdir/dbus-1/system.d",
    DBUS_CONFDIR="$path_dbusconf/dbus-1/system.d")
  AC_SUBST(DBUS_CONFDIR)
])

dnl libjpeg
AC_ARG_WITH([jpeg],
  [AS_HELP_STRING([--without-jpeg],
    [disable support for JPEG image conversions])],
  [],
  [with_jpeg="check"])

have_jpeg="maybe"
AS_IF([test "x$with_jpeg" != "xno"],
  [AC_CHECK_HEADERS([jpeglib.h jerror.h], [], [have_jpeg="no"])
   AC_CHECK_LIB([jpeg], [jpeg_read_header], [], [have_jpeg="no"])
   AS_IF([test "x$have_jpeg" != "xno"],
     [with_jpeg="yes"],
     [test "x$with_jpeg" = "xyes"],
     [AC_MSG_FAILURE([unable to find libjpeg! ensure CFLAGS/LDFLAGS are
set appropriately or configure --without-jpeg])],
     [with_jpeg="no"])
])
AM_CONDITIONAL([HAVE_JPEG], [test "x$with_jpeg" = "xyes"])

dnl ImageMagick or GraphicsMagick
dnl disable both if IM is explicitly disabled to preserve old behavior

AC_ARG_WITH([imagemagick],
  [AS_HELP_STRING([--without-imagemagick],
    [disable support for scanning images with ImageMagick])],
  [],
  [with_imagemagick="check"])

AC_ARG_WITH([graphicsmagick],
  [AS_HELP_STRING([--with-graphicsmagick],
    [use GraphicsMagick alternative to ImageMagick])],
  [],
  [with_graphicsmagick="check"])

magick="UnknownMagick"
have_IM="maybe"
AS_IF([test "x$with_imagemagick" = "xno"], [],
      [test "x$with_imagemagick" = "xyes" || \
       test "x$with_graphicsmagick" != "xyes"],
  [looked_for="ImageMagick >= 6.2.6"
   PKG_CHECK_MODULES([MAGICK], [MagickWand >= 6.2.6],
     [MAGICK_VERSION=`$PKG_CONFIG MagickWand --modversion`],
     [dnl
dnl Wand is deprecated in favor of MagickWand,
dnl but the latter doesn't exist in older versions (bug #2848437)
      saved_error=$MAGICK_PKG_ERRORS
      PKG_CHECK_MODULES([MAGICK], [Wand >= 6.2.6],
        [MAGICK_VERSION=`$PKG_CONFIG Wand --modversion`],
        [have_IM="no"])])
   AS_IF([test "x$have_IM" != "xno"],
     [magick="ImageMagick"
      AC_MSG_NOTICE([trying ImageMagick version $MAGICK_VERSION])
dnl double check ImageMagick install (bug #2582232)
      CPPFLAGS_save="$CPPFLAGS"
      CPPFLAGS="$CPPFLAGS $MAGICK_CFLAGS"
      AC_CHECK_HEADERS([wand/MagickWand.h],
        [
          have_IM="yes"
          with_imagemagick="yes"
        ],
        [have_IM="broken"])
dnl check for ImageMagick 7, see https://imagemagick.org/script/porting.php#headers
      AS_IF([test "x$have_IM" = "xbroken"], [
        AC_CHECK_HEADERS([MagickWand/MagickWand.h],
          [
            have_IM="yes"
            have_IM7="yes"
            with_imagemagick="yes"
          ],
          [have_IM="broken"])
      ])
      CPPFLAGS="$CPPFLAGS_save"])])

have_GM="maybe"
AS_IF([test "x$have_IM" = "xyes"], [],
      [test "x$with_graphicsmagick" = "xno"], [],
      [test "x$with_graphicsmagick" = "xyes" || \
       test "x$with_imagemagick" = "xcheck"],
  [AS_IF([test "x$looked_for" = "x"],
     [looked_for="GraphicsMagick"],
     [looked_for="$looked_for or GraphicsMagick"])
   PKG_CHECK_MODULES([GM], [GraphicsMagickWand],
     [have_GM="yes"
      with_imagemagick="no"
      magick="GraphicsMagick"
      MAGICK_CFLAGS="$MAGICK_CFLAGS $GM_CFLAGS"
      MAGICK_LIBS="$MAGICK_LIBS $GM_LIBS"
      MAGICK_VERSION=`$PKG_CONFIG GraphicsMagickWand --modversion`],
     [have_GM="no"
      AS_IF([test "x$saved_error" = "x"],
        [saved_error=$MAGICK_PKG_ERRORS])])])

dnl now that we have collected all the info abt what Magick is available
dnl let the user know what we will or can't do
AS_IF([test "x$have_IM" = "xbroken" && test "x$have_GM" = "xyes"],
  [
    with_imagemagick="no"
    AC_MSG_WARN([Your ImageMagick install is broken, using GraphicsMagick instead])
  ])
AS_IF([test "x$have_IM" = "xyes" || test "x$have_GM" = "xyes"],
  [AC_MSG_NOTICE([using $magick version $MAGICK_VERSION])],
  [test "x$with_imagemagick" = "xno" && \
   test "x$with_graphicsmagick" != "xyes"],
  [AC_MSG_NOTICE([image scanning disabled -- zbarimg will *not* be built])],
  [test "x$have_IM" = "xbroken"],
  [AC_MSG_FAILURE([$magick package found but wand/MagickWand.h not installed?!
this is a problem with your $magick install, please try again after
resolving the inconsistency or installing GraphicsMagick alternative...])],
  [test "x$with_graphicsmagick" = "xcheck"],
  [AC_MSG_NOTICE([ImageMagick/GraphicsMagick not detected. Several features will be disabled])
   with_imagemagick="no"],
  [AC_MSG_FAILURE([dnl
Unable to find $looked_for:

$saved_error

* Ensure that you installed any "development" packages for ImageMagick or
  GraphicsMagick.
* Consider adjusting the PKG_CONFIG_PATH environment variable if you
  installed software in a non-standard prefix.
* You may set the environment variables MAGICK_CFLAGS and MAGICK_LIBS
  to avoid the need to call pkg-config.
  See the pkg-config man page for more details.
* To avoid using ImageMagick or GraphicsMagick altogether you may add the
  --without-imagemagick flag to the configure command; the zbarimg program
  will *not* be built.
])])

AS_IF([test "x$have_IM" = "xyes"],
  [AC_DEFINE([HAVE_IMAGEMAGICK], [1], [Define to 1 to use ImageMagick])],
  [test "x$have_GM" = "xyes"],
  [AC_DEFINE([HAVE_GRAPHICSMAGICK], [1], [Define to 1 to use GraphicsMagick])])
AS_IF([test "x$have_IM7" = "xyes"],
  [AC_DEFINE([HAVE_IMAGEMAGICK7], [1], [Define to 1 to use ImageMagick 7])])
AM_CONDITIONAL([HAVE_MAGICK],
  [test "x$have_IM" = "xyes" || test "x$have_GM" = "xyes"])

dnl Mozilla NPAPI
AC_ARG_WITH([npapi],
  [AS_HELP_STRING([--with-npapi],
    [enable support for Firefox/Mozilla/OpenOffice plugin])],
  [],
  [with_npapi="no"])

AS_IF([test "x$with_npapi" != "xno"],
  [PKG_CHECK_MODULES([NPAPI], [firefox-plugin])
   NPAPI_VERSION=`$PKG_CONFIG firefox-plugin --modversion`
   AC_MSG_NOTICE([using firefox-plugin version $NPAPI_VERSION])])

AM_CONDITIONAL([HAVE_NPAPI], [test "x$with_npapi" = "xyes"])

dnl GTK
dnl For now, defaults to GTK version 3

AC_ARG_WITH([gtk],
  [AS_HELP_STRING([--with-gtk],
    [Specify support for GTK. Valid values are: no, auto, gtk2, gtk3 (default is auto)])],
  [AS_IF([test "x$with_gtk" != "xno"   && test "x$with_gtk" != "xauto" &&
          test "x$with_gtk" != "xgtk2" && test "x$with_gtk" != "xgtk3"],
         [echo "Invalid value for --with-gtk. Falling back to 'no'"
          with_gtk="no"])],
  [with_gtk="auto"])

AC_ARG_VAR([GLIB_GENMARSHAL], [full path to glib-genmarshal])
AC_ARG_VAR([GTK_VERSION_MAJOR])

AS_IF([test "x$with_gtk" = "xgtk3" || test "x$with_gtk" = "xauto"],
  [PKG_CHECK_MODULES([GTK3], [gtk+-3.0],
    [GLIB_GENMARSHAL=`$PKG_CONFIG glib-2.0 --variable=glib_genmarshal`
     GTK_VERSION=`$PKG_CONFIG gtk+-3.0 --modversion`
     AC_MSG_NOTICE([using GTK+ version $GTK_VERSION])
     GTK_VERSION_MAJOR=3.0
     with_gtk="gtk3"
     GTK_CFLAGS=$GTK3_CFLAGS
     GTK_LIBS=$GTK3_LIBS
    ],[libgtk3=false])
  ])

AS_IF([test "x$with_gtk" = "xgtk2" || test "x$with_gtk" = "xauto"],
  [PKG_CHECK_MODULES([GTK2], [gtk+-2.0],
    [GLIB_GENMARSHAL=`$PKG_CONFIG glib-2.0 --variable=glib_genmarshal`
     GTK_VERSION=`$PKG_CONFIG gtk+-2.0 --modversion`
     AC_MSG_NOTICE([using GTK+ version $GTK_VERSION])
     GTK_VERSION_MAJOR=2.0
     with_gtk="gtk2"
     GTK_CFLAGS=$GTK2_CFLAGS
     GTK_LIBS=$GTK2_LIBS
    ],[libgtk2=false])
  ])

dnl GTK not found
AS_IF([test "x$with_gtk" = "xauto"], [with_gtk="no"])

AC_SUBST(GTK_LIBS)
AC_SUBST(GTK_CFLAGS)

AM_CONDITIONAL([HAVE_GTK], [test "x$with_gtk" != "xno"])

AC_ARG_WITH([gir],
  [AS_HELP_STRING([--with-gir], [enable support for GObject Introspection])],
  [],
  [with_gir="yes"])

dnl Python
dnl For now, keep python2 as default
dnl If both PYTHON and --with-python=foo are defined, PYTHON takes precedence

AC_ARG_WITH([python],
  [AS_HELP_STRING([--with-python],
    [Specify support for Python. Valid values are: no, auto, python2, python3
(default is auto).
Please notice that PYTHON var, if especified, takes precedence.])],
  [AS_IF([test "x$with_python" != "xno"      && test "x$with_python" != "xauto" &&
          test "x$with_python" != "xpython2" && test "x$with_python" != "xpython3"],
         [echo "Invalid value for --with-python. Falling back to 'no'"
          with_python="xno"])],
  [with_python="auto"])

AC_ARG_VAR([PYTHON_CONFIG], [full path to python-config program])
AC_ARG_VAR([PYTHON_CFLAGS], [compiler flags for building python extensions])
AC_ARG_VAR([PYTHON_LDFLAGS], [linker flags for building python extensions])

AC_ARG_VAR([PYGTK_H2DEF], [full path to PyGTK h2def.py module (python2 only)])
AC_ARG_VAR([PYGTK_CODEGEN], [full path to pygtk-codegen program (python2 only)])
AC_ARG_VAR([PYGTK_DEFS], [directory where PyGTK definitions may be found (python2 only)])

AS_IF([test -z "$PYTHON"],
      [AS_IF([test "x$with_python" = "xauto"],
             [AC_PATH_PROGS([PYTHON], [python3 python2 python], [:], [$PATH])],
             [AS_IF([test "x$with_python" = "xpython3"],
                    [AC_PATH_PROGS([PYTHON], [python3 python], [:], [$PATH])],
                    [AS_IF([test "x$with_python" = "xpython2"],
                           [AC_PATH_PROGS([PYTHON], [python2 python], [:], [$PATH])],
                           [with_python="no"])
                    ])
             ]
      )],
      [with_python="auto"]
)

AS_IF([test "x$with_python" != "xno"],
      [AM_PATH_PYTHON([2.7.0])])

AS_IF([test "x$PYTHON_VERSION" != "x" && test "x$with_python" != "xno"],
  [PYTHON_VERSION_MAJOR="`echo $PYTHON_VERSION | cut -d'.' -f 1`"

   AS_IF([test "x$PYTHON_CFLAGS" != "x"],
     [],
     [test "x$PYTHON_CONFIG" != "x" && test -x "$PYTHON_CONFIG"],
     [PYTHON_CFLAGS=`$PYTHON_CONFIG --cflags`],
     [test -x "$PYTHON-config"],
     [PYTHON_CFLAGS=`$PYTHON-config --cflags`],
     [PYTHON_CFLAGS=`$PYTHON -c 'import distutils.sysconfig as s, sys; sys.stdout.write(" ".join(s.get_config_vars("CFLAGS")) + " -I"+s.get_python_inc() + " -I"+s.get_python_inc(plat_specific=True))'`])
   AS_IF([test "x$PYTHON_LDFLAGS" != "x"],
     [],
     [test "x$PYTHON_CONFIG" != "x" && test -x "$PYTHON_CONFIG"],
     [PYTHON_LDFLAGS=`$PYTHON_CONFIG --ldflags`],
     [test -x "$PYTHON-config"],
     [PYTHON_LDFLAGS=`$PYTHON-config --ldflags`],
     [PYTHON_LDFLAGS=`$PYTHON -c 'import distutils.sysconfig as s, sys; sys.stdout.write(" ".join(s.get_config_vars("LDFLAGS")) + " -I"+s.get_python_inc() + " -I"+s.get_python_inc(plat_specific=True))'`])

   dnl check that #include <Python.h> compiles (bug #3092663)
   CPPFLAGS_save="$CPPFLAGS"
   CPPFLAGS="$CPPFLAGS $PYTHON_CFLAGS"
   AS_IF([test "x$win32" = "xyes"], PYTHON_LDFLAGS="$PYTHON_LDFLAGS -no-undefined")
   AC_CHECK_HEADERS([Python.h], [],
     [AS_IF([test "x$with_python" = "xauto"],with_python="no",
       [AC_MSG_ERROR([dnl
Python module enabled, but unable to compile Python.h.
Install the development package for python-$am_cv_python_version, or configure
--without-python to disable the python bindings.dnl
])])])
   CPPFLAGS="$CPPFLAGS_save"

   dnl PyGTK
   dnl disable pygtk when we're on Python 3
   AS_IF([test "x$with_gtk" != "xno"],
     [AS_IF([test "x$PYTHON_VERSION_MAJOR" = "x2"],
       [PKG_CHECK_MODULES([PYGTK], [pygtk-2.0], [with_pygtk2="yes"], [with_pygtk2="no"])
        AC_CHECK_PROGS([PYGTK_CODEGEN], [pygobject-codegen-2.0 pygtk-codegen-2.0 pygtk-codegen], [:])

        AS_IF([test "x$PYGTK_H2DEF" = "x"],
          [PYGTK_H2DEF=`$PKG_CONFIG pygtk-2.0 --variable=codegendir`/h2def.py
           AS_IF([test -f "$PYGTK_H2DEF"], [], [PYGTK_H2DEF=":"])])
        AS_IF([test "x$PYGTK_DEFS" = "x"],
          [PYGTK_DEFS=`$PKG_CONFIG pygtk-2.0 --variable=defsdir`])
     ])
   ])
], [with_python="no"])

AS_IF([test "x$PYTHON_VERSION_MAJOR" != "x2"], [with_pygtk2="no"])
AM_CONDITIONAL([HAVE_PYTHON], [test "x$with_python" != "xno"])
AM_CONDITIONAL([HAVE_PYGTK2], [test "x$with_pygtk2" != "xno"])

dnl GObject Introspection (GIR)

AS_IF([test "x$with_gir" = "xyes" && test "x$with_gtk" != "xno"],
  [m4_ifdef([GOBJECT_INTROSPECTION_CHECK],
    [GOBJECT_INTROSPECTION_CHECK([0.6.7])
     AS_IF([test "x$found_introspection" = "xyes"],
       [INTROSPECTION_TYPELIBDIR=`$PKG_CONFIG --variable=typelibdir --define-variable="libdir=${libdir}" gobject-introspection-1.0`
        INTROSPECTION_GIRDIR=`$PKG_CONFIG --variable=girdir --define-variable="datadir=${datadir}" gobject-introspection-1.0`
        AC_SUBST(INTROSPECTION_TYPELIBDIR)
        AC_SUBST(INTROSPECTION_GIRDIR)])])
 ])

AS_IF([test "x$found_introspection" != "xyes"], [with_gir="no"])
AM_CONDITIONAL([HAVE_INTROSPECTION], [test "x$with_gir" = "xyes"])

dnl Qt
AC_ARG_WITH([qt],
  [AS_HELP_STRING([--without-qt],
    [disable support for Qt widget])],
  [],
  [with_qt="yes"])

dnl Qt6
AC_ARG_WITH([qt6],
  [AS_HELP_STRING([--with-qt6],
    [If --with-qt, enable experimental support for Qt6 widget (currently broken)])],
  [],
  [with_qt6="no"])

AS_IF([test "x$with_qt" != "xno"],
      [AS_IF([test "x$with_qt6" != "xno"],
	     PKG_CHECK_MODULES([QT],
                               [Qt6],
                               [MOC=`pkg-config Qt6 --variable=moc`
                                QT_VERSION=`$PKG_CONFIG Qt6 --modversion`
                                QT6_HEADERS=`pkg-config Qt6 --variable=headerdir`
                                CPPFLAGS="$CPPFLAGS -I$QT6_HEADERS -I$QT6_HEADERS/QtWidgets -I$QT6_HEADERS/QtCore -I$QT6_HEADERS/QtGui"
                                qt_pkgconfig_file="zbar-qt5.pc"
                               ],
                               [with_qt6="no"]))
       AS_IF([test "x$with_qt6" = "xno"],
             PKG_CHECK_MODULES([QT],
                               [Qt5Core >= 5 Qt5Gui >= 5 Qt5Widgets >= 5.0 Qt5X11Extras >= 5.0],
                               [MOC=`pkg-config Qt5 --variable=moc`
                                QT_VERSION=`$PKG_CONFIG Qt5 --modversion`
                                qt_pkgconfig_file="zbar-qt5.pc"
                               ],
                               [with_qt="no"]))
      ])

AC_ARG_VAR([MOC], [full path to Qt moc program])

AM_CONDITIONAL([HAVE_QT], [test "x$with_qt" = "xyes"])

AM_COND_IF([HAVE_QT],
           [AC_MSG_NOTICE([using Qt version $QT_VERSION])
            AC_MSG_NOTICE([using moc from $MOC])
            CPPFLAGS="$CPPFLAGS $QT_CPPFLAGS"
            LIBS="$LIBS $QT_LIBS"
            AC_CONFIG_FILES([zbar-qt.pc:"${qt_pkgconfig_file}.in"])
            #dnl -fPIC has no effect on Windows and breaks windres
            AS_IF([test "x$win32" = "xno"], [CPPFLAGS="$CPPFLAGS -fPIC"])])

dnl Java
have_java="maybe"

AC_ARG_VAR([JAVA_HOME], [root location of JDK])

AC_ARG_VAR([JAVAC], [location of Java language compiler])
AC_ARG_VAR([JAVAH], [location of Java header generator])

dnl If $JAVA_HOME not defined, try to autodetect it
AS_IF([test -z "$JAVA_HOME"],
      [AC_PATH_PROGS([JAVAC], [javac jikes ecj gcj], [:], [$PATH])
       AS_IF([test ! -z "$JAVAC"],
	     [JAVA_HOME=$( readlink -f ${JAVAC} | rev | cut -d/ -f3- | rev )])])

dnl If $JAVA_HOME is defined, set JAVA_PATH and JAVAC
AS_IF([test ! -z "$JAVA_HOME"],
      [JAVA_PATH="$JAVA_HOME/bin$PATH_SEPARATOR$PATH"
       AS_IF([test -z "$JAVAC"],
             [AC_PATH_PROGS([JAVAC], [javac jikes ecj gcj], [:], [$JAVA_PATH])])])

AC_ARG_WITH([java],
  [AS_HELP_STRING([--without-java],
    [disable support for Java interface])],
  [],
  [with_java="check"])

JAVAC=$(echo $JAVAC |sed 's/ecj/ecj -1.5/')

dnl Javah was obsoleted on Java 8 and removed on Java 11. So, we need to
dnl look strictly at the $JAVA_HOME in order to avoid mixing different versions
AS_IF([test -z "$JAVAH"],
      [AC_PATH_PROGS([JAVAH], [javah], [], [$JAVA_HOME/bin])])

AM_CONDITIONAL([HAVE_JAVAH], [test "x$JAVAH" != "x"])

AC_ARG_VAR([JAR], [location of Java archive tool])
AC_PATH_PROGS([JAR], [jar], [:], [$JAVA_PATH])
AS_IF([test "x$JAR" = "x:"], [have_java="no"])

AC_ARG_VAR([JAVA], [location of Java application launcher])
AC_PATH_PROGS([JAVA], [java], [/bin/false], [$JAVA_PATH])

AC_ARG_VAR([CLASSPATH], [Java class path (include JUnit to run java tests)])
AS_IF([test "x$CLASSPATH" = "x"], [CLASSPATH="."])

dnl Search for Java unit test library
AS_IF([test -z "$JUNIT_HOME"],
      [JUNIT_HOME="/usr/share/java"])

AS_IF([test -f "$JUNIT_HOME/junit4.jar"],
      [JUNIT="$JUNIT_HOME/junit4.jar"],
      [AS_IF([test -f "$JUNIT_HOME/junit.jar"],
	    [JUNIT="$JUNIT_HOME/junit.jar"])])

AS_IF([test "x$JUNIT" != "x"],
      [AS_IF([test -f "/usr/share/java/hamcrest/all.jar"],
	     [CLASSPATH="$JUNIT:/usr/share/java/hamcrest/all.jar:$CLASSPATH"
              AC_SUBST(CLASSPATH)
              with_java_unit="yes"])],
      [AS_IF([test -f "/usr/share/java/hamcrest-all.jar"],
             [CLASSPATH="$JUNIT:/usr/share/java/hamcrest-all.jar:$CLASSPATH"
              AC_SUBST(CLASSPATH)
              with_java_unit="yes"])])

AM_CONDITIONAL([HAVE_JAVA_UNIT], [test "x$with_java_unit" = "xyes"])

AC_ARG_VAR([JAVA_CFLAGS], [compiler flags for building JNI extensions])
AS_IF([test "x$JAVA_CFLAGS" = "x" && test "x$JAVA_HOME" != "x"],
  [JAVA_CFLAGS="-I$JAVA_HOME/include"])

AS_IF([test -d "$JAVA_HOME/include/linux"],
  [JAVA_CFLAGS="$JAVA_CFLAGS -I$JAVA_HOME/include/linux"])

AS_IF([test "x$with_java" != "xno"],
  [CPPFLAGS_save="$CPPFLAGS"
   CPPFLAGS="$CPPFLAGS $JAVA_CFLAGS"
   AC_CHECK_HEADERS([jni.h], [], [have_java="no"])
   CPPFLAGS="$CPPFLAGS_save"
   AS_IF([test "x$have_java" != "xno"],
     [with_java="yes"],
     [test "x$with_java" = "xyes"],
     [AC_MSG_FAILURE([unable to find Java JNI! ensure CFLAGS are set appropriately or configure --without-java])],
     [with_java="no"])
])
AM_CONDITIONAL([HAVE_JAVA], [test "x$with_java" = "xyes"])


dnl header files

dnl FIXME switches for shm, mmap
AC_HEADER_ASSERT
AC_CHECK_HEADERS([errno.h fcntl.h features.h inttypes.h float.h limits.h \
  locale.h stddef.h stdlib.h string.h unistd.h sys/types.h sys/stat.h \
  sys/ioctl.h sys/time.h sys/times.h sys/ipc.h sys/shm.h sys/mman.h])
AC_HEADER_MAJOR
AC_CHECK_HEADER_STDBOOL

dnl types

AC_TYPE_INT32_T
AC_TYPE_UINT32_T
AC_TYPE_UINT8_T
AC_TYPE_UINTPTR_T
AC_TYPE_UID_T
AC_TYPE_INT32_T
AC_TYPE_INT64_T
AC_TYPE_OFF_T
AC_TYPE_SIZE_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT64_T
AC_TYPE_UINT8_T
AC_CHECK_MEMBERS([struct stat.st_rdev])

dnl compile characteristics

AC_C_CONST
AC_C_INLINE

dnl functions

AC_FUNC_MMAP
AC_CHECK_FUNCS([alarm clock_gettime floor getcwd gettimeofday localeconv memchr memmove memset modf munmap pow select setenv sqrt strcasecmp strchr strdup strerror strrchr strstr strtol strtoul malloc realloc])


dnl output generation

dnl avoid doc rebuilds unless revision info changes
AC_CONFIG_COMMANDS([doc/version.xml],
  [AS_IF([test -f doc/version.xml && \
          ! echo $VERSION | diff doc/version.xml - >/dev/null 2>&1 || \
          ! echo $VERSION | diff $srcdir/doc/version.xml - >/dev/null 2>&1 ],
    [echo "writing new doc/version.xml" ; echo $VERSION > $srcdir/doc/version.xml ])],
  [VERSION="$VERSION"]
)
AC_CONFIG_COMMANDS([doc/reldate.xml],
  [AS_IF([test -f doc/reldate.xml && \
          ! echo $RELDATE | diff doc/reldate.xml - >/dev/null 2>&1 || \
          ! echo $RELDATE | diff $srcdir/doc/reldate.xml - >/dev/null 2>&1 ],
    [echo "writing new doc/reldate.xml" ; echo $RELDATE > $srcdir/doc/reldate.xml ])],
  [RELDATE="$RELDATE"]
)

echo "Generating config files"

AC_CONFIG_FILES([
Makefile
gtk/Makefile
java/Makefile
po/Makefile.in
zbar/Makefile
zbar.pc
zbar-gtk.pc
doc/doxygen.conf])

AC_CONFIG_FILES([test/test_examples.sh],[chmod 755 test/test_examples.sh])
AC_CONFIG_FILES([test/check_dbus.sh],[chmod 755 test/check_dbus.sh])

AC_OUTPUT

dnl summary log

echo ""
echo "please verify that the detected configuration matches your expectations:"
echo "------------------------------------------------------------------------"
AS_IF([test "x$USE_NLS" = "xyes"],
  [echo    "gettext                $USE_NLS"]
)
AS_IF([test "x$win32" != "xno"],
  [AS_IF([test "x$with_directshow" != "xno"],
    [echo "DirectShow driver      --with-directshow=$with_directshow"],
    [echo "VfW driver             --with-directshow=$with_directshow"]
  )],
  [echo "X                      --with-x=$have_x"]
)

AS_IF([test "x$with_python" != "xno"], pyver="\tpython${PYTHON_VERSION}")
AS_IF([test "x$with_gtk" != "xno"], gtkver="\tGtk${GTK_VERSION}")
AS_IF([test "x$with_qt" != "xno"], qtver="\tQt${QT_VERSION}")

echo    "pthreads               --enable-pthread=$enable_pthread"
echo    "doc                    --enable-doc=$enable_doc"
echo    "v4l                    --enable-video=$enable_video"
echo    "jpeg                   --with-jpeg=$with_jpeg"
echo -e "Python                 --with-python=$with_python $pyver"
echo -e "GTK                    --with-gtk=$with_gtk       $gtkver"
echo    "GObject introspection  --with-gir=$with_gir"
echo -e "Qt                     --with-qt=$with_qt         $qtver"
echo    "Java                   --with-java=$with_java"

AS_IF([test "x$win32" = "xno"],
  [echo "Dbus                   --with-dbus=$with_dbus"])
AS_IF([test "x$have_GM" = "xyes"],
  [echo "GraphicsMagick         --with-graphicsmagick=yes"],
  [echo "ImageMagick            --with-imagemagick=$with_imagemagick"])
echo "Enabled codes:        $enabled_codes"
echo "Disabled codes:       $disabled_codes"
AS_IF([test "x$with_java" = "xyes"],
  [echo "JAVA_HOME              $JAVA_HOME"])

dnl Display "warnings" about disabled and experimental features

echo ""
AS_IF([test "x$enable_video" != "xyes"],
  [echo "        => zbarcam video scanner will *NOT* be built"])
AS_IF([test "x$have_libv4l" != "xyes"],
  [echo "        => libv4l will *NOT* be used"])
AS_IF([test "x$with_jpeg" != "xyes"],
  [echo "        => JPEG image conversions will *NOT* be supported"])
AS_IF([test "x$have_IM" != "xyes" && test "x$have_GM" != "xyes"],
  [echo "        => the zbarimg file scanner will *NOT* be built"])
AS_IF([test "x$have_GM" = "xyes"],
  [echo "        => ImageMagick is preferred, as GraphicsMagick doesn't support https"])
AS_IF([test "x$with_gtk" = "xno"],
  [echo "        => GTK support will *NOT* be built"])
AS_IF([test "x$with_pygtk2" != "xyes" && test "xPYTHON_VERSION_MAJOR" = "x2"],
  [echo "        => the Python 2 GTK widget wrapper will *NOT* be built"])
AS_IF([test "x$with_qt" != "xyes"],
  [echo "        => the Qt widget will *NOT* be built"])
AS_IF([test "x$with_qt" = "xyes" && test "x$enable_static_qt" = "xyes" ],
  [echo "        => Building a static Qt library"])
AS_IF([test "x$with_java" != "xyes"],
  [echo "        => the Java interface will *NOT* be built"])
AS_IF([test "x$with_java_unit" != "xyes"],
  [echo "        => the Java unit test will *NOT* be enabled"])
dnl echo "NPAPI Plugin     --with-npapi=$with_npapi"
dnl AS_IF([test "x$with_mozilla" != "xyes"],
dnl  [echo "       => the Mozilla/Firefox/OpenOffice plugin will *NOT* be built"])
AS_IF([test "x$enable_pdf417" = "xyes"],
  [echo "        => the pdf417 code support is incomplete!"])
