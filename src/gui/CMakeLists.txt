##############################################################################
# CMake
##############################################################################

cmake_minimum_required(VERSION 2.8.0)
project(gui)

##############################################################################
# Catkin
##############################################################################

# qt_build provides the qt cmake glue, roscpp the comms for a default talker
find_package(catkin REQUIRED COMPONENTS qt_build roscpp sensor_msgs image_transport ros_video_components timer)
set(QML_IMPORT_PATH "${QML_IMPORT_PATH};${CATKIN_GLOBAL_LIB_DESTINATION}" )
set(QML_IMPORT_PATH2 "${QML_IMPORT_PATH};${CATKIN_GLOBAL_LIB_DESTINATION}" )
include_directories(${catkin_INCLUDE_DIRS})
# Use this to define what the package will export (e.g. libs, headers).
# Since the default here is to produce only a binary, we don't worry about
# exporting anything.
catkin_package()

##############################################################################
# Qt Environment
##############################################################################

# this comes from qt_build's qt-ros.cmake which is automatically
# included via the dependency ca ll in package.xml
#rosbuild_prepare_qt4(QtCore QtGui QtQml QtQuick) # Add the appropriate components to the component list here
find_package(Qt5 COMPONENTS Core Gui Qml Quick REQUIRED)

##############################################################################
# Sections
##############################################################################

file(GLOB QT_RESOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} resources/*.qrc)
file(GLOB_RECURSE QT_MOC RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS include/gui/*.hpp)

QT5_ADD_RESOURCES(QT_RESOURCES_CPP ${QT_RESOURCES})
QT5_WRAP_CPP(QT_MOC_HPP ${QT_MOC})

##############################################################################
# Sources
##############################################################################

file(GLOB_RECURSE QT_SOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} FOLLOW_SYMLINKS src/*.cpp)

##############################################################################
# Binaries
##############################################################################

add_executable(gui ${QT_SOURCES} ${QT_RESOURCES_CPP} ${QT_FORMS_HPP} ${QT_MOC_HPP})
qt5_use_modules(gui Quick Core)
target_link_libraries(gui ${QT_LIBRARIES} ${catkin_LIBRARIES})
target_include_directories(gui PUBLIC include ${catkin_INCLUDE_DIRS})
add_dependencies(gui ${catkin_EXPORTED_TARGETS})
install(TARGETS gui RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
