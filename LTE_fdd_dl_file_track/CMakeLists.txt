# Source
include(GrPlatform)
add_library(LTE_fdd_dl_ft SHARED src/LTE_fdd_dl_ft_samp_buf.cc)
include_directories(hdr ${CMAKE_SOURCE_DIR}/libgraph/hdr ${CMAKE_SOURCE_DIR}/libtools/hdr ${CMAKE_SOURCE_DIR}/liblte/hdr ${CMAKE_SOURCE_DIR}/cmn_hdr ${CMAKE_SOURCE_DIR}/LTE_fdd_dl_file_track/hdr ${CMAKE_SOURCE_DIR}/LTE_fdd_dl_track/hdr)
target_link_libraries(LTE_fdd_dl_ft lte graph itpp fftw3f ${Boost_LIBRARIES} ${GNURADIO_RUNTIME_LIBRARIES} ${GNURADIO_PMT_LIBRARIES})
set_target_properties(LTE_fdd_dl_ft PROPERTIES DEFINE_SYMBOL "LTE_fdd_dl_ft_EXPORTS")
install(TARGETS LTE_fdd_dl_ft LIBRARY DESTINATION lib${LIB_SUFFIX} ARCHIVE DESTINATION lib${LIB_SUFFIX} RUNTIME DESTINATION bin)

# Swig
find_package(SWIG)
find_package(PythonLibs)
if(NOT SWIG_FOUND OR NOT PYTHONLIBS_FOUND)
    return()
endif()
include(GrSwig)
include(GrPython)
foreach(incdir ${GNURADIO_RUNTIME_INCLUDE_DIRS})
    list(APPEND GR_SWIG_INCLUDE_DIRS ${incdir}/gnuradio/swig)
endforeach(incdir)
foreach(incdir ${GNURADIO_OSMOSDR_INCLUDE_DIRS})
    list(APPEND GR_SWIG_INCLUDE_DIRS ${incdir}/osmosdr/swig)
endforeach(incdir)
set(GR_SWIG_LIBRARIES LTE_fdd_dl_ft)
GR_SWIG_MAKE(LTE_fdd_dl_ft src/LTE_fdd_dl_ft.i)
GR_SWIG_INSTALL(TARGETS LTE_fdd_dl_ft DESTINATION ${GR_PYTHON_DIR}/LTE_fdd_dl_ft)
install(FILES src/LTE_fdd_dl_ft.i DESTINATION ${GR_INCLUDE_DIR}/LTE_fdd_dl_ft/swig)

# Python
if(NOT PYTHONINTERP_FOUND)
    return()
endif()
GR_PYTHON_INSTALL(FILES src/__init__.py DESTINATION ${GR_PYTHON_DIR}/LTE_fdd_dl_ft)
GR_PYTHON_INSTALL(PROGRAMS python/LTE_fdd_dl_file_track.py DESTINATION bin)
