if(MSVC)
    add_definitions(-DFCGuiPath -DHAVE_ACOSH -DHAVE_ASINH -DHAVE_ATANH)
else(MSVC)
    add_definitions(-DHAVE_LIMITS_H -DHAVE_CONFIG_H)
endif(MSVC)

include_directories(
    ${CMAKE_BINARY_DIR}
    ${CMAKE_SOURCE_DIR}/src
    ${CMAKE_CURRENT_BINARY_DIR}
    ${Boost_INCLUDE_DIRS}
    ${COIN3D_INCLUDE_DIRS}
    ${ZLIB_INCLUDE_DIR}
    ${OCC_INCLUDE_DIR}
    ${EIGEN3_INCLUDE_DIR}
    ${PYTHON_INCLUDE_DIRS}
)

link_directories(${OCC_LIBRARY_DIR})

set(PathGui_LIBS
    Path
    PartGui
    FreeCADGui
)

if(BUILD_QT5)
    qt5_add_resources(PathResource_SRCS Resources/Path.qrc)
else()
    qt4_add_resources(PathResource_SRCS Resources/Path.qrc)
endif()

SOURCE_GROUP("Resources" FILES ${PathResource_SRCS})

set(PathGui_UIC_SRCS
    DlgSettingsPathColor.ui
    TaskDlgPathCompound.ui
    DlgProcessorChooser.ui
)

if(BUILD_QT5)
    qt5_wrap_ui(PathGui_UIC_HDRS ${PathGui_UIC_SRCS})
else()
    qt4_wrap_ui(PathGui_UIC_HDRS ${PathGui_UIC_SRCS})
endif()

SET(PathGui_SRCS_Module
    Command.cpp
    AppPathGui.cpp
    AppPathGuiPy.cpp
    Resources/Path.qrc
    PreCompiled.cpp
    PreCompiled.h
    DlgSettingsPathColor.ui
    DlgSettingsPathColor.cpp
    DlgSettingsPathColor.h
    TaskDlgPathCompound.ui
    TaskDlgPathCompound.cpp
    TaskDlgPathCompound.h
    DlgProcessorChooser.ui
    DlgProcessorChooser.cpp
    DlgProcessorChooser.h
)

SOURCE_GROUP("Module" FILES ${PathGui_SRCS_Module})

SET(PathGui_SRCS_ViewProvider
    ViewProviderPath.cpp
    ViewProviderPath.h
    ViewProviderPathCompound.cpp
    ViewProviderPathCompound.h
    ViewProviderPathShape.cpp
    ViewProviderPathShape.h
    ViewProviderArea.cpp
    ViewProviderArea.h
)

SOURCE_GROUP("ViewProvider" FILES ${PathGui_SRCS_ViewProvider})

SET(PathGui_SRCS
    ${PathResource_SRCS}
    ${PathGui_UIC_HDRS}
    ${PathGui_SRCS_Module}
    ${PathGui_SRCS_ViewProvider}
)

if(FREECAD_USE_PCH)
    add_definitions(-D_PreComp_)
    GET_MSVC_PRECOMPILED_SOURCE("PreCompiled.cpp" PCH_SRCS ${PathGui_SRCS})
    ADD_MSVC_PRECOMPILED_HEADER(PathGui PreCompiled.h PreCompiled.cpp PCH_SRCS)
endif(FREECAD_USE_PCH)

SET(PathGuiIcon_SVG
    Resources/icons/PathWorkbench.svg
)

add_library(PathGui SHARED ${PathGui_SRCS} ${PathGuiIcon_SVG})
target_link_libraries(PathGui ${PathGui_LIBS})

SET_BIN_DIR(PathGui PathGui /Mod/Path)
SET_PYTHON_PREFIX_SUFFIX(PathGui)

fc_copy_sources(PathGui "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_DATADIR}/Mod/Path" ${PathGuiIcon_SVG})

INSTALL(TARGETS PathGui DESTINATION ${CMAKE_INSTALL_LIBDIR})
INSTALL(FILES ${PathGuiIcon_SVG} DESTINATION "${CMAKE_INSTALL_DATADIR}/Mod/Path/Resources/icons")
