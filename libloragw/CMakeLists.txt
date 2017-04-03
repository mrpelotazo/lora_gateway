cmake_minimum_required(VERSION 3.2)

project(libloragw)
include(GNUInstallDirs)

#-----------------------------------------------------------------------------
# libloragw version
#-----------------------------------------------------------------------------
set(LIBLORAGW_VERSION_MAJOR	4)
set(LIBLORAGW_VERSION_MINOR	1)
set(LIBLORAGW_VERSION_PATCH	3)
set(LIBLORAGW_VERSION	"${LIBLORAGW_VERSION_MAJOR}.${LIBLORAGW_VERSION_MINOR}.${LIBLORAGW_VERSION_PATCH}")

#-----------------------------------------------------------------------------
# libloragw configuration
#-----------------------------------------------------------------------------
set(DEBUG_AUX	"0" CACHE	STRING	"print aux debug output")
set(DEBUG_SPI	"0"	CACHE	STRING	"print spi debug output")
set(DEBUG_REG	"0"	CACHE	STRING	"print reg debug output")
set(DEBUG_HAL	"0"	CACHE	STRING	"print hal debug output")
set(DEBUG_GPS	"0"	CACHE	STRING	"print gps debug output")
set(DEBUG_GPIO	"0"	CACHE	STRING	"print gpio debug output")
set(DEBUG_LBT	"0"	CACHE	STRING	"print lbt debug output")

set_property(CACHE DEBUG_AUX PROPERTY STRINGS "0" "1")
set_property(CACHE DEBUG_SPI PROPERTY STRINGS "0" "1")
set_property(CACHE DEBUG_REG PROPERTY STRINGS "0" "1")
set_property(CACHE DEBUG_HAL PROPERTY STRINGS "0" "1")
set_property(CACHE DEBUG_GPS PROPERTY STRINGS "0" "1")
set_property(CACHE DEBUG_GPIO PROPERTY STRINGS "0" "1")
set_property(CACHE DEBUG_LBT PROPERTY STRINGS "0" "1")

configure_file(config.h.in config.h)

#-----------------------------------------------------------------------------
# libloragw sources and headers
#-----------------------------------------------------------------------------
file(GLOB LIB_SOURCES ${PROJECT_SOURCE_DIR}/src/*.c)
file(GLOB LIB_INTERFACE_HEADERS
	${PROJECT_SOURCE_DIR}/inc/loragw_hal.h
	${PROJECT_SOURCE_DIR}/inc/loragw_aux.h
	${PROJECT_SOURCE_DIR}/inc/loragw_reg.h
	${PROJECT_SOURCE_DIR}/inc/loragw_gps.h
)

#-----------------------------------------------------------------------------
# libloragw target
#-----------------------------------------------------------------------------
add_library(loragw ${LIB_SOURCES})

#-----------------------------------------------------------------------------
# libloragw includes
#-----------------------------------------------------------------------------
target_include_directories(loragw PRIVATE
	${PROJECT_BINARY_DIR}
	${PROJECT_SOURCE_DIR}/src
	${PROJECT_SOURCE_DIR}/inc
)

target_include_directories(loragw INTERFACE
		$<INSTALL_INTERFACE:include/semtech>
)

set_target_properties(loragw PROPERTIES VERSION ${LIBLORAGW_VERSION})

#-----------------------------------------------------------------------------
# install targets
#-----------------------------------------------------------------------------
install(TARGETS loragw EXPORT libloragw-config DESTINATION ${CMAKE_INSTALL_FULL_LIBDIR})
install(FILES ${LIB_INTERFACE_HEADERS} DESTINATION ${CMAKE_INSTALL_FULL_INCLUDEDIR}/semtech)
install(FILES ${PROJECT_BINARY_DIR}/config.h DESTINATION ${CMAKE_INSTALL_FULL_INCLUDEDIR}/semtech)
install(EXPORT libloragw-config DESTINATION ${CMAKE_INSTALL_FULL_LIBDIR}/cmake/libloragw)
export(TARGETS loragw FILE libloragw-config.cmake)
