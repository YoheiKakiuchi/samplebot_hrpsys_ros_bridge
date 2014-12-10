cmake_minimum_required(VERSION 2.8.3)
project(samplebot_hrpsys_ros_bridge)

find_package(catkin REQUIRED COMPONENTS hrpsys_ros_bridge)

catkin_package(
  DEPENDS
  CATKIN_DEPENDS hrpsys_ros_bridge
  INCLUDE_DIRS
  LIBRARIES
  )

if(NOT EXISTS ${hrpsys_ros_bridge_SOURCE_DIR}) # for installed package
  set(hrpsys_ros_bridge_SOURCE_DIR ${hrpsys_ros_bridge_PREFIX}/share/hrpsys_ros_bridge)
endif()

# set CMAKE_BUILD_TYPE
if(NOT CMAKE_BUILD_TYPE)
  set(
    CMAKE_BUILD_TYPE Release CACHE STRING
    "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
    FORCE)
endif()

## Build for iob
find_package(PkgConfig)
pkg_check_modules(omniorb omniORB4 REQUIRED)
pkg_check_modules(omnidynamic omniDynamic4 REQUIRED)
pkg_check_modules(openrtm_aist openrtm-aist REQUIRED)
pkg_check_modules(openhrp3 openhrp3.1 REQUIRED)
pkg_check_modules(hrpsys hrpsys-base REQUIRED)

if(EXISTS ${hrpsys_SOURCE_DIR})
  set(ROBOTHARDWARE_SOURCE ${hrpsys_SOURCE_DIR}/src/rtc/RobotHardware)
  set(HRPEC_SOURCE         ${hrpsys_SOURCE_DIR}/src/ec/hrpEC)
else()
  set(ROBOTHARDWARE_SOURCE ${hrpsys_PREFIX}/share/hrpsys/src/rtc/RobotHardware)
  set(HRPEC_SOURCE         ${hrpsys_PREFIX}/share/hrpsys/src/ec/hrpEC)
endif()

include_directories(${catkin_INCLUDE_DIRS} ${openrtm_aist_INCLUDE_DIRS} ${openhrp3_INCLUDE_DIRS} ${hrpsys_INCLUDE_DIRS})

link_directories(${CATKIN_DEVEL_PREFIX}/lib ${hrpsys_PREFIX}/lib ${openhrp3_LIBRARY_DIRS})

add_subdirectory(iob)

## for robot model
compile_openhrp_model (
  "${PROJECT_SOURCE_DIR}/models/SAMPLEBOTmain.wrl"
  SAMPLEBOT
  --robothardware-conf-file-option "pdgains.file_name: ${PROJECT_SOURCE_DIR}/models/PDgains.sav"
  --conf-dt-option "0.002"
  --simulation-timestep-option "0.002"
  )

## for launch
generate_default_launch_eusinterface_files (
  "${PROJECT_SOURCE_DIR}/models/SAMPLEBOTmain.wrl"
  samplebot_hrpsys_ros_bridge
  SAMPLEBOT
  "--use-unstable-hrpsys-config"
  )
