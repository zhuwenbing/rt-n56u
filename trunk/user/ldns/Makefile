# Padavan hanwckf rt-n56u trunk/user/ldns/Makefile
# ldns-1.8.4, build drill and link to dig
# auto download, checksum, extract

LDNS_VER := 1.8.4
LDNS_TAR := ldns-$(LDNS_VER).tar.gz
# primary + fallbacks
LDNS_URLS := \
	https://mirror.ghproxy.com/https://nlnetlabs.nl/downloads/ldns/$(LDNS_TAR) \
	https://nlnetlabs.nl/downloads/ldns/$(LDNS_TAR) \
	https://github.com/NLnetLabs/ldns/releases/download/$(LDNS_VER)/$(LDNS_TAR)
LDNS_SHA256 := 58f976f4c347b75c1b815430e80a40687a2671714ab4c4bc6d448b5c4a6cfd45
LDNS_SRC_DIR := $(CURDIR)/ldns-$(LDNS_VER)
BUILD_DIR := $(BUILD_TOP)/user/ldns

CONFIGURE_ENV = \
	CC=$(CROSS)gcc \
	CXX=$(CROSS)g++ \
	AR=$(CROSS)ar \
	RANLIB=$(CROSS)ranlib \
	STRIP=$(CROSS)strip \
	LD=$(CROSS)ld

CONFIGURE_OPTS = \
	--host=mipsel-linux-uclibc \
	--prefix=/usr \
	--with-drill \
	--without-ssl \
	--disable-shared \
	--enable-static \
	--disable-python \
	--disable-gost \
	--disable-dane

# download + sha256 verify, with retries and fallbacks
$(LDNS_TAR):
	@echo "Downloading $(LDNS_TAR)..."
	@rm -f $@
	@set -e; \
	for url in $(LDNS_URLS); do \
	  echo "Trying $$url"; \
	  if wget --tries=3 --timeout=30 -O $@ "$$url"; then \
	    echo "Downloaded from $$url"; break; \
	  else \
	    echo "Failed: $$url"; continue; \
	  fi; \
	done; \
	[ -f $@ ] || (echo "ERROR: Unable to download $(LDNS_TAR) from any mirror" && exit 1); \
	echo "$(LDNS_SHA256)  $@" | sha256sum -c -

# extract marker
$(LDNS_SRC_DIR)/.extracted: $(LDNS_TAR)
	rm -rf $(LDNS_SRC_DIR)
	tar -xf $(LDNS_TAR)
	touch $@

# configure
$(BUILD_DIR)/Makefile: $(LDNS_SRC_DIR)/.extracted
	mkdir -p $(BUILD_DIR)
	cd $(BUILD_DIR) && $(CONFIGURE_ENV) $(LDNS_SRC_DIR)/configure $(CONFIGURE_OPTS)

all: $(BUILD_DIR)/Makefile
	$(MAKE) -C $(BUILD_DIR)
	$(CROSS)strip $(BUILD_DIR)/src/drill/drill

romfs:
	$(INSTALL_BIN) $(BUILD_DIR)/src/drill/drill $(TARGET_DIR)/usr/bin/drill
	ln -sf drill $(TARGET_DIR)/usr/bin/dig

clean:
	rm -rf $(BUILD_DIR)
	rm -rf $(LDNS_SRC_DIR)
	rm -f $(LDNS_SRC_DIR)/.extracted
# uncomment below line if you want clean remove tarball
#	rm -f $(LDNS_TAR)
