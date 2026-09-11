# Padavan hanwckf rt-n56u trunk/user/ldns/Makefile
# ldns-1.8.4, build drill and link to dig
# auto download, checksum, extract

LDNS_VER := 1.8.4
LDNS_TAR := ldns-$(LDNS_VER).tar.gz
LDNS_URL := https://www.nlnetlabs.nl/downloads/ldns/$(LDNS_TAR)
LDNS_SHA256 := 838b907594baaff1cd767e95466a7745998ae64bc74be038dccc62e2de2e4247
LDNS_SRC_DIR := $(CURDIR)/ldns-$(LDNS_VER)

# Use BUILD_TOP if provided by the top-level build; otherwise fall back to a local build dir
ifeq ($(strip $(BUILD_TOP)),)
BUILD_DIR := $(CURDIR)/build
else
BUILD_DIR := $(BUILD_TOP)/user/ldns
endif

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
	--enable-drill \
	--without-ssl \
	--disable-shared \
	--enable-static \
	--disable-python \
	--disable-gost \
	--disable-dane

# download + sha256 verify
$(LDNS_TAR):
	wget -O $@ $(LDNS_URL)
	echo "$(LDNS_SHA256)  $@" | sha256sum -c

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

# make sure romfs builds first and fails early if drill isn't built
romfs: all
	@test -f $(BUILD_DIR)/src/drill/drill || (echo "ERROR: drill not found at $(BUILD_DIR)/src/drill/drill; check build output" >&2; exit 1)
	$(INSTALL_BIN) $(BUILD_DIR)/src/drill/drill $(TARGET_DIR)/usr/bin/drill
	ln -sf drill $(TARGET_DIR)/usr/bin/dig

clean:
	rm -rf $(BUILD_DIR)
	rm -rf $(LDNS_SRC_DIR)
	rm -f $(LDNS_SRC_DIR)/.extracted
# uncomment below line if you want clean remove tarball
#	rm -f $(LDNS_TAR)
