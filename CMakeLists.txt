cmake_minimum_required(VERSION 3.5)

project(biblemate2 VERSION 0.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 COMPONENTS Widgets LinguistTools Sql REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Widgets LinguistTools Sql PrintSupport REQUIRED)

set(TS_FILES
    languages/biblemate2_it_IT.ts)

set(PROJECT_SOURCES
        main.cpp
        mainwindow.cpp
        note.cpp
        book.cpp
        verse.cpp
        dbhelper.cpp
        listviewdelegate.cpp
        listviewdelegate_2.cpp
        listviewdelegate_4.cpp
        mainwindow.h
        note.h
        book.h
        verse.h
        dbhelper.h
        listviewdelegate.h
        listviewdelegate_2.h
        listviewdelegate_4.h
        mainwindow.ui
        resources.qrc
        ${TS_FILES}
)

file(GLOB MY_BIBLES "bibles/*.db")
file(COPY ${MY_BIBLES} DESTINATION bibles)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_create_translation(QM_FILES ${CMAKE_SOURCE_DIR} ${TS_FILES})

    qt_add_executable(biblemate2
                    MANUAL_FINALIZATION
                    ${PROJECT_SOURCES}
                    ${QM_FILES}
    )
    install(TARGETS biblemate2 DESTINATION bin)

# Define target properties for Android with Qt 6 as:
#    set_property(TARGET biblemate2 APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation

    #qt_create_translation(QM_FILES ${CMAKE_SOURCE_DIR} ${TS_FILES})

else()
    qt5_create_translation(QM_FILES ${CMAKE_SOURCE_DIR} ${TS_FILES})

    if(ANDROID)
        add_library(biblemate2 SHARED
            ${PROJECT_SOURCES}
            ${QM_FILES}
        )

# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(biblemate2
            ${PROJECT_SOURCES}
            ${QM_FILES}
        )
        install(TARGETS biblemate2 DESTINATION bin)

    endif()

    #qt5_create_translation(QM_FILES ${CMAKE_SOURCE_DIR} ${TS_FILES})

endif()

target_link_libraries(biblemate2 PRIVATE Qt${QT_VERSION_MAJOR}::Widgets Qt${QT_VERSION_MAJOR}::Sql Qt${QT_VERSION_MAJOR}::PrintSupport)

set_target_properties(biblemate2 PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER my.example.com
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(biblemate2)
endif()
