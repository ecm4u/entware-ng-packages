#
# Copyright (C) 2007-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=pciutils
PKG_VERSION:=3.5.2
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@KERNEL/software/utils/pciutils
PKG_HASH:=3a99141a9f40528d0a0035665a06dc37ddb1ae341658e51b50a76ecf86235efc
PKG_MAINTAINER:=Álvaro Fernández Rojas <noltari@gmail.com>

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_USE_MIPS16:=0
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/pciutils
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Linux PCI Utilities
  URL:=http://mj.ucw.cz/pciutils.shtml
  DEPENDS:=+libkmod +zlib
endef

define Package/pciutils/description
 contains collection of programs for inspecting and manipulating configuration
 of PCI devices
endef

PCI_IDS_REV:=91cfa8a0c994634ba9a4a8639aa2ac6dff8453b9
PCI_IDS_FILE:=pci.ids.$(PCI_IDS_REV)
define Download/pci_ids
  FILE:=$(PCI_IDS_FILE)
  URL_FILE:=pci.ids
  URL:=@GITHUB/pciutils/pciids/$(PCI_IDS_REV)
  HASH:=798528092d1c58eeac99c6505033ec4ce8fe3e19d7e0c41b06790d58753a89b6
endef
$(eval $(call Download,pci_ids))

define Build/Prepare
	$(call Build/Prepare/Default)
	$(RM) $(PKG_BUILD_DIR)/pci.ids
	$(CP) $(DL_DIR)/$(PCI_IDS_FILE) $(PKG_BUILD_DIR)/pci.ids
endef

MAKE_FLAGS += \
	CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS)" \
	PREFIX="/opt" \
	HOST="Linux" \
	HWDB="no" \
	ZLIB="yes"

ifneq ($(CONFIG_USE_GLIBC),)
TARGET_LDFLAGS += -lresolv
endif

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_BUILD_DIR)/lib/libpci.a $(1)/opt/lib
	$(INSTALL_DIR) $(1)/opt/include/pci
	$(CP) $(foreach i,pci.h config.h header.h types.h, \
		$(PKG_BUILD_DIR)/lib/$(i)) $(1)/opt/include/pci
endef

define Package/pciutils/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/{lspci,setpci,update-pciids} $(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/share
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/opt/share/pci.ids.gz $(1)/opt/share/
endef

define Package/pciutils/postinst
#!/bin/sh
 cd /opt/share; /opt/sbin/update-pciids; rm pci.ids.gz.old
 exit 0
endef

$(eval $(call BuildPackage,pciutils))
