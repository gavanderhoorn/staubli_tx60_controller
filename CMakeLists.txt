cmake_minimum_required(VERSION 2.4.6)
project(staubli_tx60_controller)
set(CMAKE_VERBOSE_MAKEFILE ON)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS std_msgs actionlib_msgs message_generation genmsg roscpp actionlib)

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)

include_directories(
    ./srv
    ./src
    ./action
)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

### Generate messages in the 'msg' folder
 add_message_files(
   FILES
   JointTrajectoryPoint.msg
   StaubliMovementParams.msg
 )

## Generate services in the 'srv' folder
 add_service_files(
   FILES
   FwdKinematics.srv
   GetCartesian.srv
   GetJoints.srv
   GetRotMat.srv
   InvKinematics.srv
   ResetMotion.srv
   SetTrajectoryParams.srv
 )

## Generate actions in the 'action' folder
 add_action_files(
   FILES
   SetCartesian.action
   SetJoints.action
   SetJointTrajectory.action
 )

## Generate added messages and services with any dependencies listed here
 generate_messages(
   DEPENDENCIES
   std_msgs
   actionlib_msgs
 )

catkin_package(
  INCLUDE_DIRS include
#  LIBRARIES test
   CATKIN_DEPENDS message_runtime actionlib_msgs
#  DEPENDS  
)

add_subdirectory(./staubli_tx60_api)


SET(STAUBLI_TX60_SOURCE
    src/staubli_tx60_server.cpp
    src/servicesManager.cpp
    src/staubliJointStatePublisher.cpp
    src/action_managers/setCartesianActionManager.cpp
    src/action_managers/setJointTrajectoryActionManager.cpp
    src/action_managers/setGenericJointTrajectoryActionManager.cpp
    src/action_managers/setJointsActionManager.cpp

)

SET(STAUBLI_TX60_HEADERS
    include/servicesManager.h
    include/staubliJointStatePublisher.h
    include/action_managers/setCartesianActionManager.h
    include/action_managers/setJointTrajectoryActionManager.h
    include/action_managers/setGenericJointTrajectoryActionManager.h
    include/action_managers/setJointsActionManager.h
    include/action_managers/staubliControlActionManager.h
    include/action_managers/staubliControlActionManager.hpp
    include/action_managers/staubliActionManager.h
    include/staubliState.h
)

SET(STAUBLI_LIBS
    tx60l
    soapC
    Server0
    Server1
    Server3
    stdsoap2
)


## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include staubli_tx60_api/include ${catkin_INCLUDE_DIRS})

add_executable(staubli_tx60_server src/staubli_tx60_server.cpp ${STAUBLI_TX60_SOURCE} ${STAUBLI_TX60_HEADERS})
target_link_libraries(staubli_tx60_server ${catkin_LIBRARIES})
add_dependencies(staubli_tx60_server staubli_tx60_generate_messages_cpp)

add_executable(testing_client test/testing_client.cpp)
target_link_libraries(testing_client ${catkin_LIBRARIES})
add_dependencies(testing_client staubli_tx60_generate_messages_cpp)

add_executable(wave src/wave.cpp)
target_link_libraries(wave ${catkin_LIBRARIES})
add_dependencies(wave staubli_tx60_generate_messages_cpp)


target_link_libraries(staubli_tx60_server ${STAUBLI_LIBS})





