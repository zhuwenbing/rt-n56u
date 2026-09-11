# ldns‑1.7.1 For hanwckf rt‑n56u original firmware
include $(TOPDIR)/rules.mak

VER := 1.7.1
DL_FILE := ldns-$(VER).tar.gz
DL_URL := https://www.nlnetlabs.nl/downloads/ldns/$(DL_FILE)

SRCDIR := $(SRCTMP)/ldns-$(VER)

ifeq ($(CONFIG_FIRMWARE_INCLUDE_LDNS),y)
ALL_TARGET += build
INSTALL_TARGET += install
endif

download:
	[ -d $(DLDIR) ] || mkdir -p $(DLDIR)
	[ -f $(DLDIR)/$(DL_FILE) ] || $(WGET) $(DL_URL) -O $(DLDIR)/$(DL_FILE)
	rm -rf $(SRCDIR)
	mkdir -p $(SRCTMP)
	tar -xf $(DLDIR)/$(DL_FILE) -C $(SRCTMP)

build: download
	cd $(SRCDIR) && \
	$(TARGET_CONFIGURE_OPTS) \
	CFLAGS="$(TARGET_CFLAGS) -Os -D_BSD_SOURCE -D_POSIX_C_SOURCE=200112L -DNO_RECVMMSG" \
	LDFLAGS="$(TARGET_LDFLAGS) -static --static" \
	./configure \
		--host=$(TARGET_HOST) \
		--prefix=/usr \
		--sysconfdir=/etc \
		--disable-shared \
		--enable-static \
		--without-pyldns \
		--disable-examples \
		--disable-doxygen \
		--disable-manpages \
		--without-ssl \
		--disable-rpath \
		--disable-gost

	$(MAKE) -C $(SRCDIR)

install: build
	install -D -m 755 $(SRCDIR)/tools/dig/ldns-dig $(ROMFS)/usr/bin/ldns-dig
	ln -sf ldns-dig $(ROMFS)/usr/bin/dig

clean:
	rm -rf $(SRCDIR)

.PHONY: download build install clean
