# SPDX-License-Identifier: GPL-2.0-only

# Unlike the kernel space, exported headers are written in standard C.
#  - Forbid C++ style comments
#  - Use '__inline__', '__asm__' instead of 'inline', 'asm'
#
# -std=c90 (equivalent to -ansi) catches the violation of those.
# We cannot go as far as adding -Wpedantic since it emits too many warnings.
UAPI_CFLAGS := -std=c90 -Wall -Werror=implicit-function-declaration

# In theory, we do not care -m32 or -m64 for header compile tests.
# It is here just because CONFIG_CC_CAN_LINK is tested with -m32 or -m64.
UAPI_CFLAGS += $(filter -m32 -m64, $(KBUILD_CPPFLAGS) $(KBUILD_CFLAGS))

# USERCFLAGS might contain sysroot location for CC.
UAPI_CFLAGS += $(USERCFLAGS)

override c_flags = $(UAPI_CFLAGS) -Wp,-MMD,$(depfile) -I$(objtree)/usr/include

# The following are excluded for now because they fail to build.
#
# Do not add a new header to the blacklist without legitimate reason.
# Please consider to fix the header first.
#
# Sorted alphabetically.
no-header-test += asm/shmbuf.h
no-header-test += asm/signal.h
no-header-test += asm/ucontext.h
no-header-test += drm/vmwgfx_drm.h
no-header-test += linux/am437x-vpfe.h
no-header-test += linux/android/binder.h
no-header-test += linux/android/binderfs.h
no-header-test += linux/coda.h
no-header-test += linux/errqueue.h
no-header-test += linux/fsmap.h
no-header-test += linux/hdlc/ioctl.h
no-header-test += linux/ivtv.h
no-header-test += linux/kexec.h
no-header-test += linux/matroxfb.h
no-header-test += linux/omap3isp.h
no-header-test += linux/omapfb.h
no-header-test += linux/patchkey.h
no-header-test += linux/phonet.h
no-header-test += linux/reiserfs_xattr.h
no-header-test += linux/sctp.h
no-header-test += linux/signal.h
no-header-test += linux/sysctl.h
no-header-test += linux/usb/audio.h
no-header-test += linux/v4l2-mediabus.h
no-header-test += linux/v4l2-subdev.h
no-header-test += linux/videodev2.h
no-header-test += linux/vm_sockets.h
no-header-test += sound/asequencer.h
no-header-test += sound/asoc.h
no-header-test += sound/asound.h
no-header-test += sound/compress_offload.h
no-header-test += sound/emu10k1.h
no-header-test += sound/sfnt_info.h
no-header-test += xen/evtchn.h
no-header-test += xen/gntdev.h
no-header-test += xen/privcmd.h

# More headers are broken in some architectures

ifeq ($(SRCARCH),arc)
no-header-test += linux/bpf_perf_event.h
endif

ifeq ($(SRCARCH),ia64)
no-header-test += asm/setup.h
no-header-test += asm/sigcontext.h
no-header-test += asm/perfmon.h
no-header-test += asm/perfmon_default_smpl.h
no-header-test += linux/if_bonding.h
endif

ifeq ($(SRCARCH),mips)
no-header-test += asm/stat.h
endif

ifeq ($(SRCARCH),powerpc)
no-header-test += asm/stat.h
no-header-test += linux/bpf_perf_event.h
endif

ifeq ($(SRCARCH),riscv)
no-header-test += linux/bpf_perf_event.h
endif

ifeq ($(SRCARCH),sparc)
no-header-test += asm/stat.h
no-header-test += asm/uctx.h
no-header-test += asm/fbio.h
endif

# asm-generic/*.h is used by asm/*.h, and should not be included directly
no-header-test += asm-generic/%

extra-y := $(patsubst $(obj)/%.h,%.hdrtest, $(shell find $(obj) -name '*.h' 2>/dev/null))

# Include the header twice to detect missing include guard.
quiet_cmd_hdrtest = HDRTEST $<
      cmd_hdrtest = \
		$(CC) $(c_flags) -S -o /dev/null -x c /dev/null \
			$(if $(filter-out $(no-header-test), $*.h), -include $< -include $<); \
		$(PERL) $(srctree)/scripts/headers_check.pl $(obj) $(SRCARCH) $<; \
		touch $@

$(obj)/%.hdrtest: $(obj)/%.h FORCE
	$(call if_changed_dep,hdrtest)

clean-files += $(filter-out Makefile, $(notdir $(wildcard $(obj)/*)))
