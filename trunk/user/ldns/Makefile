# ====================================================================
# Padavan Makefile for ldns (Optimized for GitHub Actions)
# ====================================================================

PKG_NAME:=ldns
PKG_VERSION:=1.8.4
PKG_RELEASE:=1

# ldns下载路径
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://www.nlnetlabs.nl/downloads/ldns/
PKG_BUILD_DIR:=$(shell pwd)/$(PKG_NAME)-$(PKG_VERSION)

THISDIR=$(shell pwd)

.PHONY: all download extract config_test clean romfs

all: config_test
	$(MAKE) -C $(PKG_BUILD_DIR)

# 1. 自动下载源码包
download:
	@if [ ! -f $(PKG_SOURCE) ]; then \
		echo "Downloading $(PKG_SOURCE)..."; \
		curl -sL $(PKG_SOURCE_URL)$(PKG_SOURCE) -o $(PKG_SOURCE); \
	fi

# 2. 自动解压
extract: download
	@if [ ! -d $(PKG_BUILD_DIR) ]; then \
		echo "Extracting $(PKG_SOURCE)..."; \
		tar -zxf $(PKG_SOURCE); \
	fi

# 3. 交叉编译配置 (预先注入 Padavan 的环境变量)
config_test: extract
	@if [ ! -f $(PKG_BUILD_DIR)/Makefile ]; then \
		cd $(PKG_BUILD_DIR) && \
		./configure \
			--host=$(HOST_TARGET) \
			--build=$(HOST_BUILD) \
			--prefix=/usr \
			--with-drill \
			--disable-shared \
			--enable-static \
			--disable-gost \
			--disable-ecdsa \
			--disable-ed25519 \
			--with-ssl=$(STAGEDIR) \
			CC="$(CC)" \
			CFLAGS="$(CFLAGS) -I$(STAGEDIR)/include" \
			LDFLAGS="$(LDFLAGS) -L$(STAGEDIR)/lib"; \
	fi

clean:
	if [ -f $(PKG_BUILD_DIR)/Makefile ]; then \
		$(MAKE) -C $(PKG_BUILD_DIR) clean; \
	fi
	rm -rf $(PKG_BUILD_DIR) $(PKG_SOURCE)

# 4. 提取生成的 drill 工具并打入固件，同时创建 dig 软链接
romfs:
	# 复制 drill 主程序到固件中
	$(ROMFSINST) -p +x $(PKG_BUILD_DIR)/drill/drill /usr/bin/drill
	# 在固件中创建一个名为 dig 的软链接，指向 drill
	$(ROMFSINST) -s /usr/bin/drill /usr/bin/dig
