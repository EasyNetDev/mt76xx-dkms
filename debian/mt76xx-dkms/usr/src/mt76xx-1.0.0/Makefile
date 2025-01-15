#
# Makefile for Linux MT76XX.
#

export DRIVER_NAME := mt76
export DRIVER_INFO := drivers/net/wireless/$(DRIVER_NAME)

$(info Makefile: Include Config.mk)
include $(PWD)/Config.mk

ifneq ($(KERNELRELEASE),)
# call from kernel build system

# Autodetection if we have a driver for the specific MAJOR.MINOR version of kernel
export KVER_MAJ_MIN := $(shell echo ${KERNELRELEASE} | sed "s/\([0-9]\+\.[0-9]\+\)\..*/\1/g")

$(info Makefile: Called from Kernel Build)
#$(info Makefile: CONFIG_WLAN_VENDOR_MEDIATEK=$(CONFIG_WLAN_VENDOR_MEDIATEK))
#$(info Makefile: CONFIG_MT76x0E=$(CONFIG_MT76x0E))

obj-y := $(DRIVER_NAME)/

else
# external module build

$(info Called as external module Build)
#$(info Makefile: CONFIG_WLAN_VENDOR_MEDIATEK=$(CONFIG_WLAN_VENDOR_MEDIATEK))
#$(info Makefile: CONFIG_MT76x0E=$(CONFIG_MT76x0E))

EXTRA_FLAGS += -I$(PWD)

#
# KDIR is a path to a directory containing kernel source.
# It can be specified on the command line passed to make to enable the module to
# be built and installed for a kernel other than the one currently running.
# By default it is the path to the symbolic link created when
# the current kernel's modules were installed, but
# any valid path to the directory in which the target kernel's source is located
# can be provided on the command line.
#
# In case we want to compile the module against different kernel version than current one
KVER	?= $(shell uname -r)

# Autodetection if we have a driver for the specific MAJOR.MINOR version of kernel
export KVER_MAJ_MIN := $(shell echo ${KVER} | sed "s/\([0-9]\+\.[0-9]\+\)\..*/\1/g")

KDIR	?= /lib/modules/$(KVER)/build
MDIR	?= /lib/modules/$(KVER)
PWD	:= $(shell pwd)
obj-y	:= mt76/

%.ko:
	$(MAKE) -C $(KDIR) M=$(PWD) $(MODULE) $(MODULES_COMMON) $(MODULES_USB) $(MODULES_PCI) $(MODULES_SDIO) $(MODULES_WMAC) $(MODULE_NO_BUILD)

PHONY += all
all: ${DRIVER_NAME}.ko

PHONY += modules
modules: ${DRIVER_NAME}.ko

PHONY += clean
clean:
	$(MAKE) -C $(KDIR) M=$(PWD) clean

dkms-config:
	echo Add these parameters in MAKE in dkms.conf.in: $(MODULES_COMMON) $(MODULES_USB) $(MODULES_PCI) $(MODULES_SDIO) $(MODULES_WMAC) $(MODULE_NO_BUILD)

PHONY += help
help:
	$(MAKE) -C $(KDIR) M=$(PWD) help

PHONY += install
install:
	for i in $(MODULES_INSTALL); do \
	    install -m644 -b -D mt76/$(KVER_MAJ_MIN)/mediatek/mt76/$$i ${MDIR}/updates/dkms/net/wireless/mt76/$$i; \
	done
	depmod -a

PHONY += uninstall
uninstall:
	for i in $(MODULES_INSTALL); do \
	    rm -f ${MDIR}/updates/dkms/net/wireless/mt76/$$i; \
	done
	rm -rf ${MDIR}/updates/dkms/net/wireless/mt76/
	depmod -a

endif

.PHONY : $(PHONY)
