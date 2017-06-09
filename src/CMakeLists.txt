#
# leptonica
#

################################################################################

########################################
# SHARED LIBRARY leptonica
########################################

file(GLOB hdr "*.h")
file(GLOB src "*.c")
if (MSVC)
    set_source_files_properties(${src} PROPERTIES LANGUAGE CXX)
endif()

add_library                     (leptonica ${src} ${hdr})
set_target_properties           (leptonica PROPERTIES VERSION   ${VERSION_PLAIN})
set_target_properties           (leptonica PROPERTIES SOVERSION 5.1.0)
if (WIN32)
set_target_properties           (leptonica PROPERTIES OUTPUT_NAME       leptonica-${VERSION_PLAIN})
set_target_properties           (leptonica PROPERTIES DEBUG_OUTPUT_NAME leptonica-${VERSION_PLAIN}d)
endif()

if (BUILD_SHARED_LIBS)
    target_compile_definitions  (leptonica PRIVATE -DLIBLEPT_EXPORTS)
endif()

target_include_directories(
    leptonica
    INTERFACE
    "$<INSTALL_INTERFACE:include/leptonica>"
)

if (GIF_LIBRARY)
    target_link_libraries       (leptonica PUBLIC ${GIF_LIBRARY})
endif()
if (JP2K_FOUND)
    target_link_libraries       (leptonica PUBLIC ${JP2K_LIBRARIES})
endif()
if (WEBP_FOUND)
    target_link_libraries       (leptonica PUBLIC ${WEBP_LIBRARIES})
endif()
target_link_libraries           (leptonica PUBLIC JPEG::jpeg PNG::png TIFF::libtiff ZLIB::zlib)

if (UNIX)
    target_link_libraries       (leptonica PUBLIC m)
endif()

if (NOT CPPAN_BUILD)
#    export(TARGETS leptonica FILE ${CMAKE_BINARY_DIR}/LeptonicaTargets.cmake)
else()
    target_link_libraries       (leptonica
        pvt.cppan.demo.gif
        pvt.cppan.demo.jpeg
        pvt.cppan.demo.png
        pvt.cppan.demo.tiff
        pvt.cppan.demo.webp
        pvt.cppan.demo.openjpeg.openjp2
    )
    file(WRITE ${CMAKE_BINARY_DIR}/LeptonicaTargets.cmake "include(${CMAKE_BINARY_DIR}/cppan.cmake)\n")
    export(TARGETS leptonica APPEND FILE ${CMAKE_BINARY_DIR}/LeptonicaTargets.cmake)
endif()

install(TARGETS leptonica EXPORT LeptonicaTargets RUNTIME DESTINATION bin LIBRARY DESTINATION lib ARCHIVE DESTINATION lib INCLUDES DESTINATION include)
install(EXPORT LeptonicaTargets NAMESPACE "Leptonica::" DESTINATION lib/cmake/${PROJECT_NAME})
install(FILES
    ${hdr}
    ${CMAKE_BINARY_DIR}/src/endianness.h
    DESTINATION include/leptonica)

################################################################################
