cmake_minimum_required(VERSION 2.8)

SET(EXECUTABLE_NAME "DD-Evaluator")

FILE(GLOB LOCAL_HEADERS *.h*)
FILE(GLOB LOCAL_SOURCES *.cpp)

INCLUDE_DIRECTORIES(../../include)

LIST(APPEND DD_SUBLIBRARY_NAMES "flow")

SET(ADDITIONAL_LIBS "")

foreach(SUBLIB_NAME ${DD_SUBLIBRARY_NAMES})
	string(TOUPPER "${SUBLIB_NAME}" UPPERCASE_NAME)
	INCLUDE_DIRECTORIES(../../include/${SUBLIB_NAME})
endforeach(SUBLIB_NAME) 

LIST(APPEND ADDITIONAL_LIBS ${OpenCV_LIBRARIES})

MESSAGE(STATUS "OpenCV_LIBRARIES = ${OpenCV_LIBRARIES}")

IF(USE_BOOST)
	LIST(APPEND ADDITIONAL_LIBS ${Boost_LIBRARIES})
ENDIF()

LIST(APPEND ADDITIONAL_LIBS thermalvis)

foreach(SUBLIB_NAME ${CALIB_SUBLIBRARY_NAMES})
	LIST(APPEND ADDITIONAL_LIBS thermalvis-${SUBLIB_NAME})
endforeach(SUBLIB_NAME) 

ADD_EXECUTABLE( ${EXECUTABLE_NAME} 
	dd_evaluator.cpp dd_evaluator.hpp
	evaluation.cpp evaluation.hpp
)	

TARGET_LINK_LIBRARIES( 
	${EXECUTABLE_NAME} 
	${ADDITIONAL_LIBS}
)

foreach(EXTERNAL_LIB ${EXTERNAL_LIBRARIES}) 
	string(TOUPPER ${EXTERNAL_LIB} UPPERCASE_NAME)
	IF(USE_${UPPERCASE_NAME} AND ${EXTERNAL_LIB}_FOUND)
		SET(LINKING_FILE "${CMAKE_CURRENT_SOURCE_DIR}/../../cmake/link/${EXTERNAL_LIB}.cmake")
		IF(EXISTS "${LINKING_FILE}")
			include(${LINKING_FILE})
		ENDIF()
	ENDIF()
endforeach(EXTERNAL_LIB)
