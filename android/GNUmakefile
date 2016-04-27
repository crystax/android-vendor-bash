override MYDIR := $(abspath $(dir $(lastword $(MAKEFILE_LIST))))
override TOPDIR := $(abspath $(MYDIR)/..)

sinclude $(TOPDIR)/local.mk

ABI ?= armeabi-v7a-hard

#==================================================================================================

define rm-if-exists
$(if $(wildcard $(1)),rm -Rf $(wildcard $(1)))
endef

define out-dir
$(TOPDIR)/android/out/$(1)
endef

define build-dir
$(call out-dir,$(1))/build
endef

define install-dir
$(call out-dir,$(1))/install
endef

#==================================================================================================

.PHONY: all
all: bash

.PHONY: clean
clean:
	$(call rm-if-exists,$(call out-dir))

.PHONY: bash
bash:
	$(if $(strip $(NDK)),,$(error NDK was not defined!))
	$(if $(strip $(ABI)),,$(error ABI was not defined!))
	@$(strip \
		export NDK=$(NDK) && \
		export ABI=$(ABI) && \
		export OUTDIR=$(call out-dir,$(ABI)) && \
		source $(TOPDIR)/android/envsetup.sh && \
		mkdir -p $(call build-dir,$(ABI)) && \
		cd $(call build-dir,$(ABI)) && \
		$(TOPDIR)/configure \
			--prefix=$(call install-dir,$(ABI)) \
			--host=$$TARGET \
			--disable-nls \
			--disable-readline \
			--enable-alias \
			--enable-arith-for-command \
			--enable-array-variables \
			--enable-brace-expansion \
			--enable-direxpand-default \
			--enable-directory-stack \
			--without-bash-malloc \
			--without-libintl-prefix \
			--without-libiconv-prefix \
			&& \
		make -j && \
		make install && \
		true \
	)

.PHONY: test
test: $(call build-dir,$(ABI))/bash
	@$(strip \
		export NDK=$(NDK) && \
		export ABI=$(ABI) && \
		export OUTDIR=$(call out-dir,$(ABI)) && \
		source $(TOPDIR)/android/envsetup.sh && \
		cd $(call build-dir,$(ABI)) && \
		make test \
	)
