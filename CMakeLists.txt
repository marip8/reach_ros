cmake_minimum_required(VERSION 2.8.3)
project(reach_ros)

add_compile_options(-std=c++14)

find_package(reach REQUIRED)

find_package(
  catkin REQUIRED
  COMPONENTS eigen_conversions
             interactive_markers
             moveit_core
             moveit_msgs
             moveit_ros_planning_interface
             sensor_msgs
             visualization_msgs)

catkin_package(
  INCLUDE_DIRS
  include
  LIBRARIES
  ${PROJECT_NAME}_plugins
  CATKIN_DEPENDS
  eigen_conversions
  interactive_markers
  moveit_core
  moveit_msgs
  moveit_ros_planning_interface
  sensor_msgs
  visualization_msgs)

# ######################################################################################################################
# BUILD ##
# ######################################################################################################################

include_directories(${catkin_INCLUDE_DIRS} include)

# Plugins
add_library(
  ${PROJECT_NAME}_plugins
  src/utils.cpp
  # Evaluator
  src/evaluation/manipulability_moveit.cpp
  src/evaluation/joint_penalty_moveit.cpp
  src/evaluation/distance_penalty_moveit.cpp
  # IK Solver
  src/ik/moveit_ik_solver.cpp
  # Display
  src/display/ros_display.cpp)
target_link_libraries(${PROJECT_NAME}_plugins ${catkin_LIBRARIES} reach::reach)

# Reach study node
add_executable(${PROJECT_NAME}_node src/reach_study_node.cpp)
target_link_libraries(
  ${PROJECT_NAME}_node
  ${catkin_LIBRARIES}
  yaml-cpp
  reach::reach)

# Demo
add_subdirectory(demo)

# ######################################################################################################################
# INSTALL ##
# ######################################################################################################################

install(
  TARGETS ${PROJECT_NAME}_plugins ${PROJECT_NAME}_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/ DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY launch demo DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
