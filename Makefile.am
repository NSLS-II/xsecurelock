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

CLEANFILES =

macros = \
	-DHELPER_PATH=\"$(pkglibexecdir)\" \
	-DDOCS_PATH=\"$(docdir)\" \
	-DAUTH_EXECUTABLE=\"@auth_executable@\" \
	-DAUTHPROTO_EXECUTABLE=\"@authproto_executable@\" \
	-DGLOBAL_SAVER_EXECUTABLE=\"@global_saver_executable@\" \
	-DSAVER_EXECUTABLE=\"@saver_executable@\" \
	-DPAM_SERVICE_NAME=\"@pam_service_name@\" \
	-DBANNER_FILENAME=\"@banner_filename@\" \
	-DUSERFILE_BLOCK=\"@userfile_block@\" \
	-DUSERFILE_PRIV=\"@userfile_priv@\" \
	-DUSERFILE_ANY=\"@userfile_any@\" \
	-DUSERFILE_NOBLANK=\"@userfile_noblank@\"

if HAVE_LIBBSD
macros += -DHAVE_LIBBSD
endif
if PAM_CHECK_ACCOUNT_TYPE
macros += -DPAM_CHECK_ACCOUNT_TYPE
endif
if HAVE_DPMS_EXT
macros += -DHAVE_DPMS_EXT
endif
if HAVE_FONTCONFIG
macros += -DHAVE_FONTCONFIG
endif
if HAVE_XSCREENSAVER_EXT
macros += -DHAVE_XSCREENSAVER_EXT
endif
if HAVE_XSYNC_EXT
macros += -DHAVE_XSYNC_EXT
endif
if HAVE_XCOMPOSITE_EXT
macros += -DHAVE_XCOMPOSITE_EXT
endif
if HAVE_XF86MISC_EXT
macros += -DHAVE_XF86MISC_EXT
endif
if HAVE_XFIXES_EXT
macros += -DHAVE_XFIXES_EXT
endif
if HAVE_XFT_EXT
macros += -DHAVE_XFT_EXT
endif
if HAVE_XRANDR_EXT
macros += -DHAVE_XRANDR_EXT
endif
if HAVE_XKB_EXT
macros += -DHAVE_XKB_EXT
endif
if ANY_USER_AUTH
macros += -DANY_USER_AUTH
endif
if NO_BLANK
macros += -DNO_BLANK
endif
if BANNER
macros += -DBANNER
endif
if WALLPAPER
macros += -DWALLPAPER
endif
if SYSLOG
macros += -DSYSLOG
endif
if SECURE
macros += -DSECURE
endif

bin_PROGRAMS = \
	xsecurelock
xsecurelock_SOURCES = \
	auth_child.c auth_child.h \
	env_settings.c env_settings.h \
	env_info.c env_info.h \
	logging.c logging.h \
	mlock_page.h \
	main.c \
	saver_child.c saver_child.h \
	unmap_all.c unmap_all.h \
	util.c util.h \
	version.c version.h \
	wait_pgrp.c wait_pgrp.h \
	wm_properties.c wm_properties.h \
	xscreensaver_api.c xscreensaver_api.h \
	incompatible_compositor.xbm \
	userfile.c userfile.h
nodist_xsecurelock_SOURCES = \
	env_helpstr.inc
xsecurelock_CPPFLAGS = $(macros) $(LIBBSD_CFLAGS)
xsecurelock_LDADD = $(LIBBSD_LIBS)

helpersdir = $(pkglibexecdir)
helpers_SCRIPTS = \
	helpers/saver_blank
if HAVE_HTPASSWD
helpers_SCRIPTS += \
	helpers/authproto_htpasswd
endif
if HAVE_MPLAYER
helpers_SCRIPTS += \
	helpers/saver_mplayer
endif
if HAVE_MPV
helpers_SCRIPTS += \
	helpers/saver_mpv
endif
if HAVE_PAMTESTER
helpers_SCRIPTS += \
	helpers/authproto_pamtester
endif
if HAVE_XSCREENSAVER
helpers_SCRIPTS += \
	helpers/saver_xscreensaver
endif

helpers_PROGRAMS = \
	pgrp_placeholder
pgrp_placeholder_SOURCES = \
	helpers/pgrp_placeholder.c

helpers_PROGRAMS += \
	saver_multiplex
saver_multiplex_SOURCES = \
	env_settings.c env_settings.h \
	helpers/monitors.c helpers/monitors.h \
	helpers/saver_multiplex.c \
	logging.c logging.h \
	saver_child.c saver_child.h \
	wait_pgrp.c wait_pgrp.h \
	wm_properties.c wm_properties.h \
	xscreensaver_api.c xscreensaver_api.h
saver_multiplex_CPPFLAGS = $(macros)

helpers_PROGRAMS += \
	dimmer
dimmer_SOURCES = \
	env_settings.c env_settings.h \
	helpers/dimmer.c \
	logging.c logging.h \
	wm_properties.c wm_properties.h
dimmer_CPPFLAGS = $(macros)

if HAVE_IDLE_TIMER
helpers_PROGRAMS += \
	until_nonidle
until_nonidle_SOURCES = \
	env_settings.c env_settings.h \
	helpers/until_nonidle.c \
	logging.c logging.h \
	wait_pgrp.c wait_pgrp.h
until_nonidle_CPPFLAGS = $(macros)
endif

helpers_PROGRAMS += \
	auth_x11
auth_x11_SOURCES = \
	env_info.c env_info.h \
	env_settings.c env_settings.h \
	helpers/authproto.c helpers/authproto.h \
	helpers/auth_x11.c \
	helpers/monitors.c helpers/monitors.h \
	logging.c logging.h \
	mlock_page.h \
	util.c util.h \
	wait_pgrp.c wait_pgrp.h \
	wm_properties.c wm_properties.h \
	xscreensaver_api.c xscreensaver_api.h

auth_x11_CPPFLAGS = $(macros) $(FONTCONFIG_CFLAGS) $(XFT_CFLAGS) $(LIBBSD_CFLAGS)
auth_x11_LDADD = $(FONTCONFIG_LIBS) $(XFT_LIBS) $(LIBBSD_LIBS)

if BANNER
auth_x11_SOURCES += \
	banner.c banner.h
endif

if HAVE_PAM
helpers_PROGRAMS += \
	authproto_pam
authproto_pam_SOURCES = \
	env_info.c env_info.h \
	env_settings.c env_settings.h \
	helpers/authproto.c helpers/authproto.h \
	helpers/authproto_pam.c \
	logging.c logging.h \
	mlock_page.h \
	util.c util.h
if ANY_USER_AUTH
authproto_pam_SOURCES += \
	userfile.c userfile.h
endif
authproto_pam_CPPFLAGS = $(macros) $(LIBBSD_CFLAGS)
authproto_pam_LDADD = $(LIBBSD_LIBS)
endif

doc_DATA = \
	CONTRIBUTING \
	LICENSE \
	README.md \
	README-NSLS2.md

if HAVE_PANDOC
man1_MANS = xsecurelock.1
xsecurelock.1.md: doc/xsecurelock.1.md README.md
	{ \
		grep -B 9999 'ENV VARIABLES HERE' doc/xsecurelock.1.md; \
		grep -A 9999 'ENV VARIABLES START' README.md |\
			grep -B 9999 'ENV VARIABLES END'; \
		grep -A 9999 'ENV VARIABLES HERE' doc/xsecurelock.1.md; \
	} > xsecurelock.1.md
xsecurelock.1: xsecurelock.1.md
	$(path_to_pandoc) -s -f markdown -t man -o $@ $<
CLEANFILES += xsecurelock.1.md xsecurelock.1
endif

# Some tools that we sure don't wan to install
noinst_PROGRAMS = cat_authproto nvidia_break_compositor get_compositor remap_all
cat_authproto_SOURCES = \
	logging.c logging.h \
	helpers/authproto.c helpers/authproto.h \
	test/cat_authproto.c \
	util.c util.h
nvidia_break_compositor_SOURCES = \
	test/nvidia_break_compositor.c
nvidia_break_compositor_CPPFLAGS = $(macros)
get_compositor_SOURCES = \
	test/get_compositor.c
get_compositor_CPPFLAGS = $(macros)
remap_all_SOURCES= \
	test/remap_all.c \
	unmap_all.c unmap_all.h
remap_all_CPPFLAGS = $(macros)

FORCE:
version.c: FORCE
	if [ -n "$(GIT_VERSION)" ]; then \
		echo "const char *const git_version = \"$(GIT_VERSION)\";" \
			> version.c; \
	elif git describe --always --dirty >/dev/null 2>&1; then \
		echo "const char *const git_version = \"` \
			git describe --always --dirty \
		`\";" > version.c; \
	else \
		: version.c must exist in non-git builds.; \
		cat version.c; \
	fi
.PRECIOUS: version.c  # Old one is better than none.

env_helpstr.inc: README.md # Autogenerate for --help.
	grep -A 9999 'ENV VARIABLES START' "$<" |\
		grep -B 9999 'ENV VARIABLES END' |\
		grep '^[ *]' |\
		sed -e 's,\\\|",\\&,g; s,$$,\\n",; s,^,",;' > "$@"
xsecurelock-main.$(OBJEXT): env_helpstr.inc

EXTRA_DIST = \
	CONTRIBUTING \
	LICENSE \
	README.md \
	README-NSLS2.md \
	autogen.sh \
	doc/xsecurelock.1.md \
	ensure-documented-settings.sh \
	helpers/saver_blank \
	incompatible_compositor.xbm.sh \
	run-iwyu.sh \
	run-linters.sh \
	test/*.c \
	test/*.sh \
	test/*.xdo \
	version.c

examplesdir = $(docdir)/examples
dist_examples_DATA = \
	doc/examples/saver_livestreams

if HAVE_DOXYGEN
doxyfile.stamp:
	$(DOXYGEN) Doxyfile
	echo Timestamp > doxyfile.stamp

CLEANFILES += doxyfile.stamp

all-local: doxyfile.stamp

clean-local:
	$(RM) -r $(top_srcdir)/doxy
endif

scripts/run-xsecurelock: scripts/run-xsecurelock.in
	$(SED) \
		-e 's|[@]libexecdir@|$(libexecdir)|g' \
		-e 's|[@]bindir@|$(bindir)|g' \
		-e 's|[@]sysconfdir@|$(sysconfdir)|g' \
		< "$<" > "$@"

scripts/saver-xsecurelock: scripts/saver-xsecurelock.in
	$(SED) \
		-e 's|[@]datarootdir@|$(datarootdir)|g' \
		< "$<" > "$@"

autostart/xsecurelock.desktop: autostart/xsecurelock.desktop.in
	$(SED) \
		-e 's|[@]libexecdir@|$(libexecdir)|g' \
		-e 's|[@]bindir@|$(bindir)|g' \
		-e 's|[@]sysconfdir@|$(sysconfdir)|g' \
		< "$<" > "$@"

if BANNER
config/banner: config/banner.in
	$(PAR) j1w@banner_width@ < "$<" | \
			$(AWK) -v w=@banner_width@ '{if (NR>1) {printf "%-*s\n", w, $$0} else {printf "%s\n", $$0}}'  >\
			"$@"
endif

autolockdir = $(pkglibexecdir)
autolock_SCRIPTS = \
	scripts/saver-xsecurelock

runscriptdir = $(bindir)
runscript_SCRIPTS = \
	scripts/run-xsecurelock 

autostartdir = $(sysconfdir)/xdg/autostart
autostart_DATA = \
	autostart/xsecurelock.desktop

CLEANFILES += scripts/run-xsecurelock \
			  scripts/saver-xsecurelock

xsecurelocksysconfdir=$(sysconfdir)/xsecurelock
xsecurelocksysconf_DATA = config/config

if BANNER
xsecurelocksysconf_DATA += config/banner
CLEANFILES += config/banner
endif

if ANY_USER_AUTH
xsecurelocksysconf_DATA += \
	config/userfile-priv  \
	config/userfile-block \
	config/userfile-any
endif

xsecurelocksysconf_DATA += \
	config/userfile-noblank

if HAVE_PAM
xsecurelockpamdir=$(sysconfdir)/pam.d
xsecurelockpam_DATA = config/xsecurelock-nsls2
endif

iconsdir = $(datadir)/xsecurelock
icons_DATA = icons/lock.png
