add_definitions(-DHRPSYS_PACKAGE_VERSION=\"\\"${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}\\"\")

# hrpIo
add_library(hrpIo_samplebot SHARED iob_samplebot.cpp)
target_link_libraries(hrpIo_samplebot rt)

# RobotHardware
set(comp_source ${ROBOTHARDWARE_SOURCE}/robot.cpp ${ROBOTHARDWARE_SOURCE}/RobotHardware.cpp ${ROBOTHARDWARE_SOURCE}/RobotHardwareService_impl.cpp)
set(libs hrpIo_shm hrpModel-3.1 hrpCollision-3.1 hrpUtil-3.1 hrpsysBaseStub)

add_library(RobotHardware_samplebot SHARED ${comp_source})

target_link_libraries(RobotHardware_samplebot ${libs})
set_target_properties(RobotHardware_samplebot PROPERTIES PREFIX "")
set_target_properties(RobotHardware_samplebot PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set_target_properties(RobotHardware_samplebot PROPERTIES OUTPUT_NAME RobotHardware)

add_executable(RobotHardwareComp_samplebot ${ROBOTHARDWARE_SOURCE}/RobotHardwareComp.cpp ${comp_source})
target_link_libraries(RobotHardwareComp_samplebot ${libs} ${omniorb_LIBRARIES} ${omnidynamic_LIBRARIES} RTC coil)
set_target_properties(RobotHardwareComp_samplebot PROPERTIES OUTPUT_NAME RobotHardwareComp)

add_library(hrpEC_samplebot SHARED ${HRPEC_SOURCE}/hrpEC.cpp ${HRPEC_SOURCE}/hrpEC-common.cpp)
target_link_libraries(hrpEC_samplebot hrpIo_samplebot hrpsysBaseStub)
set_target_properties(hrpEC_samplebot PROPERTIES PREFIX "")
set_target_properties(hrpEC_samplebot PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set_target_properties(hrpEC_samplebot PROPERTIES OUTPUT_NAME hrpEC)
set_target_properties(hrpEC_samplebot PROPERTIES COMPILE_DEFINITIONS "OPENRTM_VERSION110")

## install
install(TARGETS hrpIo_samplebot
  LIBRARY DESTINATION lib CONFIGURATIONS Release Debug RelWithDebInfo
)
install(TARGETS RobotHardwareComp_samplebot RobotHardware_samplebot hrpEC_samplebot
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION} CONFIGURATIONS Release Debug RelWithDebInfo
  LIBRARY DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/lib CONFIGURATIONS Release Debug RelWithDebInfo
)
