#
# configure.in - input script to autoconf to
#                configure directory
#
# This file is part of the GNU PIC Library.
#
# Originally written by
# 	Vangelis Rokas <vrokas@otenet.gr>
#
# Adopted for the SDCC/pic14 library by
#	Raphael Neider <rneider AT web.de>
#
# Added libc and DEBUG/NOOPTS/EXPERIMENTAL conditionals by
#       Gonzalo Pérez de Olaguer Córdoba <salo@gpoc.es>
#
# $Id: configure.ac 14090 2023-05-29 13:06:08Z spth $
#

# Process this file with autoconf to produce a configure script.
AC_PREREQ([2.71])
AC_INIT([pic14lib],[0.1],[tecodev AT users sourceforge net])
AM_INIT_AUTOMAKE([foreign subdir-objects no-dependencies])
AC_CONFIG_SRCDIR([libsdcc/idata.c])
AC_CONFIG_HEADERS([config.h])

AM_MAINTAINER_MODE

# SDCC setup
case $srcdir in
    [\\/]*|?:[\\/]*) abs_srcdir="$srcdir";
        ;;
    *) abs_srcdir="$ac_pwd/$srcdir";
        ;;
esac
case $ac_top_build_prefix in
    [\\/]*|?:[\\/]*)
        abs_top_builddir="$ac_top_build_prefix";
        ;;
    *) abs_top_builddir="$ac_pwd/$ac_top_build_prefix";
        ;;
esac

libdir=$libdir/pic14

# Checks for programs.
# The default architectures (regular and enhanced cores) can be selected at
# configure time by setting the environment variables ARCH and/or EARCH to
# the desired device (16fxxx).
AC_SUBST(ARCH,  [${ARCH:-16f877}])
# Previous default setting for EARCH was 16f1934, but gpsim doesn't support it.
# It should be used the device with larger ROM and RAM sizes.
AC_SUBST(EARCH, [${EARCH:-16f1788}])

# We cannot use AC_PROG_CC(sdcc) as sdcc might not be built at configure-time...
AC_SUBST(CC, [\'$abs_top_builddir/../../../bin/sdcc\'])
AC_PATH_PROG(CCAS, gpasm, :)
AC_PATH_PROG(LD, gplink, :)
AC_PATH_PROG(AR, gplib, :)

case ":$CCAS:$LD:$AR:" in
    *:::*)
        AC_MSG_ERROR([gputils (gpasm, gplink, and gplib) are required but not found.
Either install gputils or reconfigure with --disable-pic14-port and --disable-pic16-port.])
        ;;
esac;

mCCAS="$CCAS"
mLD="$LD"
AC_SUBST(CFLAGS, [""])
AC_SUBST(CCASFLAGS, [""])
AC_SUBST(LDFLAGS, ["-m -l -w"])
AC_SUBST(ARFLAGS, ["-c"])
AC_SUBST(CCAS, [\'$CCAS\'])
AC_SUBST(LD, [\'$LD\'])
AC_SUBST(AR, [\'$AR\'])

# $RANLIB is called by the autotools but not provided nor required
AC_SUBST(RANLIB, [:])
AC_SUBST(OBJEXT, [o])

_AM_DEPENDENCIES(CC)
_AM_DEPENDENCIES(CCAS)

AC_MSG_CHECKING([whether gputils support enhanced cores])
# Check support for enhanced pic cores.
ENABLE_ENHANCED_PICS="no"
CHECK=".checkdevices/check"
mkdir -p .checkdevices
printf '  include "p%s.inc"\n  END\n' "${EARCH}" > "${CHECK}.asm"
if "$mCCAS" -p${EARCH} -o "${CHECK}.o" -c "${CHECK}.asm" >/dev/null 2>&1 && "$mLD" "${CHECK}.o" >/dev/null 2>&1; then
  ENABLE_ENHANCED_PICS="yes"
fi;
AC_MSG_RESULT([$ENABLE_ENHANCED_PICS])
AM_CONDITIONAL([ENABLE_ENHANCED_PICS], [ test x$ENABLE_ENHANCED_PICS = xyes ])

AC_MSG_CHECKING([wheter gputils support the extended instruction set])
ENABLE_EXTENDED_INSTRUCTIONS="no"
printf '  include "p%s.inc"\n  CODE\n  ADDFSR FSR0, 4\n  MOVIW FSR0++\n  END\n' "${EARCH}" > "${CHECK}.asm"
if "$mCCAS" -p${EARCH} -o "${CHECK}.o" -c "${CHECK}.asm" >/dev/null 2>&1 && "$mLD" "${CHECK}.o" >/dev/null 2>&1; then
  ENABLE_EXTENDED_INSTRUCTIONS="yes"
fi;
AC_MSG_RESULT([$ENABLE_EXTENDED_INSTRUCTIONS])
AM_CONDITIONAL([ENABLE_EXTENDED_INSTRUCTIONS], [ test x$ENABLE_EXTENDED_INSTRUCTIONS = xyes ])

# default values for the following conditions
: ${DEBUG:=0}
: ${NOOPTS:=0}
: ${EXPERIMENTAL:=0}

# generate debug info during the build process
AM_CONDITIONAL([ENABLE_DEBUG], [ test x$DEBUG = x1 ])

# build libraries with optimizations disabled
AM_CONDITIONAL([ENABLE_NOOPTS], [ test x$NOOPTS = x1 ])

# build libraries with experimental code enabled
AM_CONDITIONAL([ENABLE_EXPERIMENTAL], [ test x$EXPERIMENTAL = x1 ])

# Checks for libraries.

# Checks for header files.

# Checks for typedefs, structures, and compiler characteristics.

# Checks for library functions.

AC_CONFIG_FILES([Makefile
                 libsdcc/enhanced/Makefile
                 libsdcc/enhanced-no-xinst/Makefile
                 libsdcc/regular/Makefile
                 libc/Makefile
                 libm/Makefile])
AC_OUTPUT
