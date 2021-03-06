# Copyright 2014 Google Inc. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

define([thisversion],[1.7.0])
define([nextversion],[1.7.0])
AC_INIT([xsecurelock],thisversion,[rpolzer@google.com])
AM_INIT_AUTOMAKE([-Wall subdir-objects foreign])
AC_PROG_CC
AC_PROG_CC_C99
AC_PROG_SED
AC_PROG_AWK

AC_DEFUN([RP_TRY_CC_FLAG], [
	AC_MSG_CHECKING([whether the compiler supports $1])
	saved_CFLAGS=$CFLAGS
	CFLAGS="$CFLAGS $1"
	AC_COMPILE_IFELSE([AC_LANG_PROGRAM([])],
	                  [AC_MSG_RESULT([yes])],
	                  [AC_MSG_RESULT([no])
	                   CFLAGS_$saved_CFLAGS])])
RP_TRY_CC_FLAG([-Wall])
RP_TRY_CC_FLAG([-Wextra])
AC_DEFINE([_POSIX_C_SOURCE], [200112L])
AC_DEFINE([_XOPEN_SOURCE], [600])

dnl We only detect libraries globally, but not all binaries need all of them.
dnl So we use --as-needed to work around.
AC_MSG_CHECKING([whether the linker supports --as-needed])
saved_LDFLAGS=$LDFLAGS
LDFLAGS="$LDFLAGS -Wl,--as-needed"
AC_LINK_IFELSE([AC_LANG_PROGRAM([])],
               [AC_MSG_RESULT([yes])],
               [AC_MSG_RESULT([no])
                LDFLAGS=$saved_LDFLAGS])

AC_CHECK_HEADER(X11/Xlib.h,
                [], [AC_ERROR([X includes not found.])])
AC_CHECK_LIB(X11, XOpenDisplay,
             [], [AC_ERROR([X library not found.])])
AC_CHECK_LIB(Xmuu, XmuClientWindow,
             [], [AC_CHECK_LIB(Xmu, XmuClientWindow,
             [], [AC_ERROR([Xmuu or Xmu library not found.])])])
AC_CHECK_LIB(m, sqrt,
             [], [AC_ERROR([Math library not found.])])

# RP_SEARCH_LIBS(sym, lib, macro, flag, default, description)
AC_DEFUN([RP_SEARCH_LIBS], [
          AC_ARG_WITH([$4],
                      [AS_HELP_STRING([--with-$4],
                                      [$6 @<:@default=$5@:>@])],
                                      [with_$4=$withval],
                                      [with_$4=$5])
          AS_IF([test "x$with_$4" = xno],
                [have_$4=false],
                [AC_SEARCH_LIBS([$1], [$2],
                                [have_$4=true],
                                [AS_IF([test "x$with_$4" = xcheck],
                                       [have_$4=false],
                                       [AC_MSG_ERROR([--with-$4 was enabled, but test for $4 failed])])])])
          AM_CONDITIONAL([$3], [test x$have_$4 = xtrue])])

# RP_SEARCH_PROG(prog, path, macro, flag, default, description)
AC_DEFUN([RP_SEARCH_PROG], [
          AC_ARG_WITH([$4],
                      [AS_HELP_STRING([--with-$4],
                                      [$6 @<:@default=$5@:>@])],
                                      [with_$4=$withval],
                                      [with_$4=$5])
          AS_IF([test "x$with_$4" = xno],
                [have_$4=false],
                [path_to_$4=$with_$4
                 AC_PATH_PROG([path_to_$4], [$1], [], [$2])
                 AS_IF([test "x$path_to_$4" = x],
                       [AS_IF([test "x$with_$4" = xcheck],
                              [have_$4=false],
                              [AC_MSG_ERROR([--with-$4 was enabled, but test for $4 failed])])],
                       [have_$4=true])])
          AM_CONDITIONAL([$3], [test x$have_$4 = xtrue])])

# RP_CHECK_MODULE(prefix, module, macro, flag, default, description)
AC_DEFUN([RP_CHECK_MODULE], [
          AC_ARG_WITH([$4],
                      [AS_HELP_STRING([--with-$4],
                                      [$6 @<:@default=$5@:>@])],
                                      [with_$4=$withval],
                                      [with_$4=$5])
          AS_IF([test "x$with_$4" = xno],
                [have_$4=false],
                [PKG_CHECK_MODULES($1, [$2],
                                   [have_$4=true],
                                [AS_IF([test "x$with_$4" = xcheck],
                                       [have_$4=false],
                                       [AC_MSG_ERROR([--with-$4 was enabled, but test for $4 failed])])])])
          AM_CONDITIONAL([$3], [test x$have_$4 = xtrue])])

# The DPMS extension is used to save power of the screen when turned off. Saves
# electric power.
RP_SEARCH_LIBS(DPMSQueryExtension, Xext,
               [HAVE_DPMS_EXT], [dpms], [check],
               [Use the DPMS extension to save power])

# The X11 Screen Saver extension is used to turn off the screen saver when X11
# handles screen blanking (e.g. via timeout) anyway; not needed for when we
# handle blanking explicitly (XSECURELOCK_BLANK_TIMEOUT). Saves CPU power.
RP_SEARCH_LIBS(XScreenSaverQueryExtension, Xss,
               [HAVE_XSCREENSAVER_EXT], [xss], [check],
               [Use the X11 Screen Saver extension to save power])

# The X Synchronization extension is used to get per-device idle times. Used by
# until_nonidle only.
RP_SEARCH_LIBS(XSyncQueryExtension, Xext,
               [HAVE_XSYNC_EXT], [xsync], [check],
               [Use the X Synchronization extension to detect idle time])

# The Composite extension needs an explicit opt-out because not having it can
# cause desktop notifications to appear on top of the screen saver (privacy
# risk).
RP_SEARCH_LIBS(XCompositeQueryExtension, Xcomposite,
               [HAVE_XCOMPOSITE_EXT], [xcomposite], [yes],
               [Use the X11 Composite extension to cover desktop notifications])

# This extension doesn't really exist anymore, but served to counteract the
# (also no longer existing) AllowClosedownGrabs and similar X server settings.
RP_SEARCH_LIBS(XF86MiscSetGrabKeysState, Xxf86misc,
               [HAVE_XF86MISC_EXT], [xf86misc], [check],
               [Use the XFree86-Misc extension to disable ungrab keys])

# PAM-less operation is... unfortunate. Let's have users explicitly disable this
# if they need to use other auth modules, as most likely they should rather
# install PAM development libraries instead.
RP_SEARCH_LIBS(pam_authenticate, pam,
               [HAVE_PAM], [pam], [yes],
               [Enable support for the PAM library to authenticate the user])

RP_CHECK_MODULE(libbsd, [libbsd],
                [HAVE_LIBBSD], [libbsd], [check],
                [Use libbsd for utility functions.])
AC_CHECK_FUNCS([explicit_bzero])

# Xft optionally provides nicer font rendering.
RP_CHECK_MODULE(FONTCONFIG, [fontconfig],
                [HAVE_FONTCONFIG], [fontconfig], [check],
                [Use fontconfig])
AS_IF([test x$have_fontconfig = xtrue],
      [check_if_fontconfig_else_no=check],
      [check_if_fontconfig_else_no=no])
RP_CHECK_MODULE(XFT, [xft],
                [HAVE_XFT_EXT], [xft], [$check_if_fontconfig_else_no],
                [Use the Xft extension for nicer fonts (requires Xrender and fontconfig too)])

# Querying the keyboard LEDs (and layout name) makes auth_x11 more user
# friendly.
RP_SEARCH_LIBS(XkbGetIndicatorState, X11,
               [HAVE_XKB_EXT], [xkb], [check],
               [Use the X Keyboard extension to show the keyboard LEDs])

# XRandR provides information about monitor layouts and is strongly recommended
# on systems which can use more than one monitor (which includes most laptops).
RP_SEARCH_LIBS(XRRGetScreenResources, Xrandr,
               [HAVE_XRANDR_EXT], [xrandr], [check],
               [Use the XRandR extension to query monitor layouts])

# The XFixes extension is used to work around possible weird leftover state from
# compositors.
RP_SEARCH_LIBS(XFixesSetWindowShapeRegion, Xfixes,
               [HAVE_XFIXES_EXT], [xfixes], [check],
               [Use the XFixes extension to work around some compositors])

RP_SEARCH_PROG(htpasswd, [$PATH],
               [HAVE_HTPASSWD], [htpasswd], [check],
               [Install auth_htpasswd (specify --with-htpasswd=/usr/bin/htpasswd to set the path to use)])

RP_SEARCH_PROG(mplayer, [$PATH],
               [HAVE_MPLAYER], [mplayer], [check],
               [Install saver_mplayer (specify --with-mplayer=/usr/bin/mplayer to set the path to use)])

RP_SEARCH_PROG(mpv, [$PATH],
               [HAVE_MPV], [mpv], [check],
               [Install saver_mpv (specify --with-mpv=/usr/bin/mpv to set the path to use)])

RP_SEARCH_PROG(pamtester, [$PATH],
               [HAVE_PAMTESTER], [pamtester], [check],
               [Install authproto_pamtester (specify --with-pamtester=/usr/bin to set the path to use)])

RP_SEARCH_PROG(pandoc, [$PATH],
               [HAVE_PANDOC], [pandoc], [check],
               [Use pandoc to generate man pages])

RP_SEARCH_PROG(slidescreen, /usr/lib/xscreensaver,
               [HAVE_XSCREENSAVER], [xscreensaver], [check],
               [Install saver_xscreensaver (specify --with-xscreensaver=/usr/lib/xscreensaver to set the saver plugin directory to use)])
# However, all we really want is the directory name...
path_to_xscreensaver=${path_to_xscreensaver%/slidescreen}

pam_service_name=
enable_pam_check_account_type=no

AS_IF([test x$have_pam = xtrue], [
  AC_ARG_WITH([pam_service_name],
              AC_HELP_STRING([--with-pam-service-name],
                             [The name of the PAM service that xsecurelock's
                              auth helpers will authenticate as by default.
                              This flag is mandatory. Be sure to verify that
                              /etc/pam.d/SERVICENAME actually exists, as failure
                              of doing so may prevent unlocking the screen.]),
              [pam_service_name=$withval],
              [AC_MSG_ERROR([--with-pam-service-name is mandatory.])])
  AS_IF([test -f "/etc/pam.d/$pam_service_name"], [],
        [AC_MSG_WARN([/etc/pam.d/$pam_service_name doesn't exist. Sure?])])

  AC_ARG_ENABLE([pam_check_account_type],
                AC_HELP_STRING([--enable-pam-check-account-type],
                               [Enable checking of PAM accounting result. Only
                                enable this if accounting is properly configured
                                on your system; especially keep this disabled if
                                --with-pam-service-name is set to an
                                authentication-only service name like
                                common-auth.]),
                [enable_pam_check_account_type=$enableval],
                [enable_pam_check_account_type=no])
])

AC_ARG_WITH([default_auth_module],
            AC_HELP_STRING([--with-default-auth-module],
                           [The default authentication module to use when none
                            was specified. Can be overridden via the
                            XSECURELOCK_AUTH environment variable or a command
                            line argument.]),
                            [auth_executable=$withval],
                            [auth_executable=auth_x11])

AC_MSG_NOTICE([Configured default auth module: $auth_executable])

AC_ARG_WITH([default_authproto_module],
            AC_HELP_STRING([--with-default-authproto-module],
                           [The default authentication protocol module to use when none was
                            specified. Can be overridden via the
                            XSECURELOCK_AUTHPROTO environment variable. By
                            default this is autodetected to be one of the
                            supported authentication modules.]),
                            [authproto_executable=$withval],
                            [
                             authproto_executable=
                             AS_IF([test x$have_htpasswd = xtrue],
                                   [authproto_executable=authproto_htpasswd])
                             AS_IF([test x$have_pamtester = xtrue],
                                   [authproto_executable=authproto_pamtester])
                             AS_IF([test x$have_pam = xtrue],
                                   [authproto_executable=authproto_pam])
                             AS_IF([test -z "$authproto_executable"],
                                   [AC_MSG_ERROR([No authproto executable found.])])
                            ])

AC_MSG_NOTICE([Configured default authproto module: $authproto_executable])

AC_ARG_WITH([default_global_saver_module],
            AC_HELP_STRING([--with-default-global-saver-module],
                           [The default global saver module to use when none was
                            specified. Can be overridden via the
                            XSECURELOCK_GLOBAL_SAVER environment variable.]),
                            [global_saver_executable=$withval],
                            [global_saver_executable=saver_multiplex])

AC_MSG_NOTICE([Configured default global saver module: ]dnl
[$global_saver_executable])

AC_ARG_WITH([default_saver_module],
            AC_HELP_STRING([--with-default-saver-module],
                           [The default per-screen saver module to use when none
                            was specified. Can be overridden via the
                            XSECURELOCK_SAVER environment variable.]),
                            [saver_executable=$withval],
                            [saver_executable=saver_blank])

AC_MSG_NOTICE([Configured default saver module: $saver_executable])

AC_ARG_ENABLE([any_user_auth],
            AC_HELP_STRING([--enable-any-user-auth],
                              [Enalbe authentication option to use any
                              valid account on the system for screensaver
                              authentication.]),
            [enable_any_user_auth=$enableval],
            [enable_any_user_auth=no])

AC_ARG_ENABLE([no_blank],
            AC_HELP_STRING([--enable-no-blank],
                              [Enable non blanking screensaver option
                              to enable a transparent screen lock.]),
            [enable_no_blank=$enableval],
            [enable_no_blank=no])

AC_ARG_ENABLE([banner],
            AC_HELP_STRING([--enable-banner],
                              [Enalbe logon banner information
                              defined in banner.h]),
            [enable_banner=$enableval],
            [enable_banner=no])

AC_ARG_ENABLE([wallpaper],
            AC_HELP_STRING([--enable-wallpaper],
                              [Enalbe wallpaper from wallpaper.xbm]),
            [enable_wallpaper=$enableval],
            [enable_wallpaper=no])

AC_ARG_ENABLE([syslog],
            AC_HELP_STRING([--enable-syslog],
                           [Enable syslog integration for logging authentication]),
            [enable_syslog=$enableval],
            [enable_syslog=no])

AC_ARG_ENABLE([secure],
            AC_HELP_STRING([--enable-secure],
                           [Enable secure mode where no config files are settable
                            using environmental variables]),
            [enable_secure=$enableval],
            [enable_secure=no])

AC_SUBST(auth)
AC_SUBST(no_blank)
AC_SUBST(banner)
AC_SUBST(wallpaper)
AC_SUBST(syslog)
AC_SUBST(banner_filename)
AC_SUBST(banner_width)
AC_SUBST(userfile_block)
AC_SUBST(userfile_priv)
AC_SUBST(userfile_any)
AC_SUBST(userfile_noblank)

AC_SUBST(pam_service_name)
AC_SUBST(auth_executable)
AC_SUBST(authproto_executable)
AC_SUBST(global_saver_executable)
AC_SUBST(saver_executable)
AC_SUBST(path_to_htpasswd)
AC_SUBST(path_to_mplayer)
AC_SUBST(path_to_mpv)
AC_SUBST(path_to_pamtester)
AC_SUBST(path_to_xscreensaver)
AM_CONDITIONAL([PAM_CHECK_ACCOUNT_TYPE],
               [test x$enable_pam_check_account_type = xyes])
AM_CONDITIONAL([HAVE_IDLE_TIMER],
               [test x$have_xss = xtrue -o x$have_xsync = xtrue])
AM_CONDITIONAL([ANY_USER_AUTH],
               [test x$enable_any_user_auth = xyes])
AM_CONDITIONAL([NO_BLANK],
               [test x$enable_no_blank = xyes])
AM_CONDITIONAL([BANNER],
               [test x$enable_banner = xyes])
AM_CONDITIONAL([WALLPAPER],
               [test x$enable_wallpaper = xyes])
AM_CONDITIONAL([SYSLOG],
               [test x$enable_syslog = xyes])
AM_CONDITIONAL([SECURE],
               [test x$enable_secure = xyes])

banner_filename=
banner_width=
AS_IF([test x$enable_banner = xyes], [
  AC_ARG_WITH([banner_filename],
              AC_HELP_STRING([--with-banner-filename],
                             [The path for the banner filename to
                              display on the unlock screen above the login or
                              password prompt. This flag is mandatory when the
                              logon banner is enabled.]),
              [banner_filename=$withval],
              [AC_MSG_ERROR([--with-banner-filename is mandatory.])])
  AC_ARG_WITH([banner_width],
              AC_HELP_STRING([--with-banner-width],
                             [The banner width in characters to justify to.]),
              [banner_width=$withval],
              [banner_width=60])
  AC_CHECK_PROGS([PAR], [par], [])
  AS_IF([test -z "$PAR"],
        [AC_MSG_ERROR([par needed to format banner])])
])

userfile_priv=
userfile_block=
userfile_any=
userfile_noblank=
AS_IF([test x$enable_any_user_auth = xyes], [
  AC_ARG_WITH([userfile_priv],
              AC_HELP_STRING([--with-userfile-priv],
                             [The path for the userfile filename to
                              configure privalidged users
                              This flag is mandatory when the any user auth
                              is enabled.]),
              [userfile_priv=$withval],
              [AC_MSG_ERROR([--with-userfile-priv is mandatory.])])
  AC_ARG_WITH([userfile_block],
              AC_HELP_STRING([--with-userfile-block],
                             [The path for the userfile filename to
                              configure blocked users
                              This flag is mandatory when the any user auth
                              is enabled.]),
              [userfile_block=$withval],
              [AC_MSG_ERROR([--with-userfile-priv is mandatory.])])
  AC_ARG_WITH([userfile_any],
              AC_HELP_STRING([--with-userfile-any],
                             [The path for the userfile filename to
                              configure users which will allow for any
                              valid user to unlock.
                              This flag is mandatory when the any user auth
                              is enabled.]),
              [userfile_any=$withval],
              [AC_MSG_ERROR([--with-userfile-any is mandatory.])])
])

AC_ARG_WITH([userfile_noblank],
            AC_HELP_STRING([--with-userfile-noblank],
                        [The path for the userfile filename to
                        configure users which will not have the screen
                        blanked when the screensaver is activated
                        This flag is mandatory.]),
            [userfile_noblank=$withval],
            [AC_MSG_ERROR([--with-userfile-noblank is mandatory.])])

AC_CONFIG_FILES([Makefile])
AC_CONFIG_FILES([helpers/authproto_htpasswd],
                [chmod +x helpers/authproto_htpasswd])
AC_CONFIG_FILES([helpers/authproto_pamtester],
                [chmod +x helpers/authproto_pamtester])
AC_CONFIG_FILES([helpers/saver_mplayer],
                [chmod +x helpers/saver_mplayer])
AC_CONFIG_FILES([helpers/saver_mpv],
                [chmod +x helpers/saver_mpv])
AC_CONFIG_FILES([helpers/saver_xscreensaver],
                [chmod +x helpers/saver_xscreensaver])

# Generate documentation.
AC_CHECK_PROGS([DOXYGEN], [doxygen], [])
AS_IF([test -z "$DOXYGEN"],
      [AC_MSG_WARN([Doxygen not found. Who needs documentation anyway.])])
AM_CONDITIONAL([HAVE_DOXYGEN], [test -n "$DOXYGEN"])
AC_CONFIG_FILES([Doxyfile])

# End of documentation.

AC_OUTPUT
