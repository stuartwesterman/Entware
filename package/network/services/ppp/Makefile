#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=ppp
PKG_VERSION:=2.4.7
PKG_RELEASE:=12

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://download.samba.org/pub/ppp/
PKG_HASH:=02e0a3dd3e4799e33103f70ec7df75348c8540966ee7c948e4ed8a42bbccfb30
PKG_MAINTAINER:=Felix Fietkau <nbd@nbd.name>
PKG_LICENSE:=BSD-4-Clause
PKG_CPE_ID:=cpe:/a:samba:ppp

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

PKG_BUILD_DEPENDS:=libpcap

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/ppp/Default
  SECTION:=net
  CATEGORY:=Network
  URL:=https://ppp.samba.org/
endef

define Package/ppp
$(call Package/ppp/Default)
  TITLE:=PPP daemon
  VARIANT:=default
endef

define Package/ppp-multilink
$(call Package/ppp/Default)
  TITLE:=PPP daemon (with multilink support)
  VARIANT:=multilink
endef

define Package/ppp/description
This package contains the PPP (Point-to-Point Protocol) daemon.
endef

define Package/ppp/conffiles
/opt/etc/ppp/chap-secrets
/opt/etc/ppp/filter
/opt/etc/ppp/ip-down
/opt/etc/ppp/ip-up
/opt/etc/ppp/ipv6-down
/opt/etc/ppp/ipv6-up
/opt/etc/ppp/options
endef

define Package/ppp-mod-pppoa
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=PPPoA plugin
endef

define Package/ppp-mod-pppoa/description
This package contains a PPPoA (PPP over ATM) plugin for ppp.
endef

define Package/ppp-mod-pppoe
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=PPPoE plugin
endef

define Package/ppp-mod-pppoe/description
This package contains a PPPoE (PPP over Ethernet) plugin for ppp.
endef

define Package/ppp-mod-radius
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=RADIUS plugin
endef

define Package/ppp-mod-radius/description
This package contains a RADIUS (Remote Authentication Dial-In User Service)
plugin for ppp.
endef

define Package/ppp-mod-radius/conffiles
/opt/etc/ppp/radius.conf
/opt/etc/ppp/radius/
endef

define Package/ppp-mod-pppol2tp
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=PPPoL2TP plugin
endef

define Package/ppp-mod-pppol2tp/description
This package contains a PPPoL2TP (PPP over L2TP) plugin for ppp.
endef

define Package/ppp-mod-pptp
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink) +resolveip
  TITLE:=PPtP plugin
endef

define Package/ppp-mod-pptp/description
This package contains a PPtP plugin for ppp.
endef

define Package/ppp-mod-passwordfd
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=pap/chap secret from filedescriptor
endef

define Package/ppp-mod-passwordfd/description
This package allows to pass the PAP/CHAP secret from a filedescriptor.
Eliminates the need for a secrets file.
endef

define Package/chat
$(call Package/ppp/Default)
  TITLE:=Establish conversation with a modem
endef

define Package/chat/description
This package contains an utility to establish conversation with other PPP servers
(via a modem).
endef

define Package/pppdump
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=Read PPP record file
endef

define Package/pppdump/description
This package contains an utility to read PPP record file.
endef

define Package/pppstats
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink)
  TITLE:=Report PPP statistics
endef

define Package/pppstats/description
This package contains an utility to report PPP statistics.
endef

define Package/pppoe-discovery
$(call Package/ppp/Default)
  DEPENDS:=@(PACKAGE_ppp||PACKAGE_ppp-multilink) +ppp-mod-pppoe
  TITLE:=Perform a PPPoE-discovery process
endef

define Package/pppoe-discovery/description
This tool performs the same discovery process as pppoe, but does
not initiate a session. Can be useful to debug pppoe.
endef



# for 2.6 kernels ???? no, 3.4 also
LINUX_UAPI_DIR=""


define Build/Configure
$(call Build/Configure/Default,, \
	UNAME_S="Linux" \
	UNAME_R="$(LINUX_VERSION)" \
	UNAME_M="$(ARCH)" \
)
	mkdir -p $(PKG_BUILD_DIR)/pppd/plugins/pppoatm/linux
	$(CP) \
		$(LINUX_DIR)/include/linux/compiler.h \
		$(LINUX_DIR)/include/$(LINUX_UAPI_DIR)linux/atm*.h \
		$(PKG_BUILD_DIR)/pppd/plugins/pppoatm/linux/

	# Kernel 4.14.9+ only, ignore the exit status of cp in case the file
	# doesn't exits
	-$(CP) $(LINUX_DIR)/include/linux/compiler_types.h \
		$(PKG_BUILD_DIR)/pppd/plugins/pppoatm/linux/
endef

TARGET_CFLAGS += -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

MAKE_FLAGS += COPTS="$(TARGET_CFLAGS) -I$(STAGING_DIR)/opt/include" \
		PRECOMPILED_FILTER=1 \
		STAGING_DIR="$(STAGING_DIR)"

ifeq ($(BUILD_VARIANT),multilink)
  MAKE_FLAGS += HAVE_MULTILINK=y
else
  MAKE_FLAGS += HAVE_MULTILINK=
endif

ifdef CONFIG_USE_MUSL
  MAKE_FLAGS += USE_LIBUTIL=
endif

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/include/pppd $(1)/opt/include/
endef

define Package/ppp/script_install
endef

define Package/ppp/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/pppd $(1)/opt/sbin/
endef
Package/ppp-multilink/install=$(Package/ppp/install)

define Package/ppp-mod-pppoa/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/lib/pppd/$(PKG_VERSION)/pppoatm.so \
		$(1)/opt/lib/pppd/$(PKG_VERSION)/
endef

define Package/ppp-mod-pppoe/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/lib/pppd/$(PKG_VERSION)/rp-pppoe.so \
		$(1)/opt/lib/pppd/$(PKG_VERSION)/
endef

define Package/ppp-mod-radius/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/lib/pppd/$(PKG_VERSION)/radius.so \
		$(1)/opt/lib/pppd/$(PKG_VERSION)/
	$(INSTALL_DIR) $(1)/opt/etc/ppp
	$(INSTALL_DATA) ./files/etc/ppp/radius.conf $(1)/opt/etc/ppp/
	$(INSTALL_DIR) $(1)/opt/etc/ppp/radius
	$(INSTALL_DATA) ./files/etc/ppp/radius/dictionary* \
		$(1)/opt/etc/ppp/radius/
	$(INSTALL_CONF) ./files/etc/ppp/radius/servers \
		$(1)/opt/etc/ppp/radius/
endef

define Package/ppp-mod-pppol2tp/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/lib/pppd/$(PKG_VERSION)/pppol2tp.so \
		$(1)/opt/lib/pppd/$(PKG_VERSION)/
endef

define Package/ppp-mod-pptp/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/lib/pppd/$(PKG_VERSION)/pptp.so \
		$(1)/opt/lib/pppd/$(PKG_VERSION)/
	$(INSTALL_DIR) $(1)/opt/etc/ppp
	$(INSTALL_DATA) ./files/etc/ppp/options.pptp $(1)/opt/etc/ppp/
endef

define Package/ppp-mod-passwordfd/install
	$(INSTALL_DIR) $(1)/opt/lib/pppd/$(PKG_VERSION)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/lib/pppd/$(PKG_VERSION)/passwordfd.so \
		$(1)/opt/lib/pppd/$(PKG_VERSION)/
endef

define Package/chat/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/chat $(1)/opt/sbin/
endef

define Package/pppdump/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/pppdump $(1)/opt/sbin/
endef

define Package/pppstats/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/pppstats $(1)/opt/sbin/
endef

define Package/pppoe-discovery/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/pppoe-discovery $(1)/opt/sbin/
endef

$(eval $(call BuildPackage,ppp))
$(eval $(call BuildPackage,ppp-multilink))
$(eval $(call BuildPackage,ppp-mod-pppoa))
$(eval $(call BuildPackage,ppp-mod-pppoe))
$(eval $(call BuildPackage,ppp-mod-radius))
$(eval $(call BuildPackage,ppp-mod-pppol2tp))
$(eval $(call BuildPackage,ppp-mod-pptp))
$(eval $(call BuildPackage,ppp-mod-passwordfd))
$(eval $(call BuildPackage,chat))
$(eval $(call BuildPackage,pppdump))
$(eval $(call BuildPackage,pppstats))
$(eval $(call BuildPackage,pppoe-discovery))
