# This file is part of GammaRay, the Qt application inspection and manipulation tool.
#
# SPDX-FileCopyrightText: 2015-2023 Klarälvdalens Datakonsult AB, a KDAB Group company <info@kdab.com>
#
# SPDX-License-Identifier: GPL-2.0-or-later
#
# Contact KDAB at <info@kdab.com> for commercial licensing options.
#
# probe plugin
if(NOT GAMMARAY_CLIENT_ONLY_BUILD)
    set(gammaray_positioning_srcs positioning.cpp positioninginterface.cpp positioninfopropertyadaptor.cpp)
    gammaray_add_plugin(
        gammaray_positioning
        JSON
        gammaray_positioning.json
        SOURCES
        ${gammaray_positioning_srcs}
    )
    target_link_libraries(gammaray_positioning gammaray_core Qt::Positioning)
    set_target_properties(gammaray_positioning PROPERTIES DISABLE_PRECOMPILE_HEADERS ON)

    # proxy geo position info source factory
    if(NOT QtCore_VERSION VERSION_LESS 5.7)
        set(gammaray_geopositioninfosource_srcs geopositioninfosourcefactory.cpp geopositioninfosource.cpp
                                                positioninginterface.cpp
        )

        add_library(gammaray_geopositioninfosource MODULE ${gammaray_geopositioninfosource_srcs})
        target_compile_features(gammaray_geopositioninfosource PUBLIC ${GAMMARAY_REQUIRED_CXX_FEATURES})
        target_link_libraries(gammaray_geopositioninfosource gammaray_common Qt::Positioning Qt::CorePrivate)
        install(TARGETS gammaray_geopositioninfosource DESTINATION ${TARGET_PLUGIN_INSTALL_DIR}/position)
    endif()
endif()

# ui plugin
if(GAMMARAY_BUILD_UI
   AND TARGET Qt::Location
   AND TARGET Qt::QuickWidgets
   AND NOT QtCore_VERSION VERSION_LESS 5.7
)
    set(gammaray_positioning_ui_srcs
        positioningwidget.cpp
        positioninginterface.cpp
        positioningclient.cpp
        mapcontroller.cpp
        positioningwidget.qrc
    )

    # FIXME: make it work with Qt 6.5+
    if(NOT QtCore_VERSION VERSION_GREATER_EQUAL 6.5)
        qml_lint(mapview.qml)
    endif()

    gammaray_add_plugin(
        gammaray_positioning_ui
        JSON
        gammaray_positioning.json
        SOURCES
        ${gammaray_positioning_ui_srcs}
    )

    target_link_libraries(
        gammaray_positioning_ui
        gammaray_ui
        Qt::Widgets
        Qt::Positioning
        Qt::Location
        Qt::QuickWidgets
    )
endif()
