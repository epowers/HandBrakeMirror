dnl Process this file with autoconf to produce a configure script.
dnl Created by Anjuta application wizard.

AC_INIT(ghb, 0.1)

AM_INIT_AUTOMAKE([1.7.9 foreign dist-bzip2 dist-zip])
AM_CONFIG_HEADER(config.h)
AM_MAINTAINER_MODE

AC_ISC_POSIX
AC_PROG_CC
AC_PROG_CXX
AM_PROG_CC_STDC
AC_HEADER_STDC

if test x"$CC_FOR_BUILD" = x; then
	if test x"$cross_compiling" = x"yes"; then
		AC_CHECK_PROGS(CC_FOR_BUILD, gcc, cc)
	else
		CC_FOR_BUILD="$CC"
	fi
fi

AC_SUBST(CC_FOR_BUILD)

# introduce the optional configure parameter for the path of libXXX.a
AC_ARG_WITH(hb,
	AC_HELP_STRING(
	    [--with-hb=prefix],
	    [try this for the hb-library prefix install directory]
	  ),
	  hb_PATHSET=1,
	  hb_PATHSET=0
)

if test  $hb_PATHSET = 1 ; then
    case ${with_hb} in
    /*)
		HBINC="-I$with_hb/libhb -I$with_hb/contrib/include"
		LDFLAGS="$LDFLAGS -L$with_hb/libhb -L$with_hb/contrib/lib"
		AC_SUBST(HB_DIR, "$with_hb")
        ;;
    *)
		HBINC='-I$(top_srcdir)/'"$with_hb/libhb "'-I$(top_srcdir)/'"$with_hb/contrib/include"
		LDFLAGS="$LDFLAGS "'-L$(top_srcdir)/'"$with_hb/libhb "'-L$(top_srcdir)/'"$with_hb/contrib/lib"
		AC_SUBST(HB_DIR, '$(top_srcdir)/'"$with_hb")
        ;;
    esac
else
	HBINC='-I$(top_srcdir)/'"../libhb "'-I$(top_srcdir)/'"../contrib/include"
	LDFLAGS="$LDFLAGS "'-L$(top_srcdir)/'"../libhb "'-L$(top_srcdir)/'"../contrib/lib"
	AC_SUBST(HB_DIR, '$(top_srcdir)/'"..")
fi

AC_ARG_ENABLE(dl,
	AS_HELP_STRING([--enable-dl], [enable libdl]),
	use_libdl=yes, use_libdl=no)

AC_ARG_ENABLE(gst,
	AS_HELP_STRING([--disable-gst], [disable gstreamer (live preview)]),
	gst_disable=yes, gst_disable=no)

AC_ARG_ENABLE(update-checks,
	AS_HELP_STRING([--disable-update-checks], [disable update checks]),
	update_checks=no, update_checks=yes)

# overwrite global variable (used for Makefile generation)
AC_SUBST(GLOBALCXXFLAGS, $CXXFLAGS )
AC_SUBST(GLOBALLDFLAGS, $LDFLAGS )



dnl ***************************************************************************
dnl Internatinalization
dnl ***************************************************************************
GETTEXT_PACKAGE=ghb
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE", [GETTEXT package name])
AM_GLIB_GNU_GETTEXT
IT_PROG_INTLTOOL([0.35.0])

AM_PROG_LIBTOOL

case $host in
  *-*-mingw*)
    if test "x$gst_disable" = "xyes" ; then
        GHB_PACKAGES="gtk+-2.0 >= 2.10 gthread-2.0 gio-2.0"
	else
		GHB_PACKAGES="gtk+-2.0 >= 2.10 gthread-2.0 gstreamer-0.10 gstreamer-interfaces-0.10 gstreamer-video-0.10 gstreamer-pbutils-0.10 gio-2.0"
	fi
	mingw_flag=yes
    ;;
  *)
    case "$host_os" in
	linux*)
	  pkg_gudev="gudev-1.0"
	  ;;
    esac
    if test "x$gst_disable" = "xyes" ; then
	    GHB_PACKAGES="gtk+-2.0 >= 2.10 gthread-2.0 gio-2.0 libnotify dbus-glib-1 $pkg_gudev"
    else
	    GHB_PACKAGES="gtk+-2.0 >= 2.10 gthread-2.0 gstreamer-0.10 gstreamer-interfaces-0.10 gstreamer-video-0.10 gstreamer-pbutils-0.10 gio-2.0 libnotify dbus-glib-1 $pkg_gudev"
    fi
	mingw_flag=no
    ;;
esac

if test "x$gst_disable" = "xno" ; then
	CXXFLAGS="$CXXFLAGS -D_ENABLE_GST"
	CFLAGS="$CFLAGS -D_ENABLE_GST"
fi

PKG_PROG_PKG_CONFIG
if test "x$update_checks" = "xyes" ; then
	PKG_CHECK_MODULES(OldWebKitGtk, WebKitGtk, old_webkit=yes, old_webkit=no)
	if test "x$old_webkit" = "xyes" ; then
		if test "x$mingw_flag" = "xno" ; then
			GHB_PACKAGES="$GHB_PACKAGES WebKitGtk"
			CXXFLAGS="$CXXFLAGS -D_OLD_WEBKIT"
			CFLAGS="$CFLAGS -D_OLD_WEBKIT"
		fi
	else
		if test "x$mingw_flag" = "xno" ; then
			GHB_PACKAGES="$GHB_PACKAGES webkit-1.0"
		fi
	fi
else
	CFLAGS="$CFLAGS -D_NO_UPDATE_CHECK"
fi

AM_CONDITIONAL([MINGW], [test "x$mingw_flag" = "xyes"])

PKG_CHECK_MODULES(libappindicator, appindicator-0.1, have_appind=yes, have_appind=no)
if test "x$have_appind" = "xyes" ; then
    GHB_PACKAGES="$GHB_PACKAGES appindicator-0.1"
    CXXFLAGS="$CXXFLAGS -D_USE_APP_IND"
    CFLAGS="$CFLAGS -D_USE_APP_IND"
fi
PKG_CHECK_MODULES(GHB, [$GHB_PACKAGES])

GHB_CFLAGS="$HBINC $GHB_CFLAGS"

AC_PATH_PROG(BUILD_PKG_CONFIG, pkg-config, no)
if test x"$BUILD_PKG_CONFIG" = x"no"; then
	AC_MSG_ERROR([You need to install pkg-config])
fi

GHB_TOOLS_CFLAGS=`$BUILD_PKG_CONFIG --cflags glib-2.0 gdk-pixbuf-2.0`
GHB_TOOLS_LIBS=`$BUILD_PKG_CONFIG --libs glib-2.0 gdk-pixbuf-2.0`

case $host in
  *-*-mingw*)
    if test "x$use_libdl" = "xyes" ; then
		GHB_LIBS="$GHB_LIBS -ldl"
	fi
    ;;
esac

AC_SUBST(GHB_TOOLS_CFLAGS)
AC_SUBST(GHB_TOOLS_LIBS)
AC_SUBST(GHB_CFLAGS)
AC_SUBST(GHB_LIBS)

AC_OUTPUT([
Makefile
src/Makefile
po/Makefile.in
])
