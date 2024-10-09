cmake_minimum_required(VERSION 3.20)

project(Compaqt VERSION 1.0 DESCRIPTION "Compact Qt Widgets Wrapper" LANGUAGES CXX)

if (NOT CQTBASE_SOURCE_DIR OR NOT CQTBASE_BINARY_DIR)

    include(FetchContent)
    file(GLOB_RECURSE PATCHES patches/*.patch)
    FetchContent_Populate(QtBase
        GIT_REPOSITORY https://code.qt.io/qt/qtbase.git
        GIT_TAG        b839e9b36db3a4e50dfb34521d8ef8de1fd01969 # 6.8.0
        PATCH_COMMAND  git apply ${PATCHES}
    )
    
    set(CONFIG_FILE ${qtbase_BINARY_DIR}/config.opt)
    set(CONFIG_OPTIONS
        -direct2d
        -no-gif
        -no-ico
        -no-libjpeg
        -no-libpng
        -no-opengl
        -no-dbus
        -no-harfbuzz
        -no-feature-harfbuzz
        -no-openssl
        -no-icu
        -no-feature-version_tagging
        -no-feature-directfb
        -no-feature-raster-64bit
        -no-feature-raster-fp
        -no-feature-vulkan
        -no-feature-wayland
        -no-feature-network
        -no-feature-jalalicalendar
        -no-feature-hijricalendar
        -no-feature-islamiccivilcalendar
        -no-feature-permissions
        -no-feature-concurrent
        -no-feature-easingcurve
        -no-feature-animation
        -no-feature-movie
        -no-feature-pdf
        -no-feature-picture
        -no-feature-sql
        -no-feature-androiddeployqt
        -no-feature-windeployqt
        -no-feature-xml
        -no-feature-printsupport
        -no-feature-testlib
        -no-feature-mimetype-database
        -no-feature-freetype
        -no-feature-cborstreamwriter
        -no-feature-syntaxhighlighter
        -no-feature-systemtrayicon
        -no-feature-texthtmlparser
        -no-feature-textmarkdownreader
        -no-feature-textmarkdownwriter
        -no-feature-textodfwriter
        -no-feature-textbrowser
        -no-feature-identityproxymodel
        -no-feature-transposeproxymodel
        -no-feature-concatenatetablesproxymodel
        -no-feature-colornames
        -no-feature-image_heuristic_mask
        -no-feature-image_text
        -no-feature-imageformatplugin
        -no-feature-imageformat_bmp
        -no-feature-imageformat_ppm
        -no-feature-imageformat_xbm
        -no-feature-imageformat_jpeg
        -no-feature-imageformat_png
        -no-feature-inputdialog
        -no-feature-undocommand
        -no-feature-undostack
        -no-feature-undogroup
        -no-feature-effects
        -no-feature-wizard
        -no-feature-commandlinkbutton
        -no-feature-datetimeedit
        -no-feature-calendarwidget
        -no-feature-lcdnumber
        -no-feature-colordialog
        -no-feature-filedialog
        -no-feature-fontdialog
        -no-feature-fontcombobox
        -no-feature-datawidgetmapper
        -no-feature-fscompleter
        -no-feature-undoview
        -no-feature-dockwidget
        -no-feature-mdiarea
        -no-feature-dial
        -no-feature-graphicsframecapture
        -no-feature-graphicsview
        -no-feature-graphicseffect    
        -no-feature-style-fusion
        -no-feature-style-windows
        -no-feature-style-windowsvista
        -no-feature-style-stylesheet
        -no-feature-cssparser
    )
    list(JOIN CONFIG_OPTIONS "\n" CONFIG_OPTIONS)
    file(WRITE ${CONFIG_FILE}
        "-prefix\n" "${qtbase_BINARY_DIR}\n" "-static\n"
        ${CONFIG_OPTIONS} "\n"
        "-cmake-generator\nNinja Multi-Config\n"
        "--\n"
        "-DCMAKE_CONFIGURATION_TYPES=${CMAKE_CONFIGURATION_TYPES}\n"
        "-DCMAKE_SOURCE_DIR=${qtbase_SOURCE_DIR}\n"
        "-DCMAKE_BINARY_DIR=${qtbase_BINARY_DIR}\n"
    )
    execute_process(COMMAND ${CMAKE_COMMAND}
        -DOPTFILE=${CONFIG_FILE}
        -P ${qtbase_SOURCE_DIR}/cmake/QtProcessConfigureArgs.cmake
        WORKING_DIRECTORY ${qtbase_BINARY_DIR}
    )
    execute_process(COMMAND ${CMAKE_COMMAND} --build ${qtbase_BINARY_DIR} --parallel)
	
	set(CQTBASE_SOURCE_DIR ${qtbase_SOURCE_DIR} CACHE INTERNAL "")
	set(CQTBASE_BINARY_DIR ${qtbase_BINARY_DIR} CACHE INTERNAL "")
endif()

list(APPEND CMAKE_PREFIX_PATH ${CQTBASE_BINARY_DIR})
find_package(Qt6 COMPONENTS Widgets REQUIRED)

add_library(Compaqt SHARED Compaqt.cpp)
target_link_libraries(Compaqt PRIVATE Qt6::Widgets)
get_target_property(QT_INCLUDES Qt6::Widgets INTERFACE_INCLUDE_DIRECTORIES)
target_include_directories(Compaqt SYSTEM PUBLIC ${QT_INCLUDES})
target_compile_definitions(Compaqt INTERFACE QT_SHARED)
