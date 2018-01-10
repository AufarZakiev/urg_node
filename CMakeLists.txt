cmake_minimum_required(VERSION 2.8.3)
project(udp_urg_node)

find_package(catkin REQUIRED COMPONENTS
        diagnostic_updater dynamic_reconfigure laser_proc message_generation
        nodelet rosconsole roscpp sensor_msgs std_msgs std_srvs tf urg_c
        )

find_package(Boost REQUIRED COMPONENTS system)
find_package(Threads)

set_directory_properties(PROPERTIES COMPILE_OPTIONS "-std=c++11")

# Dynamic reconfigure support
generate_dynamic_reconfigure_options(cfg/URG.cfg)

add_message_files(FILES
        Status.msg
        )

generate_messages(
        DEPENDENCIES std_msgs
)

catkin_package(
        INCLUDE_DIRS include
        LIBRARIES udp_urg_c_wrapper udp_urg_node_driver
        CATKIN_DEPENDS dynamic_reconfigure laser_proc message_runtime nodelet rosconsole
        roscpp sensor_msgs std_msgs std_srvs urg_c
        DEPENDS
)


include_directories(include ${catkin_INCLUDE_DIRS}
        ${Boost_INCLUDE_DIRS})

link_directories(${Boost_LIBRARY_DIRS})

## Declare a cpp library
add_library(udp_urg_c_wrapper src/urg_c_wrapper.cpp)
target_link_libraries(udp_urg_c_wrapper ${catkin_LIBRARIES})

# Declare the cpp as a lib also
add_library(udp_urg_node_driver src/urg_node_driver.cpp)
add_dependencies(udp_urg_node_driver ${PROJECT_NAME}_gencfg ${PROJECT_NAME}_gencpp)
target_link_libraries(udp_urg_node_driver udp_urg_c_wrapper ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})

## Declare a cpp executable
add_executable(udp_urg_node src/urg_node.cpp)
target_link_libraries(udp_urg_node udp_urg_node_driver ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})

## Declare getID tool
add_executable(udp_getID src/getID.cpp)
target_link_libraries(udp_getID udp_urg_c_wrapper ${catkin_LIBRARIES})

## Mark executables and/or libraries for installation
install(TARGETS udp_urg_c_wrapper udp_urg_node udp_urg_node_driver udp_getID
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
        )

install(
        DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h"
        PATTERN ".svn" EXCLUDE
        )

## Install script to set IP addresses
install(PROGRAMS
        scripts/set_urg_ip.py
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
        )

if (CATKIN_ENABLE_TESTING)
    find_package(roslint)
    # Ignore long -> int32_t warning, urg_c driver uses
    # long, etc. Wrapper must also use these
    set(ROSLINT_CPP_OPTS "--filter=-runtime/references,-runtime/int")
    roslint_cpp()
    roslint_add_test()

    find_package(roslaunch REQUIRED)
    roslaunch_add_file_check(launch/udp_urg_lidar.launch)
endif ()
