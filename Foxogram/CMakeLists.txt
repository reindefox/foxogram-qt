set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets Concurrent)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets Concurrent)

qt_standard_project_setup()

set(PROJECT_SOURCES
        Source/main.cpp
        Source/ui/widgets/messenger/mainwindow.cpp
        Source/ui/widgets/messenger/mainwindow.h
        Source/ui/widgets/messenger/mainwindow.ui
        Source/launcher.h
        Source/launcher.cpp
        Source/foxogramclient.h
        Source/foxogramclient.cpp
        Source/ui/widgets/auth/authwidget.h
        Source/ui/widgets/auth/authwidget.cpp
        Source/ui/widgets/loadingwidget.h
        Source/ui/widgets/loadingwidget.cpp
        Source/ui/widgets/auth/signupwidget.h
        Source/ui/widgets/auth/signupwidget.cpp
        Source/ui/tools/giflabelloader.h
        Source/ui/tools/giflabelloader.cpp
        Source/ui/widgets/auth/signinwidget.h
        Source/ui/widgets/auth/signinwidget.cpp
        Source/services/authorizationservice.h
        Source/services/authorizationservice.cpp
        Source/ui/widgets/auth/signwidgetabstract.h
        Source/ui/widgets/auth/signwidgetabstract.cpp
        Source/ui/widgets/auth/verifyemailmodal.h
        Source/ui/widgets/auth/verifyemailmodal.cpp
        Source/ui/widgets/auth/signup.h
        Source/ui/widgets/auth/signup.cpp
        Source/ui/widgets/multilayerwidget.h
        Source/ui/widgets/multilayerwidget.cpp
        Source/ui/widgets/fwidget.h
        Source/ui/widgets/fwidget.cpp
        Source/ui/widgets/auth/digitinput.h
        Source/ui/widgets/auth/digitinput.cpp
        Source/ui/tools/stringutils.h
        Source/ui/tools/stringutils.cpp
        Source/ui/widgets/auth/resetpasswordmodal.h
        Source/ui/widgets/auth/resetpasswordmodal.cpp
        Source/ui/widgets/auth/signin.h
        Source/ui/widgets/auth/signin.cpp
        Source/ui/widgets/modalwidget.h
        Source/ui/widgets/modalwidget.cpp
        Source/ui/widgets/basestyle.h
        Source/ui/widgets/basestyle.cpp
        Source/ui/widgets/auth/style.h
        Source/ui/widgets/auth/style.cpp
        Source/ui/widgets/auth/fieldlabel.h
        Source/ui/widgets/auth/fieldlabel.cpp
        Source/ui/widgets/components/flineedit.h
        Source/ui/widgets/components/flineedit.cpp
)

set(PROJECT_RESOURCES
        Resources/foxogram.qrc
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(FoxogramDesktop
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
        ${PROJECT_RESOURCES}
    )
endif()

target_include_directories(FoxogramDesktop PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Source>
)

# Including external libraries
include(../cmake/CPM.cmake)

# FIXME: Project doesn't deploy when using library
# Foxogram API library
# CPMAddPackage(
#     NAME libfoxogram
#     GIT_REPOSITORY git@github.com:foxocorp/libfoxogram.git
#     GIT_TAG main
# )

target_link_libraries(FoxogramDesktop PRIVATE
    Qt${QT_VERSION_MAJOR}::Widgets
    Qt${QT_VERSION_MAJOR}::Concurrent
    # FIXME: Project doesn't deploy when using library
    # libfoxogram
)

if(${QT_VERSION} VERSION_LESS 6.1.0)
  set(BUNDLE_ID_OPTION MACOSX_BUNDLE_GUI_IDENTIFIER com.example.FoxogramDesktop)
endif()

set_target_properties(FoxogramDesktop PROPERTIES
    ${BUNDLE_ID_OPTION}
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

include(GNUInstallDirs)
install(TARGETS FoxogramDesktop
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

qt_generate_deploy_app_script(
    TARGET FoxogramDesktop
    OUTPUT_SCRIPT deploy_script
    NO_UNSUPPORTED_PLATFORM_ERROR
)

install(SCRIPT ${deploy_script})

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(FoxogramDesktop)
endif()
