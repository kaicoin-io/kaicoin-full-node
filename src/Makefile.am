walDIST_SUBDIRS = secp256k1
AM_LDFLAGS = $(PTHREAD_CFLAGS) $(LIBTOOL_LDFLAGS)


if EMBEDDED_LEVELDB
LEVELDB_CPPFLAGS += -I$(srcdir)/leveldb/include
LEVELDB_CPPFLAGS += -I$(srcdir)/leveldb/helpers/memenv
LIBLEVELDB += $(builddir)/leveldb/libleveldb.a
LIBMEMENV  += $(builddir)/leveldb/libmemenv.a

# NOTE: This dependency is not strictly necessary, but without it make may try to build both in parallel, which breaks the LevelDB build system in a race
$(LIBLEVELDB): $(LIBMEMENV)

$(LIBLEVELDB) $(LIBMEMENV):	
	@echo "Building LevelDB ..." && $(MAKE) -C $(@D) $(@F) CXX="$(CXX)" \
	  CC="$(CC)" PLATFORM=$(TARGET_OS) AR="$(AR)" $(LEVELDB_TARGET_FLAGS) \
          OPT="$(CXXFLAGS) $(CPPFLAGS)"
endif

BITCOIN_CONFIG_INCLUDES=-I$(builddir)/config
BITCOIN_INCLUDES=-I$(builddir) -I$(builddir)/obj $(BOOST_CPPFLAGS) $(LEVELDB_CPPFLAGS) $(CRYPTO_CFLAGS) $(SSL_CFLAGS)

BITCOIN_INCLUDES += -I$(srcdir)/secp256k1/include

LIBBITCOIN_SERVER=libbitcoin_server.a
LIBBITCOIN_WALLET=libbitcoin_wallet.a
LIBBITCOIN_COMMON=libbitcoin_common.a
LIBBITCOIN_CLI=libbitcoin_cli.a
LIBBITCOIN_UTIL=libbitcoin_util.a
LIBBITCOIN_CRYPTO=crypto/libbitcoin_crypto.a
LIBBITCOIN_KAICOIN=kaicoin/libbitcoin_kaicoin.a
LIBBITCOIN_UNIVALUE=univalue/libbitcoin_univalue.a
LIBBITCOINQT=qt/libbitcoinqt.a
LIBSECP256K1=secp256k1/libsecp256k1.la

$(LIBSECP256K1): $(wildcard secp256k1/src/*) $(wildcard secp256k1/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)

# Make is not made aware of per-object dependencies to avoid limiting building parallelization
# But to build the less dependent modules first, we manually select their order here:
EXTRA_LIBRARIES = \
  crypto/libbitcoin_crypto.a \
  kaicoin/libbitcoin_kaicoin.a \
  libbitcoin_util.a \
  libbitcoin_common.a \
  univalue/libbitcoin_univalue.a \
  libbitcoin_server.a \
  libbitcoin_cli.a
if ENABLE_WALLET
BITCOIN_INCLUDES += $(BDB_CPPFLAGS)
EXTRA_LIBRARIES += libbitcoin_wallet.a
endif

if BUILD_BITCOIN_LIBS
lib_LTLIBRARIES = libbitcoinconsensus.la
LIBBITCOIN_CONSENSUS=libbitcoinconsensus.la
else
LIBBITCOIN_CONSENSUS=
endif

bin_PROGRAMS =
TESTS =

if BUILD_BITCOIND
#  bin_PROGRAMS += bitcoind kaicoind						# MCHN
  bin_PROGRAMS += kaicoind kaicoind-cold					# MCHN
endif

if BUILD_BITCOIN_UTILS
#  bin_PROGRAMS += kaicoin-util kaicoin-cli bitcoin-cli bitcoin-tx		# MCHN
  bin_PROGRAMS += kaicoin-util kaicoin-cli 		# MCHN
endif

.PHONY: FORCE
# bitcoin core #
BITCOIN_CORE_H = \
  storage/addrman.h \
  structs/alert.h \
  utils/allocators.h \
  structs/amount.h \
  structs/base58.h \
  structs/bloom.h \
  chain/chain.h \
  chainparams/chainparams.h \
  chainparams/chainparamsbase.h \
  chainparams/chainparamsseeds.h \
  chain/checkpoints.h \
  checkqueue.h \
  version/clientversion.h \
  coincontrol.h \
  storage/coins.h \
  utils/compat.h \
  utils/compressor.h \
  primitives/block.h \
  primitives/transaction.h \
  utils/core_io.h \
  wallet/crypter.h \
  wallet/db.h \
  structs/hash.h \
  core/init.h \
  keys/key.h \
  wallet/keystore.h \
  storage/leveldbwrapper.h \
  structs/limitedmap.h \
  core/main.h \
  chain/merkleblock.h \
  miner/miner.h \
  utils/mruset.h \
  net/netbase.h \
  net/net.h \
  ui/noui.h \
  chain/pow.h \
  protocol/netprotocol.h \
  keys/pubkey.h \
  utils/random.h \
  utils/utilparse.h \
  rpc/rpcclient.h \
  rpc/rpcprotocol.h \
  rpc/rpcutils.h \
  rpc/rpcwallet.h \
  rpc/rpcserver.h \
  script/interpreter.h \
  script/interpreter.h \
  script/script.h \
  script/sigcache.h \
  script/sign.h \
  script/standard.h \
  script/script_error.h \
  utils/serialize.h \
  utils/streams.h \
  utils/sync.h \
  utils/threadsafety.h \
  utils/timedata.h \
  utils/tinyformat.h \
  storage/txdb.h \
  chain/txmempool.h \
  ui/ui_interface.h \
  structs/uint256.h \
  chain/undo.h \
  utils/util.h \
  utils/utilstrencodings.h \
  utils/utilmoneystr.h \
  utils/utiltime.h \
  version/bcversion.h \
  wallet/wallet.h \
  wallet/wallettxs.h \
  wallet/wallet_ismine.h \
  wallet/walletdb.h \
  compat/sanity.h

JSON_H = \
  json/json_spirit.h \
  json/json_spirit_error_position.h \
  json/json_spirit_reader.h \
  json/json_spirit_reader_template.h \
  json/json_spirit_stream_reader.h \
  json/json_spirit_utils.h \
  json/json_spirit_value.h \
  json/json_spirit_writer.h \
  json/json_spirit_writer_template.h \
  json_spirit_ubjson.h

obj/build.h: FORCE
	@$(MKDIR_P) $(builddir)/obj
	@$(top_srcdir)/share/genbuild.sh $(abs_top_builddir)/src/obj/build.h \
	  $(abs_top_srcdir)
libbitcoin_util_a-clientversion.$(OBJEXT): obj/build.h

# server: shared between bitcoind and bitcoin-qt
libbitcoin_server_a_CPPFLAGS = $(BITCOIN_INCLUDES) $(MINIUPNPC_CPPFLAGS)
libbitcoin_server_a_SOURCES = \
  storage/addrman.cpp \
  structs/alert.cpp \
  structs/bloom.cpp \
  chain/chain.cpp \
  chain/checkpoints.cpp \
  core/init.cpp \
  storage/leveldbwrapper.cpp \
  core/main.cpp \
  protocol/kaicointx.cpp \
  protocol/kaicoinblock.cpp \
  custom/custom_server.cpp \
  protocol/handshake.cpp \
  chain/merkleblock.cpp \
  miner/miner.cpp \
  net/net.cpp \
  ui/noui.cpp \
  chain/pow.cpp \
  net/rest.cpp \
  utils/utilparse.cpp \
  json/json_spirit_ubjson.cpp \
  rpc/rpcrawdata.cpp \
  rpc/rpcutils.cpp \
  rpc/rpchelp.cpp \
  rpc/rpcblockchain.cpp \
  rpc/rpcmining.cpp \
  rpc/rpcmisc.cpp \
  rpc/rpcnet.cpp \
  rpc/rpcrawtransaction.cpp \
  rpc/rpcserver.cpp \
  script/sigcache.cpp \
  utils/timedata.cpp \
  storage/txdb.cpp \
  chain/txmempool.cpp \
  $(JSON_H) \
  $(BITCOIN_CORE_H)

# wallet: shared between bitcoind and bitcoin-qt, but only linked
# when wallet enabled
libbitcoin_wallet_a_CPPFLAGS = $(BITCOIN_INCLUDES)
libbitcoin_wallet_a_SOURCES = \
  wallet/db.cpp \
  wallet/crypter.cpp \
  rpc/rpcdump.cpp \
  rpc/rpcwallet.cpp \
  rpc/rpcwalletutils.cpp \
  rpc/rpcwallettxs.cpp \
  rpc/rpcexchange.cpp \
  rpc/rpcwalletsend.cpp \
  rpc/rpcpermissions.cpp \
  rpc/rpcassets.cpp \
  rpc/rpcstreams.cpp \
  rpc/rpcupgrades.cpp \
  wallet/wallet.cpp \
  wallet/walletcoins.cpp \
  wallet/wallettxs.cpp \
  wallet/wallet_ismine.cpp \
  wallet/walletdb.cpp \
  $(BITCOIN_CORE_H)

# crypto primitives library
crypto_libbitcoin_crypto_a_CPPFLAGS = $(BITCOIN_CONFIG_INCLUDES)
crypto_libbitcoin_crypto_a_SOURCES = \
  crypto/sha1.cpp \
  crypto/sha256.cpp \
  crypto/sha512.cpp \
  crypto/hmac_sha256.cpp \
  crypto/hmac_sha512.cpp \
  crypto/ripemd160.cpp \
  crypto/common.h \
  crypto/sha256.h \
  crypto/sha512.h \
  crypto/hmac_sha256.h \
  crypto/hmac_sha512.h \
  crypto/sha1.h \
  crypto/ripemd160.h

# kaicoin library
kaicoin_libbitcoin_kaicoin_a_CPPFLAGS = $(BITCOIN_CONFIG_INCLUDES)
kaicoin_libbitcoin_kaicoin_a_SOURCES = \
  utils/utility.cpp \
  utils/systemdependent.cpp \
  utils/tools.cpp \
  utils/utilwrapper.cpp \
  version/version.cpp \
  custom/custom.cpp \
  custom/custom_kaicoin.cpp \
  chainparams/params.cpp \
  protocol/kaicoinscript.cpp \
  utils/dbwrapper.cpp \
  wallet/wallettxdb.cpp \
  permissions/permission.cpp \
  entities/asset.cpp

# univalue JSON library
univalue_libbitcoin_univalue_a_SOURCES = \
  univalue/univalue.cpp \
  univalue/univalue_read.cpp \
  univalue/univalue_write.cpp \
  univalue/univalue_escapes.h \
  univalue/univalue.h

# common: shared between bitcoind, and bitcoin-qt and non-server tools
libbitcoin_common_a_CPPFLAGS = $(BITCOIN_INCLUDES)
libbitcoin_common_a_SOURCES = \
  utils/allocators.cpp \
  structs/amount.cpp \
  structs/base58.cpp \
  chainparams/chainparams.cpp \
  storage/coins.cpp \
  utils/compressor.cpp \
  primitives/block.cpp \
  primitives/transaction.cpp \
  utils/core_read.cpp \
  utils/core_write.cpp \
  structs/hash.cpp \
  keys/key.cpp \
  wallet/keystore.cpp \
  net/netbase.cpp \
  protocol/netprotocol.cpp \
  keys/pubkey.cpp \
  script/interpreter.cpp \
  script/script.cpp \
  script/sign.cpp \
  script/standard.cpp \
  script/script_error.cpp \
  $(BITCOIN_CORE_H)

# util: shared between all executables.
# This library *must* be included to make sure that the glibc
# backward-compatibility objects and their sanity checks are linked.
libbitcoin_util_a_CPPFLAGS = $(BITCOIN_INCLUDES)
libbitcoin_util_a_SOURCES = \
  compat/strnlen.cpp \
  compat/glibc_sanity.cpp \
  compat/glibcxx_sanity.cpp \
  chainparams/chainparamsbase.cpp \
  version/clientversion.cpp \
  utils/random.cpp \
  rpc/rpcprotocol.cpp \
  utils/sync.cpp \
  structs/uint256.cpp \
  utils/util.cpp \
  utils/utilstrencodings.cpp \
  utils/utilmoneystr.cpp \
  utils/utiltime.cpp \
  $(BITCOIN_CORE_H)

if GLIBC_BACK_COMPAT
libbitcoin_util_a_SOURCES += compat/glibc_compat.cpp
libbitcoin_util_a_SOURCES += compat/glibcxx_compat.cpp
endif

# cli: shared between bitcoin-cli and bitcoin-qt
libbitcoin_cli_a_CPPFLAGS = $(BITCOIN_INCLUDES)
libbitcoin_cli_a_SOURCES = \
  rpc/rpcclient.cpp \
  $(BITCOIN_CORE_H)

nodist_libbitcoin_util_a_SOURCES = $(srcdir)/obj/build.h
#

# bitcoind binary #
#bitcoind_LDADD = \
  $(LIBBITCOIN_SERVER) \
  $(LIBBITCOIN_COMMON) \
  $(LIBBITCOIN_UNIVALUE) \
  $(LIBBITCOIN_WALLET) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBMEMENV) \
  $(LIBSECP256K1)

#if ENABLE_WALLET
#bitcoind_LDADD += libbitcoin_wallet.a
#endif
#bitcoind_LDADD += kaicoin/libbitcoin_kaicoin.a
#bitcoind_SOURCES = bitcoind.cpp chainparams/buildgenesis.cpp
#

if TARGET_WINDOWS
#bitcoind_SOURCES += bitcoind-res.rc
endif

#bitcoind_LDADD += $(BOOST_LIBS) $(BDB_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(MINIUPNPC_LIBS)
#bitcoind_CPPFLAGS = $(BITCOIN_INCLUDES)
#bitcoind_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

# bitcoin-cli binary #
#bitcoin_cli_LDADD = \
  $(LIBBITCOIN_CLI) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(BOOST_LIBS) \
  $(SSL_LIBS) \
  $(CRYPTO_LIBS)

#bitcoin_cli_SOURCES = \
  bitcoin-cli.cpp

#bitcoin_cli_CPPFLAGS = $(BITCOIN_INCLUDES)
#

# bitcoin-tx binary #
#bitcoin_tx_LDADD = \
  $(LIBBITCOIN_COMMON) \
  $(LIBBITCOIN_UNIVALUE) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBSECP256K1) \
  $(BOOST_LIBS) \
  $(CRYPTO_LIBS)

#bitcoin_tx_SOURCES = bitcoin-tx.cpp 
#bitcoin_tx_CPPFLAGS = $(BITCOIN_INCLUDES)
#
#bitcoin_tx_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
#bitcoin_cli_SOURCES += bitcoin-cli-res.rc
endif
#bitcoin_cli_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

# MCHN START

# kaicoind binary #								
kaicoind_LDADD = \
  $(LIBBITCOIN_SERVER) \
  $(LIBBITCOIN_COMMON) \
  $(LIBBITCOIN_UNIVALUE) \
  $(LIBBITCOIN_WALLET) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBMEMENV) \
  $(LIBSECP256K1)

#if ENABLE_WALLET
#kaicoind_LDADD += libbitcoin_wallet.a
#endif
kaicoind_SOURCES = kaicoin/kaicoind.cpp rpc/rpclist.cpp chainparams/buildgenesis.cpp
#

if TARGET_WINDOWS
kaicoind_SOURCES += kaicoind-res.rc
endif

kaicoind_LDADD += $(BOOST_LIBS) $(BDB_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(MINIUPNPC_LIBS)
kaicoind_CPPFLAGS = $(BITCOIN_INCLUDES)
kaicoind_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

# kaicoind-cold binary #								
kaicoind_cold_LDADD = \
  $(LIBBITCOIN_SERVER) \
  $(LIBBITCOIN_COMMON) \
  $(LIBBITCOIN_UNIVALUE) \
  $(LIBBITCOIN_WALLET) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBMEMENV) \
  $(LIBSECP256K1)

#if ENABLE_WALLET
#kaicoind_cold_LDADD += libbitcoin_wallet.a
#endif
kaicoind_cold_SOURCES = kaicoin/kaicoind-cold.cpp core/init-cold.cpp rpc/rpclist-cold.cpp chainparams/buildgenesis.cpp
#

if TARGET_WINDOWS
kaicoind_cold_SOURCES += kaicoind-cold-res.rc
endif

kaicoind_cold_LDADD += $(BOOST_LIBS) $(BDB_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(MINIUPNPC_LIBS)
kaicoind_cold_CPPFLAGS = $(BITCOIN_INCLUDES)
kaicoind_cold_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

# kaicoin-cli binary #
kaicoin_cli_LDADD = \
  $(LIBBITCOIN_CLI) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(BOOST_LIBS) \
  $(SSL_LIBS) \
  $(CRYPTO_LIBS)

kaicoin_cli_SOURCES =  kaicoin/kaicoin-cli.cpp

kaicoin_cli_CPPFLAGS = $(BITCOIN_INCLUDES)
#

if TARGET_WINDOWS
kaicoin_cli_SOURCES += kaicoin-cli-res.rc
endif
kaicoin_cli_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

# kaicoin-util binary #
kaicoin_util_LDADD = \
  $(LIBBITCOIN_UNIVALUE) \
  $(LIBBITCOIN_KAICOIN) \
  $(LIBBITCOIN_COMMON) \
  $(LIBBITCOIN_UTIL) \
  $(LIBBITCOIN_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBSECP256K1) \
  $(BOOST_LIBS) \
  $(CRYPTO_LIBS)

kaicoin_util_SOURCES = kaicoin/kaicoin-util.cpp chainparams/buildgenesis.cpp

kaicoin_util_CPPFLAGS = $(BITCOIN_INCLUDES)
#
if TARGET_WINDOWS
kaicoin_util_SOURCES += kaicoin-util-res.rc
endif
kaicoin_util_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)


# MCHN END




if BUILD_BITCOIN_LIBS
include_HEADERS = script/bitcoinconsensus.h
libbitcoinconsensus_la_SOURCES = \
  primitives/transaction.cpp \
  crypto/hmac_sha512.cpp \
  crypto/sha1.cpp \
  crypto/sha256.cpp \
  crypto/sha512.cpp \
  crypto/ripemd160.cpp \
  utils/utility.cpp \
  utils/systemdependent.cpp \
  utils/tools.cpp \
  utils/utilwrapper.cpp \
  chainparams/buildgenesis.cpp \
  version/version.cpp \
  custom/custom.cpp \
  custom/custom_kaicoin.cpp \
  chainparams/chainparams.cpp \
  protocol/kaicoinscript.cpp \
  utils/dbwrapper.cpp \
  wallet/wallettxdb.cpp \
  permissions/permission.cpp \
  entities/asset.cpp \
  structs/hash.cpp \
  keys/pubkey.cpp \
  script/script.cpp \
  script/interpreter.cpp \
  script/bitcoinconsensus.cpp \
  structs/uint256.cpp \
  utils/utilstrencodings.cpp

if GLIBC_BACK_COMPAT
  libbitcoinconsensus_la_SOURCES += compat/glibc_compat.cpp
  libbitcoinconsensus_la_SOURCES += compat/glibcxx_compat.cpp
endif

libbitcoinconsensus_la_LDFLAGS = -no-undefined $(RELDFLAGS)
libbitcoinconsensus_la_LIBADD = $(LIBSECP256K1)
libbitcoinconsensus_la_CPPFLAGS = -I$(builddir)/obj -I$(srcdir)/secp256k1/include -DBUILD_BITCOIN_INTERNAL
endif

CLEANFILES = leveldb/libleveldb.a leveldb/libmemenv.a *.gcda *.gcno

DISTCLEANFILES = obj/build.h

EXTRA_DIST = leveldb

clean-local:
	-$(MAKE) -C leveldb clean
	-$(MAKE) -C secp256k1 clean
	rm -f leveldb/*/*.gcno leveldb/helpers/memenv/*.gcno
	-rm -f config.h

.rc.o:
	@test -f $(WINDRES)
	$(AM_V_GEN) $(WINDRES) -DWINDRES_PREPROC -i $< -o $@

.mm.o:
	$(AM_V_CXX) $(OBJCXX) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(AM_CPPFLAGS) \
	  $(CPPFLAGS) $(AM_CXXFLAGS) $(QT_INCLUDES) $(CXXFLAGS)  -c -o $@ $<

%.pb.cc %.pb.h: %.proto
	@test -f $(PROTOC)
	$(AM_V_GEN) $(PROTOC) --cpp_out=$(@D) --proto_path=$(abspath $(<D) $<)

#if ENABLE_TESTS
#include Makefile.test.include
#endif

#if ENABLE_QT
#include Makefile.qt.include
#endif

#if ENABLE_QT_TESTS
#include Makefile.qttest.include
#endif
