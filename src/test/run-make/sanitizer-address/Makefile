-include ../tools.mk

LOG := $(TMPDIR)/log.txt

# NOTE the address sanitizer only supports x86_64 linux and macOS

ifeq ($(TARGET),x86_64-apple-darwin)
ASAN_SUPPORT=$(SANITIZER_SUPPORT)
EXTRA_RUSTFLAG=-C rpath
else
ifeq ($(TARGET),x86_64-unknown-linux-gnu)
ASAN_SUPPORT=$(SANITIZER_SUPPORT)

# Apparently there are very specific Linux kernels, notably the one that's
# currently on Travis CI, which contain a buggy commit that triggers failures in
# the ASan implementation, detailed at google/sanitizers#837. As noted in
# google/sanitizers#856 the "fix" is to avoid using PIE binaries, so we pass a
# different relocation model to avoid generating a PIE binary. Once Travis is no
# longer running kernel 4.4.0-93 we can remove this and pass an empty set of
# flags again.
EXTRA_RUSTFLAG=-C relocation-model=dynamic-no-pic
endif
endif

all:
ifeq ($(ASAN_SUPPORT),1)
	$(RUSTC) -g -Z sanitizer=address -Z print-link-args $(EXTRA_RUSTFLAG) overflow.rs | grep -q librustc_asan
	$(TMPDIR)/overflow 2>&1 | tee $(LOG)
	grep -q stack-buffer-overflow $(LOG)
endif
