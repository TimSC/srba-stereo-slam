PROJECT(srba-stereo-slam)

FIND_PACKAGE(SRBA REQUIRED)
INCLUDE_DIRECTORIES(${SRBA_INCLUDE_DIRS})

FIND_PACKAGE(MRPT REQUIRED ${SRBA_REQUIRED_MRPT_MODULES} obs gui opengl vision hwdrivers graphslam)
FIND_PACKAGE(OpenCV REQUIRED)

option(SRBA_DIR "Path to SRBA" "")
MESSAGE( STATUS "SRBA_DIR:    " ${SRBA_DIR} )

option(VO_SRC "Path to VO project dir" "")
MESSAGE( STATUS "VO_SRC:    " ${VO_SRC} )

option(VO_DIR "Path to VO build dir" "")
MESSAGE( STATUS "VO_DIR:    " ${VO_DIR} )

option(BOW2_DIR "Path to BoW2 project dir" "")
MESSAGE( STATUS "BOW2_DIR:    " ${BOW2_DIR} )

option(BOW2_BUILD "Path to BoW2 build dir" "")
MESSAGE( STATUS "BOW2_BUILD:    " ${BOW2_BUILD} )

option(BOOST_INC_DIR "Path to boost includes" "")
MESSAGE( STATUS "BOOST_INC_DIR:    " ${BOOST_INC_DIR} )

SET( VO_LIB_DIR 
		${VO_DIR}/lib )
SET( VO_INC_DIR 
		${VO_SRC}/libstereo-odometry/include )

SET( BOW2_LIB_RELEASE_DIR ${BOW2_BUILD} )
SET( BOW2_LIB_DEBUG_DIR ${BOW2_BUILD} )

IF(UNIX)
	SET( VO_LIBS_RELEASE ${VO_LIB_DIR}/libstereo-odometry.so )
	SET( VO_LIBS_DEBUG ${VO_LIB_DIR}/libstereo-odometry.so )
ELSE(UNIX)
	SET( VO_LIBS_RELEASE ${VO_LIB_DIR}/Release/stereo-odometry.lib )
	SET( VO_LIBS_DEBUG ${VO_LIB_DIR}/Debug/stereo-odometry.lib )
ENDIF(UNIX)

INCLUDE_DIRECTORIES( 
		${VO_INC_DIR} 
		${BOOST_INC_DIR}
		${BOW2_DIR}/include/DBoW2 
		${BOW2_BUILD}/dependencies/src/DLib/include )

# Declare the target (an executable)
ADD_EXECUTABLE( srba-stereo-slam
	srba-stereo-slam_common.h	
	CStereoSLAMKF.cpp 				CStereoSLAMKF.h
	CSRBAStereoSLAMEstimator.cpp	CSRBAStereoSLAMEstimator.h
	CBoWManager.h
	srba-stereo-slam_utils.cpp		srba-stereo-slam_utils.h
	srba-stereo-slam_main.cpp		srba-stereo-slam.h )

# NOTE: It seems crucial the order of the linking libs!! First, our lib, then MRPT ones
IF(UNIX)
	TARGET_LINK_LIBRARIES( srba-stereo-slam 
		optimized ${VO_LIBS_RELEASE} 
		optimized ${BOW2_LIB_RELEASE_DIR}/libDUtils.so 
		optimized ${BOW2_LIB_RELEASE_DIR}/libDUtilsCV.so 
		optimized ${BOW2_LIB_RELEASE_DIR}/libDVision.so 
		optimized ${BOW2_LIB_RELEASE_DIR}/libDBoW2.so
		debug ${VO_LIBS_DEBUG} 
		debug ${BOW2_LIB_DEBUG_DIR}/libDUtils.so 
		debug ${BOW2_LIB_DEBUG_DIR}/libDUtilsCV.so 
		debug ${BOW2_LIB_DEBUG_DIR}/libDVision.so 
		debug ${BOW2_LIB_DEBUG_DIR}/libDBoW2.so 
		${MRPT_LIBS}
		${OpenCV_LIBS} )
ELSE(UNIX)
	TARGET_LINK_LIBRARIES( srba-stereo-slam 
		debug ${VO_LIBS_DEBUG} 
		debug ${BOW2_LIB_DEBUG_DIR}/Debug/libDUtils.lib 
		debug ${BOW2_LIB_DEBUG_DIR}/Debug/libDUtilsCV.lib 
		debug ${BOW2_LIB_DEBUG_DIR}/Debug/libDVision.lib 
		debug ${BOW2_LIB_DEBUG_DIR}/Debug/libDBoW2.lib 
		optimized ${VO_LIBS_RELEASE} 
		optimized ${BOW2_LIB_RELEASE_DIR}/Release/libDUtils.lib 
		optimized ${BOW2_LIB_RELEASE_DIR}/Release/libDUtilsCV.lib 
		optimized ${BOW2_LIB_RELEASE_DIR}/Release/libDVision.lib 
		optimized ${BOW2_LIB_RELEASE_DIR}/Release/libDBoW2.lib
		${MRPT_LIBS}
		${OpenCV_LIBS} )
ENDIF(UNIX)

# Add user supplied extra options (optimization, etc...)
IF(MSVC)
	# For MSVC to avoid the C1128 error about too large object files:
	SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /bigobj")
	SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /bigobj")
ENDIF(MSVC)

# Set optimized building:
IF(CMAKE_COMPILER_IS_GNUCXX AND NOT CMAKE_BUILD_TYPE MATCHES "Debug")
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -mtune=native")
ENDIF(CMAKE_COMPILER_IS_GNUCXX AND NOT CMAKE_BUILD_TYPE MATCHES "Debug")

