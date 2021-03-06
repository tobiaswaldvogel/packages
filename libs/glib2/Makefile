#
# Copyright (C) 2007-2019 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=glib2
PKG_VERSION:=2.64.2
PKG_RELEASE:=2

PKG_SOURCE:=glib-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@GNOME/glib/2.64
PKG_HASH:=9a2f21ed8f13b9303399de13a0252b7cbcede593d26971378ec6cb90e87f2277

PKG_MAINTAINER:=Peter Wagner <tripolar@gmx.at>
PKG_LICENSE:=LGPL-2.1-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:gnome:glib

PKG_BUILD_DIR:=$(BUILD_DIR)/glib-$(PKG_VERSION)
HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/glib-$(PKG_VERSION)
PKG_BUILD_DEPENDS:=meson/host
HOST_BUILD_DEPENDS:=meson/host libffi/host libiconv/host
PKG_INSTALL:=1

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include ../../devel/meson/meson.mk

define Package/glib2
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+zlib +libpthread +libffi +libattr @!USE_UCLIBC
  TITLE:=glib 2.0
  URL:=http://www.gtk.org/
endef

define Package/glib2/description
  The GLib library of C routines
endef

HOST_LDFLAGS += -liconv
TARGET_CFLAGS += -ffunction-sections -fdata-sections -flto
TARGET_LDFLAGS += -Wl,--gc-sections

COMP_ARGS= \
	-Dselinux=disabled \
	-Dlibmount=disabled \
	-Dinternal_pcre=true \
	-Dman=false \
	-Ddtrace=false \
	-Dsystemtap=false \
	-Dgtk_doc=false \
	-Dbsymbolic_functions=true \
	-Dforce_posix_threads=true \
	-Dfam=false \
	-Dinstalled_tests=false \
	-Dnls=disabled \
	-Doss_fuzz=disabled

MESON_HOST_ARGS += $(COMP_ARGS) -Dxattr=false
MESON_ARGS += $(COMP_ARGS) -Dxattr=true

# hack to compile static libraries
define Build/Compile
	$(call Build/Compile/Meson)
	$(call Ninja,-C $(MESON_BUILD_DIR) install,DESTDIR="$(PKG_INSTALL_DIR)")
	$(call Meson, \
		--reconfigure \
		--buildtype plain \
		--native-file $(PKG_BUILD_DIR)/openwrt-native.txt \
		--cross-file $(PKG_BUILD_DIR)/openwrt-cross.txt \
		-Ddefault_library=static \
		$(MESON_ARGS) \
		$(MESON_BUILD_DIR) \
		$(PKG_BUILD_DIR), \
		$(MESON_VARS))
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/bin/* \
		$(1)/usr/bin/

	$(INSTALL_DIR) $(1)/usr/include
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/include/glib-2.0 \
		$(1)/usr/include/
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/glib-2.0/include/*.h \
		$(1)/usr/include/glib-2.0/
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/include/gio-unix-2.0 \
		$(1)/usr/include/

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/glib-2.0 \
		$(1)/usr/lib/

	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/*.{so*,a} \
		$(1)/usr/lib/

	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/usr/lib/pkgconfig/*.pc \
		$(1)/usr/lib/pkgconfig

	$(INSTALL_DIR) $(2)/share/aclocal/
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/usr/share/aclocal/*.m4  \
		$(2)/share/aclocal/
endef

define Package/glib2/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/*.so* \
		$(1)/usr/lib/
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,glib2))
