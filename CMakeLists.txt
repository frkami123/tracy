cmake_minimum_required(VERSION 3.24)
set(CMAKE_MODULE_PATH ${EAO_CMAKE_MODULE_PATH} ${CMAKE_MODULE_PATH})

project(EaoTracyClient VERSION 0.0.1 DESCRIPTION "EaoTracyClient for EaoEngine")

set(CMAKE_POLICY_DEFAULT_CMP0135 NEW)
set(CMAKE_POLICY_DEFAULT_CMP0048 NEW)
set(CMAKE_POLICY_DEFAULT_CMP0077 NEW)

if(TRACY_ENABLE)
  include(CPMPackageManager)

  if(NOT TARGET TracyClient)
  CPMAddPackage(
    GITHUB_REPOSITORY wolfpld/tracy
    GIT_TAG master
    GIT_SHALLOW    ON
    GIT_PROGRESS   TRUE
    #OPTIONS 
    SYSTEM YES
    EXCLUDE_FROM_ALL YES
  )
  endif()

  add_library(EaoTracyClientShared SHARED)
  target_sources(EaoTracyClient PRIVATE
    TracyClient.cpp
  )
  target_link_libraries (EaoTracyClientShared PUBLIC TracyClient)
  set_property(TARGET EaoTracyClientShared PROPERTY CXX_STANDARD 23)
  target_compile_features(EaoTracyClientShared INTERFACE cxx_std_23)
  set_property(TARGET EaoTracyClientShared PROPERTY INTERFACE_POSITION_INDEPENDENT_CODE ON)

  add_library(Eao::Tracy ALIAS EaoTracyClientShared)
else()
  add_library(EaoTracyClientDummy INTERFACE
    DummyClient/common/TracySystem.hpp
    DummyClient/tracy/Tracy.hpp
  )
  target_include_directories(
    EaoTracyClientDummy
    INTERFACE
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/DummyClient>)

  add_library(Eao::Tracy ALIAS EaoTracyClientDummy)
endif()



