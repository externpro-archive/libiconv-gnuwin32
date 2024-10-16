cmake_minimum_required(VERSION 3.28)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR}/.devcontainer/cmake)
include(preproject)
project(libiconv VERSION 1.9.2)
include(flags)
include(GNUInstallDirs)
set(XP_INSTALL_CMAKEDIR ${CMAKE_INSTALL_DATADIR}/cmake)
set(CMAKE_INSTALL_DEFAULT_COMPONENT_NAME devel)
string(JOIN "\n" EXT1
  "get_filename_component(PKG_ROOTDIR \${CMAKE_CURRENT_LIST_DIR}/../.. ABSOLUTE)"
  "get_filename_component(PKG_ROOTDIR \${PKG_ROOTDIR} ABSOLUTE) # remove relative parts"
  ""
  )
set(libName xpro::${PROJECT_NAME})
if(WIN32)
  set(libs
    lib/libcharset.lib
    lib/libiconv.lib
    )
  install(FILES ${libs} DESTINATION ${CMAKE_INSTALL_LIBDIR})
  string(JOIN "\n" EXT2
    "if(NOT TARGET ${libName})"
    "  add_library(${libName} STATIC IMPORTED)"
    "  set(libRelease \${PKG_ROOTDIR}/${CMAKE_INSTALL_LIBDIR}/libiconv${CMAKE_STATIC_LIBRARY_SUFFIX})"
    "  if(EXISTS \"\${libRelease}\")"
    "    set_property(TARGET ${libName} APPEND PROPERTY IMPORTED_CONFIGURATIONS RELEASE)"
    "    set_target_properties(${libName} PROPERTIES"
    "      IMPORTED_LINK_INTERFACE_LANGUAGES_RELEASE \"C\""
    "      IMPORTED_LOCATION_RELEASE \"\${libRelease}\""
    "      INTERFACE_INCLUDE_DIRECTORIES \"\${PKG_ROOTDIR}/${CMAKE_INSTALL_INCLUDEDIR}\""
    "      )"
    "  endif()"
    "endif()"
    ""
    )
else()
  string(JOIN "\n" EXT2
    "if(NOT TARGET ${libName})"
    "  add_library(${libName} INTERFACE IMPORTED)"
    "  set_target_properties(${libName} PROPERTIES"
    "    INTERFACE_INCLUDE_DIRECTORIES \"\${PKG_ROOTDIR}/${CMAKE_INSTALL_INCLUDEDIR}\""
    "    )"
    "endif()"
    ""
    )
endif()
xpPackageDevel()
file(GLOB hdrs include/*.h)
install(FILES ${hdrs} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
