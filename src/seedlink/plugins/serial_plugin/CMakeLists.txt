SET(GCF_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../libgcf2-2.3.2)
SET(GCF_INC ${GCF_DIR}/src/gcf2.h)

SET(SERIALPLUGIN_SOURCES
	serial_plugin.cc
	rw2300.c
	linux2300.c
	proto_edata.cc
	proto_edata2.cc
	proto_guralp2.cc
	proto_dr24.cc
	proto_prema.cc
	proto_hrd24.cc
	proto_sadc.cc
	proto_ws2300.cc
	proto_mws.cc
	proto_solar.cc
)

INCLUDE_DIRECTORIES(../../libs/plugin)
INCLUDE_DIRECTORIES(../../libs/3rd-party/qlib2)
INCLUDE_DIRECTORIES(../libgcf2-2.3.2/src)

SET(CONFIG_FILE ${SC3_PACKAGE_INSTALL_PREFIX}/acquisition/config/plugins.ini)

SET(OPTIONS -DSYSLOG_FACILITY=LOG_LOCAL0 -DCONFIG_FILE=\\\"${CONFIG_FILE}\\\")
SET(WSOPTIONS -DVERSION=\\\"1.10\\\")

ADD_DEFINITIONS(${OPTIONS} ${WSOPTIONS})

ADD_EXECUTABLE(serial_plugin ${SERIALPLUGIN_SOURCES})

SET_SOURCE_FILES_PROPERTIES(
	proto_guralp2.cc
	PROPERTIES OBJECT_DEPENDS ${GCF_INC}
)

SET_DIRECTORY_PROPERTIES(
	PROPERTIES
	ADDITIONAL_MAKE_CLEAN_FILES
	"${GCF_INC}"
)

ADD_CUSTOM_COMMAND(
	OUTPUT ${GCF_INC}
	COMMAND ./configure
	COMMAND ${CMAKE_MAKE_PROGRAM}
	WORKING_DIRECTORY ${GCF_DIR}
)


TARGET_LINK_LIBRARIES(
	serial_plugin
		${GCF_DIR}/src/libgcf2.a
		qlib2
		slplugin
		slutils
)

INSTALL(TARGETS serial_plugin RUNTIME DESTINATION ${SEEDLINK_PLUGIN_OUTPUT_DIR})
