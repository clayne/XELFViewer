cmake_minimum_required(VERSION 3.10)

project(xelfviewer VERSION 0.06 LANGUAGES CXX)

include_guard(DIRECTORY)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

file(MAKE_DIRECTORY ../release)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ../release)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ../release)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ../release)

find_package(QT NAMES Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt5 COMPONENTS Concurrent REQUIRED)
find_package(Qt5 COMPONENTS OpenGL REQUIRED)
find_package(Qt5 COMPONENTS PrintSupport REQUIRED)
find_package(Qt5 COMPONENTS Svg REQUIRED)
find_package(Qt5 COMPONENTS Sql REQUIRED)
find_package(Qt5 COMPONENTS Network REQUIRED)
find_package(Qt5 COMPONENTS Script REQUIRED)
find_package(Qt5 COMPONENTS ScriptTools REQUIRED)

add_definitions(-DUSE_DEX)
add_definitions(-DUSE_PDF)
add_definitions(-DUSE_ARCHIVE)

include(${PROJECT_SOURCE_DIR}/../archive_widget/archive_widget.cmake)
include(${PROJECT_SOURCE_DIR}/../XAboutWidget/xaboutwidget.cmake)

set(PROJECT_SOURCES
            ${ARCHIVE_WIDGET_SOURCES}
            ${XABOUTWIDGET_SOURCES}
            dialogabout.cpp
            dialogabout.ui
            dialogoptions.cpp
            dialogoptions.ui
            guimainwindow.cpp
            guimainwindow.ui
            main_gui.cpp
            rsrc.qrc
)

if(APPLE)
    add_library(${PROJECT_NAME} MODULE ${PROJECT_SOURCES})
    set_target_properties(${PROJECT_NAME} PROPERTIES
            MACOSX_BUNDLE_GUI_IDENTIFIER ntinfo.xelfviewer
            MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
            MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
            BUNDLE TRUE)
    set(MACOSX_BUNDLE_ICON_FILE ../icons/main.icns)
else()
    if(WIN32)
        add_executable(${PROJECT_NAME} WIN32
            ${PROJECT_SOURCES}
        )
    else()
        add_executable(${PROJECT_NAME}
                ${PROJECT_SOURCES}
            )
    endif()
endif()

target_link_libraries(${PROJECT_NAME} bzip2)
target_link_libraries(${PROJECT_NAME} lzma)
target_link_libraries(${PROJECT_NAME} zlib)
target_link_libraries(${PROJECT_NAME} capstone)
target_link_libraries(${PROJECT_NAME} cppfilt)
target_link_libraries(${PROJECT_NAME} yara)

target_link_libraries(${PROJECT_NAME} Qt5::Widgets)
target_link_libraries(${PROJECT_NAME} Qt5::Concurrent)
target_link_libraries(${PROJECT_NAME} Qt5::OpenGL)
target_link_libraries(${PROJECT_NAME} Qt5::PrintSupport)
target_link_libraries(${PROJECT_NAME} Qt5::Svg)
target_link_libraries(${PROJECT_NAME} Qt5::Sql)
target_link_libraries(${PROJECT_NAME} Qt5::Network)
target_link_libraries(${PROJECT_NAME} Qt5::Script)
target_link_libraries(${PROJECT_NAME} Qt5::ScriptTools)


if(WIN32)
    target_link_libraries(${PROJECT_NAME} Wintrust)
    target_link_libraries(${PROJECT_NAME} Crypt32)
endif()
