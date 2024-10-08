PKG_NAME:=mihomo
PKG_RELEASE:=2
PKG_VERSION:=1.18.8

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/MetaCubeX/mihomo/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=2d4d4bac7832cea737557f7ea4fd09d12dee777ec37b6da26a1fa42c1dfdf962

PKG_LICENSE:=MIT
PKG_MAINTAINER:=nosignals

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/metacubex/mihomo
GO_PKG_LDFLAGS_X:=$(GO_PKG)/constant.Version=v$(PKG_VERSION)
GO_PKG_TAGS:=with_gvisor

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/$(PKG_NAME)
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Universal proxy platform.
  URL:=https://wiki.metacubex.one
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle +curl +yq firewall4 +kmod-nft-tproxy +ip-full +kmod-tun
  USERID:=mihomo=7890:mihomo=7890
endef

define Package/mihomo/description
  A rule based proxy in Go.
endef

define Package/mihomo/conffiles
/etc/config/mihomo
/etc/mihomo/mixin.yaml
/etc/mihomo/nftables/reserved_ip.nft
/etc/mihomo/nftables/reserved_ip6.nft
endef

define Package/mihomo/install
	$(call GoPackage/Package/Install/Bin,$(1))

	$(INSTALL_DIR) $(1)/etc/mihomo
	$(INSTALL_DIR) $(1)/etc/mihomo/scripts
	$(INSTALL_DIR) $(1)/etc/mihomo/nftables
	$(INSTALL_DIR) $(1)/etc/mihomo/profiles
	$(INSTALL_DIR) $(1)/etc/mihomo/run
	$(INSTALL_DIR) $(1)/etc/mihomo/run/rules
	$(INSTALL_DIR) $(1)/etc/mihomo/run/ui

	$(INSTALL_DATA) $(CURDIR)/files/mixin.yaml $(1)/etc/mihomo/mixin.yaml

	$(INSTALL_BIN) $(CURDIR)/files/scripts/constants.sh $(1)/etc/mihomo/scripts/constants.sh
	$(INSTALL_BIN) $(CURDIR)/files/scripts/tun.sh $(1)/etc/mihomo/scripts/tun.sh

	$(INSTALL_BIN) $(CURDIR)/files/nftables/hijack.nft $(1)/etc/mihomo/nftables/hijack.nft
	$(INSTALL_BIN) $(CURDIR)/files/nftables/reserved_ip.nft $(1)/etc/mihomo/nftables/reserved_ip.nft
	$(INSTALL_BIN) $(CURDIR)/files/nftables/reserved_ip6.nft $(1)/etc/mihomo/nftables/reserved_ip6.nft
	$(INSTALL_BIN) $(CURDIR)/files/nftables/geoip_cn.nft $(1)/etc/mihomo/nftables/geoip_cn.nft
	$(INSTALL_BIN) $(CURDIR)/files/nftables/geoip6_cn.nft $(1)/etc/mihomo/nftables/geoip6_cn.nft

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) $(CURDIR)/files/mihomo.conf $(1)/etc/config/mihomo

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(CURDIR)/files/mihomo.init $(1)/etc/init.d/mihomo

	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) $(CURDIR)/files/uci-defaults/init.sh $(1)/etc/uci-defaults/99_init_mihomo
	$(INSTALL_BIN) $(CURDIR)/files/uci-defaults/migrate.sh $(1)/etc/uci-defaults/99_migrate_mihomo

	$(INSTALL_DIR) $(1)/etc/capabilities
	$(INSTALL_DATA) $(CURDIR)/files/capabilities.json $(1)/etc/capabilities/mihomo.json

	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	$(INSTALL_DATA) $(CURDIR)/files/mihomo.upgrade $(1)/lib/upgrade/keep.d/mihomo
endef

define Build/Prepare
	$(Build/Prepare/Default)
	$(RM) -r $(PKG_BUILD_DIR)/rules/logic_test
endef

$(eval $(call GoBinPackage,$(PKG_NAME)))
$(eval $(call BuildPackage,$(PKG_NAME)))
