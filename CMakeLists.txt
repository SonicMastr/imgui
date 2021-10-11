cmake_minimum_required(VERSION 3.11.0)
option(VITASDK "Build Library for VitaSDK" OFF)
option(DEMO "Build Library with Demo" OFF)

if(VITASDK)
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "VitaSDK toolchain file")
    set(VITASDK_CMAKE_FILE "$ENV{VITASDK}/share/vita.cmake" CACHE PATH "VitaSDK CMake functions file")
    include("${VITASDK_CMAKE_FILE}" REQUIRED)
else()
    set(CMAKE_TOOLCHAIN_FILE "$ENV{SCE_PSP2_SDK_DIR}/host_tools/build/cmake/psp2-snc-toolchain.cmake")
endif()

project(imgui LANGUAGES CXX)

if (NOT VITASDK)
  include(VitaDevelopmentSuite)
  set(CMAKE_C_STANDARD 99)
  add_compile_options(
    -Xdiag=0 -Xquit=2
  )
endif()

include_directories(
  ${VDSUITE_USER_INCLUDE_DIRECTORIES}
  .
)

add_compile_definitions(
  GLFW_INCLUDE_ES2
  IMGUI_IMPL_OPENGL_ES2
  __NEWLIB__
)

if(DEMO)
add_library(${PROJECT_NAME} STATIC
  imgui.cpp
  imgui_draw.cpp
  imgui_widgets.cpp
  imgui_tables.cpp
  imgui_demo.cpp
  backends/imgui_impl_glfw.cpp
  backends/imgui_impl_opengl3.cpp
)
else()
add_library(${PROJECT_NAME} STATIC
  imgui.cpp
  imgui_draw.cpp
  imgui_widgets.cpp
  imgui_tables.cpp
  backends/imgui_impl_glfw.cpp
  backends/imgui_impl_opengl3.cpp
)
endif()

if(VITASDK)
    install(FILES
      imconfig.h
      imgui_internal.h
      imgui.h 
      imstb_rectpack.h
      imstb_textedit.h 
      imstb_truetype.h
      backends/imgui_impl_glfw.h
      backends/imgui_impl_opengl3.h
      DESTINATION ${CMAKE_INSTALL_PREFIX}/include
    )
    install(TARGETS ${PROJECT_NAME}
      DESTINATION ${CMAKE_INSTALL_PREFIX}/lib
    )
else()
    install(FILES
      imconfig.h
      imgui_internal.h
      imgui.h 
      imstb_rectpack.h
      imstb_textedit.h 
      imstb_truetype.h
      backends/imgui_impl_glfw.h
      backends/imgui_impl_opengl3.h
      DESTINATION ${CMAKE_INSTALL_PREFIX}/include/vdsuite/user
    )
    install(TARGETS ${PROJECT_NAME}
      DESTINATION ${VDSUITE_LIBRARY_DIRECTORIES}
    )
endif()