cmake_minimum_required(VERSION 2.8.3...3.20)
project(g2o_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(g2o_src
  GIT_REPOSITORY https://github.com/hellovuong/g2o.git
  GIT_TAG master
  UPDATE_COMMAND ""
  #PATCH_COMMAND ""
  CONFIGURE_COMMAND  cd ../g2o_src &&
                     cmake -DCMAKE_BUILD_TYPE=Release .

  BUILD_COMMAND cd ../g2o_src && make -j2
  INSTALL_COMMAND 
  mkdir -p ${CATKIN_DEVEL_PREFIX}/include/g2o/ &&
  cp -r ../g2o_src/g2o/ ${CATKIN_DEVEL_PREFIX}/include/g2o/ &&
  cp -r ../g2o_src/config.h ${CATKIN_DEVEL_PREFIX}/include/g2o/ &&
  cp -r ../g2o_src/lib ${CATKIN_DEVEL_PREFIX}/lib/g2o
)

ExternalProject_Get_Property(g2o_src install_dir)
include_directories(${install_dir}/include)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} g2o_src)
target_link_libraries(${PROJECT_NAME})
cs_install()


cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/
  CFG_EXTRAS g2o-extras.cmake)
