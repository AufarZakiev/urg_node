cmake_minimum_required(VERSION 2.8.3)
project(urg_library_wrapper)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS roscpp sensor_msgs urg_library rosconsole nodelet dynamic_reconfigure tf)

# Dynamic reconfigure support
generate_dynamic_reconfigure_options(cfg/URG.cfg)

###################################################
## Declare things to be passed to other projects ##
###################################################

## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES urg_library_wrapper
  CATKIN_DEPENDS roscpp sensor_msgs urg_library rosconsole nodelet dynamic_reconfigure
  DEPENDS
)

###########
## Build ##
###########

## Specify additional locations of header files
include_directories(include ${catkin_INCLUDE_DIRS})

## Declare a cpp library
add_library(urg_library_wrapper src/urg_library_wrapper.cpp)
target_link_libraries(urg_library_wrapper ${catkin_LIBRARIES})

## Declare a cpp executable
add_executable(urg_node src/urg_node.cpp)
add_dependencies(urg_node ${PROJECT_NAME}_gencfg)
target_link_libraries(urg_node urg_library_wrapper ${catkin_LIBRARIES})

## Add dependencies to the executable
# add_dependencies(urg_library_wrapper_node ${PROJECT_NAME})

## Specify libraries to link a library or executable target against
# target_link_libraries(urg_library_wrapper_node
#   ${catkin_LIBRARIES}
# )

#############
## Install ##
#############

## Mark executable scripts (Python etc.) for installation
## not required for python when using catkin_python_setup()
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS urg_library_wrapper urg_library_wrapper_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_urg_library_wrapper.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)