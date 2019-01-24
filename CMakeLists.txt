cmake_minimum_required(VERSION 2.8)

include(CryptoNoteWallet.cmake)
include(QREncode.cmake)

project(${CN_PROJECT_NAME})

execute_process(COMMAND git log -1 --pretty=format:%h
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE GIT_REVISION)

set(CRYPTONOTE_LIB cryptonote)

include_directories(${CMAKE_BINARY_DIR}
    src
    imkers/external
    imkers/include
    imkers/src
    libqrencode)

if (WIN32)
	set (CMAKE_PREFIX_PATH "C:\\Qt\\Qt5.9.0\\5.9\\msvc2017_64")
endif()

find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5Network REQUIRED)

set(Boost_USE_STATIC_LIBS ON)
if(WIN32)
  set(Boost_USE_STATIC_RUNTIME OFF)
else(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
endif(WIN32)

find_package(Boost 1.58 REQUIRED COMPONENTS date_time filesystem program_options regex serialization system thread chrono)
if ((${Boost_MAJOR_VERSION} EQUAL 1) AND (${Boost_MINOR_VERSION} EQUAL 54))
	message(SEND_ERROR "Boost version 1.54 is unsupported, more details are available here http://goo.gl/RrCFmA")
endif ()

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

set(VERSION "")
configure_file("imkers/src/version.h.in" "version.h")
configure_file("src/CryptoNoteWalletConfig.h.in" "CryptoNoteWalletConfig.h")

add_definitions(-DVERSION=\"${CN_VERSION}\")
add_definitions(-DGIT_REVISION=\"${GIT_REVISION}\")

set(CMAKE_AUTOMOC ON)

set(CRYPTONOTE_SOURCES
  imkers/external/miniupnpc/connecthostport.c
  imkers/external/miniupnpc/igd_desc_parse.c
  imkers/external/miniupnpc/minisoap.c
  imkers/external/miniupnpc/miniupnpc.c
  imkers/external/miniupnpc/miniwget.c
  imkers/external/miniupnpc/minixml.c
  imkers/external/miniupnpc/portlistingparse.c
  imkers/external/miniupnpc/receivedata.c
  imkers/external/miniupnpc/upnpcommands.c
  imkers/external/miniupnpc/upnpreplyparse.c
  imkers/src/Common/Base58.cpp
  imkers/src/Common/CommandLine.cpp
  imkers/src/Common/Util.cpp
  imkers/src/Common/StringTools.cpp
  imkers/src/Common/JsonValue.cpp
  imkers/src/Common/ConsoleTools.cpp
  imkers/src/Common/MemoryInputStream.cpp
  imkers/src/Common/PathTools.cpp
  imkers/src/Common/DnsTools.cpp
  imkers/src/Common/StdInputStream.cpp
  imkers/src/Common/StdOutputStream.cpp
  imkers/src/Common/StreamTools.cpp
  imkers/src/Common/StringOutputStream.cpp
  imkers/src/Common/StringView.cpp
  imkers/src/Common/VectorOutputStream.cpp
  imkers/src/crypto/blake256.c
  imkers/src/crypto/chacha8.c
  imkers/src/crypto/crypto-ops-data.c
  imkers/src/crypto/crypto-ops.c
  imkers/src/crypto/crypto.cpp
  imkers/src/crypto/groestl.c
  imkers/src/crypto/hash.c
  imkers/src/crypto/jh.c
  imkers/src/crypto/keccak.c
  imkers/src/crypto/random.c
  imkers/src/crypto/skein.c
  imkers/src/crypto/cn_slow_hash_soft.cpp
  imkers/src/crypto/cn_slow_hash_hard_intel.cpp
  imkers/src/crypto/cn_slow_hash_hard_arm.cpp
  imkers/src/crypto/tree-hash.c
  imkers/src/CryptoNoteCore/BlockchainIndices.cpp
  imkers/src/CryptoNoteCore/BlockchainMessages.cpp
  imkers/src/CryptoNoteCore/BlockIndex.cpp
  imkers/src/CryptoNoteCore/CoreConfig.cpp
  imkers/src/CryptoNoteCore/CryptoNoteBasic.cpp
  imkers/src/CryptoNoteCore/CryptoNoteTools.cpp
  imkers/src/CryptoNoteCore/Currency.cpp
  imkers/src/CryptoNoteCore/MinerConfig.cpp
  imkers/src/CryptoNoteCore/Transaction.cpp
  imkers/src/CryptoNoteCore/Account.cpp
  imkers/src/CryptoNoteCore/Blockchain.cpp
  imkers/src/CryptoNoteCore/Checkpoints.cpp
  imkers/src/CryptoNoteCore/CryptoNoteBasicImpl.cpp
  imkers/src/CryptoNoteCore/Core.cpp
  imkers/src/CryptoNoteCore/CryptoNoteFormatUtils.cpp
  imkers/src/CryptoNoteCore/CryptoNoteSerialization.cpp
  imkers/src/CryptoNoteCore/Difficulty.cpp
  imkers/src/CryptoNoteCore/IBlock.cpp
  imkers/src/CryptoNoteCore/Miner.cpp
  imkers/src/CryptoNoteCore/TransactionExtra.cpp
  imkers/src/CryptoNoteCore/TransactionPool.cpp
  imkers/src/CryptoNoteCore/TransactionPrefixImpl.cpp
  imkers/src/CryptoNoteCore/TransactionUtils.cpp
  imkers/src/InProcessNode/InProcessNode.cpp
  imkers/src/InProcessNode/InProcessNodeErrors.cpp
  imkers/src/NodeRpcProxy/NodeErrors.cpp
  imkers/src/NodeRpcProxy/NodeRpcProxy.cpp
  imkers/src/P2p/NetNodeConfig.cpp
  imkers/src/Serialization/BinaryInputStreamSerializer.cpp
  imkers/src/Serialization/BinaryOutputStreamSerializer.cpp
  imkers/src/Serialization/JsonInputValueSerializer.cpp
  imkers/src/Serialization/JsonOutputStreamSerializer.cpp
  imkers/src/Serialization/KVBinaryInputStreamSerializer.cpp
  imkers/src/Serialization/KVBinaryOutputStreamSerializer.cpp
  imkers/src/Serialization/SerializationOverloads.cpp
  imkers/src/Transfers/BlockchainSynchronizer.cpp
  imkers/src/Transfers/SynchronizationState.cpp
  imkers/src/Transfers/TransfersConsumer.cpp
  imkers/src/Transfers/TransfersContainer.cpp
  imkers/src/Transfers/TransfersSubscription.cpp
  imkers/src/Transfers/TransfersSynchronizer.cpp
  imkers/src/Wallet/LegacyKeysImporter.cpp
  imkers/src/Wallet/WalletAsyncContextCounter.cpp
  imkers/src/Wallet/WalletErrors.cpp
  imkers/src/Wallet/WalletSerializationV1.cpp
  imkers/src/Wallet/WalletSerializationV2.cpp
  imkers/src/Wallet/WalletUtils.cpp
  imkers/src/WalletLegacy/KeysStorage.cpp
  imkers/src/WalletLegacy/WalletLegacy.cpp
  imkers/src/WalletLegacy/WalletHelper.cpp
  imkers/src/WalletLegacy/WalletLegacySerializer.cpp
  imkers/src/WalletLegacy/WalletLegacySerialization.cpp
  imkers/src/WalletLegacy/WalletTransactionSender.cpp
  imkers/src/WalletLegacy/WalletUnconfirmedTransactions.cpp
  imkers/src/WalletLegacy/WalletUserTransactionsCache.cpp
  imkers/src/System/ContextGroup.cpp
  imkers/src/System/Event.cpp
  imkers/src/System/EventLock.cpp
  imkers/src/System/InterruptedException.cpp
  imkers/src/System/Ipv4Address.cpp
  imkers/src/System/TcpStream.cpp
  imkers/src/HTTP/HttpRequest.cpp
  imkers/src/HTTP/HttpParser.cpp
  imkers/src/HTTP/HttpParserErrorCodes.cpp
  imkers/src/HTTP/HttpResponse.cpp
  imkers/src/Rpc/HttpClient.cpp
  imkers/src/Rpc/JsonRpc.cpp
  imkers/src/P2p/NetNode.cpp
  imkers/src/P2p/LevinProtocol.cpp
  imkers/src/P2p/NetNodeConfig.cpp
  imkers/src/P2p/PeerListManager.cpp
  imkers/src/CryptoNoteProtocol/CryptoNoteProtocolHandler.cpp
  imkers/src/Logging/ILogger.cpp
  imkers/src/Logging/LoggerGroup.cpp
  imkers/src/Logging/CommonLogger.cpp
  imkers/src/Logging/LoggerManager.cpp
  imkers/src/Logging/FileLogger.cpp
  imkers/src/Logging/StreamLogger.cpp
  imkers/src/Logging/ConsoleLogger.cpp
  imkers/src/Logging/LoggerMessage.cpp
  imkers/src/Logging/LoggerRef.cpp
  imkers/src/BlockchainExplorer/BlockchainExplorer.cpp
  imkers/src/BlockchainExplorer/BlockchainExplorerDataBuilder.cpp
  imkers/src/BlockchainExplorer/BlockchainExplorerErrors.cpp
)

file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
file(GLOB_RECURSE HEADERS src/*.hpp)
file(GLOB_RECURSE FORMS src/gui/ui/*.ui)

set(QRC src/resources.qrc)

qt5_wrap_ui(UIS ${FORMS})
qt5_add_resources(RCC ${QRC})


if (WIN32)
  if (NOT MSVC)
	message(FATAL_ERROR "Only MSVC is supported on this platform")
  endif ()
  add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600 /DSTATICLIB)
  include_directories(imkers/src/platform/msc)

  set(PLATFORM_DIR Windows)
  set(BUILD_PLATFORM WIN32)
  set(BUILD_RESOURCES src/cryptonotewallet.rc)
  set(QTMAIN Qt5::WinMain)

elseif (UNIX)
  set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} imkers/external/miniupnpc/minissdpc.c)
  if (APPLE)
    enable_language(ASM)
    file(GLOB_RECURSE OBJC_SOURCES src/*.mm)
    set(SOURCES ${SOURCES} ${OBJC_SOURCES})
    set(PLATFORM_DIR OSX)
    set(MACOSX_BUNDLE_INFO_STRING "${CN_VERSION}")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_NAME "Imkoin")
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_VERSION "$${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")

    find_package(Qt5PrintSupport REQUIRED)

    include_directories(/usr/include/malloc)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11 -stdlib=libc++")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11 -maes -D_DARWIN_C_SOURCE")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Carbon -framework IOKit -L/usr/lib")

    set(MACOSX_BUNDLE_ICON_FILE niobio.icns)
    set(APPLICATION_ICON src/images/niobio.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set(BUILD_PLATFORM MACOSX_BUNDLE)
    set(BUILD_RESOURCES ${APPLICATION_ICON})

    GET_TARGET_PROPERTY(QT_LIB_DIR "${Qt5Widgets_LIBRARIES}" LOCATION)
    GET_FILENAME_COMPONENT(QT_LIB_DIR "${QT_LIB_DIR}" PATH)
  else(APPLE)
    set(PLATFORM_DIR Linux)
    list(REMOVE_ITEM HEADERS ${CMAKE_SOURCE_DIR}/src/gui/macdockiconhandler.h)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes -std=c11")
  endif (APPLE)
endif ()

include_directories(imkers/src/Platform/${PLATFORM_DIR})
include_directories(imkers/src/Platform/Posix)
file(GLOB PLATFORM_SOURCES imkers/src/Platform/${PLATFORM_DIR}/System/*)
set(CRYPTONOTE_SOURCES ${CRYPTONOTE_SOURCES} ${PLATFORM_SOURCES})

add_library(${CRYPTONOTE_LIB} STATIC ${CRYPTONOTE_SOURCES})
set_target_properties(${CRYPTONOTE_LIB} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)

if (APPLE OR ${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
target_link_libraries(${CRYPTONOTE_LIB} ${Boost_LIBRARIES} -lresolv)
else ()
target_link_libraries(${CRYPTONOTE_LIB} ${Boost_LIBRARIES})
endif ()

#add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC} ${QM} ${TRANSLATIONS})
add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${QTMAIN} ${CRYPTONOTE_LIB} ${QRENCODE_LIB})

if (APPLE)
  qt5_use_modules(${PROJECT_NAME} PrintSupport)
elseif (UNIX)
  target_link_libraries(${PROJECT_NAME} -lpthread)
elseif (WIN32)
  target_link_libraries(${PROJECT_NAME} Imm32 Iphlpapi Winmm)
endif (APPLE)

qt5_use_modules(${PROJECT_NAME} Widgets Gui Network)

# Installation

set(CPACK_PACKAGE_NAME ${CN_PROJECT_NAME})
set(CPACK_PACKAGE_VERSION ${CN_VERSION})
set(CPACK_PACKAGE_VENDOR "Niobio-project")
set(CPACK_PACKAGE_CONTACT "http://niobiocash.org")
set(CPACK_STRIP_FILES ON)
if (APPLE)
    set(CPACK_GENERATOR DragNDrop)
    install(TARGETS ${PROJECT_NAME} BUNDLE DESTINATION .)
    set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${VERSION_RELEASE}")
elseif (UNIX)
    find_program(RPMBUILD rpmbuild)

    install(PROGRAMS build/${PROJECT_NAME} DESTINATION bin
    PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
    install(FILES src/niobiocashwallet.desktop DESTINATION share/applications)
    install(FILES src/images/niobiocash.png DESTINATION share/pixmaps RENAME niobiocash.png)
    install(FILES copyright DESTINATION share/doc/niobiowallet)

    if (NOT RPMBUILD)
        set(CPACK_GENERATOR DEB)
        set(CPACK_DEBIAN_PACKAGE_NAME ${CPACK_PACKAGE_NAME})
        set(CPACK_SYSTEM_NAME 64-bit)
        set(CPACK_DEBIAN_PACKAGE_MAINTAINER "${CPACK_PACKAGE_VENDOR} <contato@niobiocash.com>")
        set(CPACK_DEBIAN_PACKAGE_SECTION Office)
        set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)
        set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "Niobio Cash wallet
 Niobio Cash is a Brazilian decentralized, privacy oriented peer-to-peer
 cryptocurrency. It is open-source, nobody owns or controls Niobio Cash
 and everyone can take part of it.")
set(CPACK_PACKAGE_FILE_NAME "${CPACK_DEBIAN_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${VERSION_RELEASE}${CPACK_SYSTEM_NAME}")
    else()
        set(CPACK_GENERATOR RPM)
        set(CPACK_SYSTEM_NAME x86_64)
        set(CPACK_RPM_PACKAGE_RELEASE ${VERSION_RELEASE})
        set(CPACK_RPM_PACKAGE_LICENSE "MIT")
        set(CPACK_RPM_PACKAGE_GROUP Office)
        set(CPACK_RPM_PACKAGE_REQUIRES "qt5-qtbase >= 5.3.2, qt5-qtbase-gui >= 5.3.2")
        set(CPACK_RPM_PACKAGE_SUMMARY "Niobio Cash wallet")
        set(CPACK_RPM_PACKAGE_DESCRIPTION "Niobio Cash wallet
 Niobio Cash is a Brazilian decentralized, privacy oriented peer-to-peer
 cryptocurrency. It is open-source, nobody owns or controls Niobio Cash
 and everyone can take part of it.")
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${VERSION_RELEASE}${CPACK_SYSTEM_NAME}")
    endif ()
elseif (WIN32)
  install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION .)
  set(CPACK_GENERATOR ZIP)
endif (APPLE)

include(CPack)
