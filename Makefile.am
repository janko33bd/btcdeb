# Copyright (c) 2013-2018 The Bitcoin Core developers
# Distributed under the MIT software license, see the accompanying
# file COPYING or http://www.opensource.org/licenses/mit-license.php.

DIST_SUBDIRS = secp256k1

AM_LDFLAGS = $(LIBTOOL_LDFLAGS)
EXTRA_LIBRARIES =

BITCOIN_INCLUDES=-I$(builddir)

BITCOIN_INCLUDES += -I$(srcdir)/secp256k1/include

LIBBITCOIN=libbitcoin.a
LIBKERL=libkerl.a
LIBSECP256K1=secp256k1/libsecp256k1.la

$(LIBSECP256K1): $(wildcard secp256k1/src/*) $(wildcard secp256k1/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)

EXTRA_LIBRARIES += \
  $(LIBBITCOIN) \
  $(LIBKERL)

# lib_LTLIBRARIES = $(LIBBITCOIN)

bin_PROGRAMS = btcdeb btcc merklebranch mastify
noinst_PROGRAMS =

.PHONY: FORCE check-symbols check-security
# bitcoin core #
BITCOIN_CORE_H = \
	amount.h \
	base58.h \
	bech32.h \
	crypto/common.h \
	crypto/hmac_sha512.h \
	crypto/ripemd160.h \
	crypto/sha1.h \
	crypto/sha256.h \
	crypto/sha512.h \
	compat/byteswap.h \
	compat/endian.h \
	hash.h \
	interpreter.h \
	merkle.h \
	policy/policy.h \
	prevector.h \
	primitives/transaction.h \
	pubkey.h \
	script.h \
	script_error.h \
	serialize.h \
	streams.h \
	support/allocators/zeroafterfree.h \
	support/cleanse.h \
	tinyformat.h \
	uint256.h \
	utilstrencodings.h

# bitcoin: shared between btcdeb and btcc
libbitcoin_a_LIBADD = $(LIBSECP256K1)
libbitcoin_a_CPPFLAGS = $(AM_CPPFLAGS) $(BITCOIN_INCLUDES)
libbitcoin_a_CXXFLAGS = $(AM_CXXFLAGS)
libbitcoin_a_SOURCES = \
	base58.cpp \
	bech32.cpp \
	crypto/hmac_sha512.cpp \
	crypto/ripemd160.cpp \
	crypto/sha1.cpp \
	crypto/sha256.cpp \
	crypto/sha512.cpp \
	hash.cpp \
	interpreter.cpp \
	merkle.cpp \
	primitives/transaction.cpp \
	pubkey.cpp \
	script.cpp \
	script_error.cpp \
	support/cleanse.cpp \
	uint256.cpp \
	utilstrencodings.cpp \
    $(BITCOIN_CORE_H)

# kerl library #
libkerl_a_CPPFLAGS = $(AM_CPPFLAGS) -Ikerl
libkerl_a_CFLAGS = -std=gnu99
libkerl_a_SOURCES = kerl/kerl.c kerl/kerl.h

# btcdeb binary #
btcdeb_SOURCES = \
	btcdeb.cpp \
	value.h
btcdeb_CPPFLAGS = $(AM_CPPFLAGS) $(BITCOIN_INCLUDES)
btcdeb_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
btcdeb_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_AP_LDFLAGS)

btcdeb_LDADD = \
	$(LIBBITCOIN) \
	$(LIBKERL) \
	$(LIBSECP256K1)

# btcc binary #
btcc_SOURCES = \
	btcc.cpp \
	value.h
btcc_CPPFLAGS = $(AM_CPPFLAGS) $(BITCOIN_INCLUDES)
btcc_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)

btcc_LDADD = \
	$(LIBBITCOIN)

# merklebranch binary #
merklebranch_SOURCES = \
	merklebranch.cpp \
	value.h
merklebranch_CPPFLAGS = $(AM_CPPFLAGS) $(BITCOIN_INCLUDES)
merklebranch_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)

merklebranch_LDADD = \
	$(LIBBITCOIN)

# mastify binary #
mastify_SOURCES = \
	mastify.cpp \
	value.h
mastify_CPPFLAGS = $(AM_CPPFLAGS) $(BITCOIN_INCLUDES)
mastify_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
mastify_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_AP_LDFLAGS)

mastify_LDADD = \
	$(LIBBITCOIN) \
	$(LIBSECP256K1)

clean-local:
	-rm -f config.h $(LIBBITCOIN) $(LIBKERL) $(LIBSECP256K1)

.rc.o:
	@test -f $(WINDRES)
	## FIXME: How to get the appropriate modulename_CPPFLAGS in here?
	$(AM_V_GEN) $(WINDRES) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(CPPFLAGS) -DWINDRES_PREPROC -i $< -o $@

check-symbols: $(bin_PROGRAMS)
if GLIBC_BACK_COMPAT
	@echo "Checking glibc back compat..."
	$(AM_V_at) READELF=$(READELF) CPPFILT=$(CPPFILT) $(top_srcdir)/contrib/devtools/symbol-check.py < $(bin_PROGRAMS)
endif

%.pb.cc %.pb.h: %.proto
	@test -f $(PROTOC)
	$(AM_V_GEN) $(PROTOC) --cpp_out=$(@D) --proto_path=$(<D) $<

