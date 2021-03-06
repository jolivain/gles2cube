AC_PREREQ([2.68])
AC_INIT([gles2cube], [1.0], [juju@cotds.org])
AM_INIT_AUTOMAKE([-Wall -Wno-portability subdir-objects foreign])

AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_MKDIR_P

AC_CONFIG_HEADERS([config.h])
AC_CONFIG_FILES([
  Makefile
  src/Makefile
])

AC_CHECK_HEADERS([stddef.h stdlib.h string.h])
AC_TYPE_SIZE_T
AC_CHECK_FUNCS([clock_gettime malloc memset strerror strtol uname])

AC_ARG_ENABLE([debug],
        AS_HELP_STRING([--enable-debug], [enable debug messages @<:@default=disabled@:>@]),
        [], [enable_debug=no])
AS_IF([test "x$enable_debug" = "xyes"], [
        AC_DEFINE(ENABLE_DEBUG, [1], [Debug messages.])
])

PKG_CHECK_MODULES([egl], [egl >= 1])
PKG_CHECK_MODULES([glesv2], [glesv2 >= 1])

AC_CHECK_HEADERS([EGL/eglext.h], [], [],
[[#include <EGL/egl.h>
]])

AC_ARG_WITH([native-gfx],
        AS_HELP_STRING([--with-native-gfx=GFX],[Define the native gfx backend: x11(default),vivfb,rpi,wl,sdl2,kms,tisgx,mali,wgl]),
        [], [with_native_gfx=x11])

AS_IF([test "x$with_native_gfx" = "xx11"], [
	native_gfx=x11
        PKG_CHECK_MODULES([x11], [x11 >= 1.4])
        AC_DEFINE([ENABLE_X11], [1], [Enable X11.])
        AC_MSG_NOTICE([X11 support enabled])
], [
        AC_MSG_NOTICE([X11 support disabled])
])
AM_CONDITIONAL([ENABLE_X11], [test "x$with_native_gfx" = "xx11"])

AS_IF([test "x$with_native_gfx" = "xvivfb"], [
	native_gfx=vivfb
        AC_DEFINE([ENABLE_VIVFB], [1], [Enable (i.MX6 / Vivante) fb.])
        AC_DEFINE([EGL_API_FB], [1], [Enable Vivante EGL FB])
        AC_DEFINE([LINUX], [1], [Vivante EGL FB needs LINUX to be defined])
        AC_MSG_NOTICE([VivanteFB support enabled])
], [
        AC_MSG_NOTICE([VivanteFB support disabled])
])
AM_CONDITIONAL([ENABLE_VIVFB], [test "x$with_native_gfx" = "xvivfb"])

AS_IF([test "x$with_native_gfx" = "xrpi"], [
	native_gfx=rpi
        AC_DEFINE([ENABLE_RPI], [1], [Enable Raspberry Pi])
        AC_MSG_NOTICE([Raspberry Pi support enabled])
], [
        AC_MSG_NOTICE([Raspberry Pi support disabled])
])
AM_CONDITIONAL([ENABLE_RPI], [test "x$with_native_gfx" = "xrpi"])

AS_IF([test "x$with_native_gfx" = "xwl"], [
    native_gfx=wl
    PKG_CHECK_MODULES([wayland_client], [wayland-client >= 1], [
        have_wayland_client=yes
    ])
    PKG_CHECK_MODULES([wayland_egl], [wayland-egl >= 1], [
        have_wayland_egl=yes
    ])
    AC_DEFINE([ENABLE_WL], [1], [Enable Wayland EGL])
    AC_MSG_NOTICE([Wayland EGL support enabled])
], [
    AC_MSG_NOTICE([Wayland EGL support disabled])
])
AM_CONDITIONAL([ENABLE_WL], [test "x$with_native_gfx" = "xwl"])

AC_ARG_ENABLE([ivi],
    AS_HELP_STRING([--enable-ivi], [enable IVI support for Wayland]),
    [], [enable_ivi=no])
AS_IF([test "x$enable_ivi" = "xyes"], [
    AC_DEFINE(ENABLE_IVI, [1], [Enable Wayland IVI.])
])
AM_CONDITIONAL([ENABLE_IVI], [test "x$enable_ivi" = "xyes"])

AS_IF([test "x$with_native_gfx" = "xkms"], [
    native_gfx=kms
    AC_DEFINE([ENABLE_KMS], [1], [Enable KMS/GBM/DRM])
    AC_MSG_NOTICE([KMS/GBM/DRM support enabled])
    PKG_CHECK_MODULES([drm], [libdrm])
    PKG_CHECK_MODULES([gbm], [gbm])
], [
    AC_MSG_NOTICE([KMS/GBM/DRM support disabled])
])
AM_CONDITIONAL([ENABLE_KMS], [test "x$with_native_gfx" = "xkms"])

AS_IF([test "x$with_native_gfx" = "xsdl2"], [
    native_gfx=sdl2
    PKG_CHECK_MODULES([sdl2], [sdl2 >= 2], [
        have_sdl2=yes
    ])
    AC_DEFINE([ENABLE_SDL2], [1], [Enable SDL2])
    AC_MSG_NOTICE([SDL2 EGL support enabled])
], [
    AC_MSG_NOTICE([SDL2 EGL support disabled])
])
AM_CONDITIONAL([ENABLE_SDL2], [test "x$with_native_gfx" = "xsdl2"])

AS_IF([test "x$with_native_gfx" = "xwgl"], [
    native_gfx=wgl
    AC_DEFINE([ENABLE_WGL], [1], [Enable Native Windows WGL])
    AC_MSG_NOTICE([Windows WGL support enabled])
], [
    AC_MSG_NOTICE([Windows WGL support disabled])
])
AM_CONDITIONAL([ENABLE_WGL], [test "x$with_native_gfx" = "xwgl"])

AS_IF([test "x$with_native_gfx" = "xtisgx"], [
    native_gfx=tisgx
    AC_DEFINE([ENABLE_TISGX], [1], [Enable Native TI SGX])
    AC_MSG_NOTICE([TI SGX support enabled])
], [
    AC_MSG_NOTICE([TI SGX support disabled])
])
AM_CONDITIONAL([ENABLE_TISGX], [test "x$with_native_gfx" = "xtisgx"])

AS_IF([test "x$with_native_gfx" = "xmali"], [
    native_gfx=mali
    AC_DEFINE([ENABLE_MALI], [1], [Enable Native Mali])
    AC_MSG_NOTICE([Arm Mali support enabled])
], [
    AC_MSG_NOTICE([Arm Mali support disabled])
])
AM_CONDITIONAL([ENABLE_MALI], [test "x$with_native_gfx" = "xmali"])

AS_IF([test "x$native_gfx" = "x"], [
    AC_ERROR([unknown native gfx selected: $with_native_gfx])
], [])

AC_OUTPUT

AC_MSG_RESULT([
---------------------
Configuration summary
---------------------
                debug : ${enable_debug}
           native-gfx : ${with_native_gfx}
])
