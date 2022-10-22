cmake_minimum_required(VERSION 3.21)

project(anthy-unicode-cmake VERSION 0.0.0)

include(GNUInstallDirs)

set(CONF_DIR "${CMAKE_INSTALL_SYSCONFDIR}")
configure_file(
    "${PROJECT_SOURCE_DIR}/config.h.in"
    "${PROJECT_BINARY_DIR}/config.h"
)

include_directories("${PROJECT_BINARY_DIR}")

set(anthy_headers
    anthy-unicode/anthy/alloc.h
    anthy-unicode/anthy/corpus.h
    anthy-unicode/anthy/dicutil.h
    anthy-unicode/anthy/logger.h
    anthy-unicode/anthy/matrix.h
    anthy-unicode/anthy/ruleparser.h
    anthy-unicode/anthy/textdict.h
    anthy-unicode/anthy/xchar.h
    anthy-unicode/anthy/anthy.h
    anthy-unicode/anthy/depgraph.h
    anthy-unicode/anthy/feature_set.h
    anthy-unicode/anthy/Makefile
    anthy-unicode/anthy/ordering.h
    anthy-unicode/anthy/segclass.h
    anthy-unicode/anthy/texttrie.h
    anthy-unicode/anthy/xstr.h
    anthy-unicode/anthy/conf.h
    anthy-unicode/anthy/dic.h
    anthy-unicode/anthy/filemap.h
    anthy-unicode/anthy/prediction.h
    anthy-unicode/anthy/segment.h
    anthy-unicode/anthy/word_dic.h
    anthy-unicode/anthy/convdb.h
    anthy-unicode/anthy/diclib.h
    anthy-unicode/anthy/input.h
    anthy-unicode/anthy/record.h
    anthy-unicode/anthy/splitter.h
    anthy-unicode/anthy/wtype.h
)

set(diclib_SRC
    anthy-unicode/src-diclib/diclib.c
    anthy-unicode/src-diclib/file_dic.c
    anthy-unicode/src-diclib/filemap.c
    anthy-unicode/src-diclib/xstr.c
    anthy-unicode/src-diclib/xchar.c
    anthy-unicode/src-diclib/alloc.c
    anthy-unicode/src-diclib/conf.c
    anthy-unicode/src-diclib/logger.c
    anthy-unicode/src-diclib/ruleparser.c
)
# add_library(diclib SHARED "${diclib_SRC}")

add_compile_definitions(
    S_IREAD=S_IRUSR
    S_IWRITE=S_IWUSR
)
set(worddic_SRC
    anthy-unicode/src-worddic/word_dic.c 
    anthy-unicode/src-worddic/dic_util.c
    anthy-unicode/src-worddic/wtype.c
    anthy-unicode/src-worddic/texttrie.c
    anthy-unicode/src-worddic/textdict.c
    anthy-unicode/src-worddic/record.c
    anthy-unicode/src-worddic/word_lookup.c
    anthy-unicode/src-worddic/use_dic.c
    anthy-unicode/src-worddic/priv_dic.c
    anthy-unicode/src-worddic/mem_dic.c
    anthy-unicode/src-worddic/ext_ent.c
    anthy-unicode/src-worddic/matrix.c
    anthy-unicode/src-worddic/feature_set.c
)
add_library(anthydic-unicode SHARED "${diclib_SRC}" "${worddic_SRC}")
target_include_directories(anthydic-unicode PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/anthy-unicode>
    $<INSTALL_INTERFACE:include>
)

set(ordering_SRC
    anthy-unicode/src-ordering/candswap.c
    anthy-unicode/src-ordering/candsort.c
    anthy-unicode/src-ordering/commit.c
    anthy-unicode/src-ordering/relation.c
    anthy-unicode/src-ordering/infosort.c
    anthy-unicode/src-ordering/candhistory.c
)
# add_library(ordering SHARED "${ordering_SRC}")

set(splitter_SRC
    anthy-unicode/src-splitter/wordlist.c
    anthy-unicode/src-splitter/metaword.c
    anthy-unicode/src-splitter/depgraph.c
    anthy-unicode/src-splitter/splitter.c
    anthy-unicode/src-splitter/evalborder.c
    anthy-unicode/src-splitter/compose.c
    anthy-unicode/src-splitter/lattice.c
    anthy-unicode/src-splitter/segclass.c
)
# add_library(split SHARED "${splitter_SRC}")

set(main_SRC
    anthy-unicode/src-main/main.c
    anthy-unicode/src-main/context.c
)
add_library(anthy-unicode SHARED "${splitter_SRC}" "${ordering_SRC}" "${main_SRC}")
target_include_directories(anthy-unicode PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/anthy-unicode>
    $<INSTALL_INTERFACE:include>
)
target_link_libraries(anthy-unicode
    m
    anthydic-unicode
)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    AnthyConfigVersion.cmake
    VERSION "1.0.0"
    COMPATIBILITY SameMajorVersion
)

install(DIRECTORY anthy-unicode/anthy TYPE INCLUDE)
install(TARGETS anthy-unicode anthydic-unicode EXPORT AnthyTargets)
install(EXPORT AnthyTargets
        FILE AnthyTargets.cmake
        DESTINATION "lib/cmake"
        NAMESPACE Anthy::
)

configure_package_config_file(
    AnthyConfig.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/AnthyConfig.cmake"
    INSTALL_DESTINATION "lib/cmake"
)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/AnthyConfig.cmake"
              "${CMAKE_CURRENT_BINARY_DIR}/AnthyConfigVersion.cmake"
        DESTINATION "lib/cmake"
)
