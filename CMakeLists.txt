project(Packages)

add_library(${PROJECT_NAME} INTERFACE)

#Comes from OS
find_package(OpenGL REQUIRED)
target_link_libraries(${PROJECT_NAME} INTERFACE ${OPENGL_LIBRARIES} )

#commented temp disabled uncomment if you want to use vulkan and spirv 
#NOTE - vulkan sdk needs to be installed 
#[[find_package(Vulkan REQUIRED COMPONENTS shaderc_combined)
string(REPLACE ".lib" "d.lib" SHADERC_DEBUG ${Vulkan_shaderc_combined_LIBRARY})
target_link_libraries(${PROJECT_NAME} INTERFACE
    debug ${SHADERC_DEBUG}
    optimized ${Vulkan_shaderc_combined_LIBRARY})
    target_link_libraries(${PROJECT_NAME} INTERFACE Vulkan::Vulkan Vulkan::shaderc_combined)

add_subdirectory(SPIRV-Cross)
target_link_libraries(${PROJECT_NAME} INTERFACE spirv-cross-c)
]]
#glfw_Source
add_subdirectory(glfw)

#uncomment and update the version if you want to the latest version
#[[using fectch COntent
include(FetchContent)
FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw.git
    GIT_TAG 3.3.8
)
#available from Cmake version 3.14 and above
FetchContent_MakeAvailable(glfw)]]

target_link_libraries(${PROJECT_NAME} INTERFACE glfw) 

#glad
add_subdirectory(glad)
target_link_libraries(${PROJECT_NAME} INTERFACE glad)

add_subdirectory(bmp)
target_link_libraries(${PROJECT_NAME} INTERFACE bmp)

add_subdirectory(glm)
target_link_libraries(${PROJECT_NAME} INTERFACE glm)

#imgui Docking
add_subdirectory(imgui)
target_link_libraries(${PROJECT_NAME} INTERFACE imgui)

#stb 
add_subdirectory(stb)
target_link_libraries(${PROJECT_NAME} INTERFACE stb)

#spd
add_subdirectory(spdlog)
#target_link_libraries(${PROJECT_NAME} INTERFACE spdlog::spdlog_header_only)
target_link_libraries(${PROJECT_NAME} INTERFACE spdlog::spdlog)

#entt
add_subdirectory(entt)
target_link_libraries(${PROJECT_NAME} INTERFACE EnTT::EnTT)

#yaml
set(YAML_CPP_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(YAML_CPP_BUILD_TOOLS OFF CACHE BOOL "" FORCE)
add_subdirectory(yaml)
target_link_libraries(${PROJECT_NAME} INTERFACE yaml-cpp)

#ImGuizmo
add_subdirectory(ImGuizmo)
target_link_libraries(${PROJECT_NAME} INTERFACE ImGuizmo)

#uncomment if you want to use binary version
#assimp
add_subdirectory(assimp)
target_link_libraries(${PROJECT_NAME} INTERFACE assimp::assimp)
set_property(TARGET ${PROJECT_NAME} PROPERTY COMPILE_WARNING_AS_ERROR OFF)
# Disable warnings as errors just for Assimp
target_compile_options(assimp PRIVATE /W3 /WX- /wd4100)#fixes release builds

# binary example
#[[# ===binary === #
# Precompiled Assimp (binary version)
set(ASSIMP_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/binary")
set(ASSIMP_INCLUDE_DIR "${ASSIMP_ROOT}/include")
set(ASSIMP_LIB_DIR "${ASSIMP_ROOT}/lib")

# Add headers and static lib
target_include_directories(${PROJECT_NAME} INTERFACE ${ASSIMP_INCLUDE_DIR})
target_link_libraries(${PROJECT_NAME} INTERFACE
    debug "${ASSIMP_LIB_DIR}/assimp-vc143-mtd.lib"
)]]

#MikkTSpace
add_subdirectory(MikkTSpace)
target_link_libraries(${PROJECT_NAME} INTERFACE mikktspace)

#tinyexr
set(TINYEXR_BUILD_SAMPLE OFF CACHE BOOL "" FORCE)
add_subdirectory(tinyexr)
target_link_libraries(${PROJECT_NAME} INTERFACE tinyexr)

#tinygltf
set(TINYGLTF_BUILD_LOADER_EXAMPLE OFF CACHE BOOL "" FORCE)
add_subdirectory(tinygltf)
target_link_libraries(${PROJECT_NAME} INTERFACE tinygltf)

#profiling
add_subdirectory(tracy)# version 0.11.1
# Enable Tracy profiling
add_compile_definitions(TRACY_ENABLE)
#add_compile_definitions(TRACY_NO_INVARIANT_CHECK)
add_compile_definitions(TRACY_ALLOC) 
add_compile_definitions(TRACY_NO_EXIT)
target_link_libraries(${PROJECT_NAME} INTERFACE Tracy::TracyClient)

include(FetchContent)
FetchContent_Declare(
        JoltPhysics
        GIT_REPOSITORY "https://github.com/jrouwe/JoltPhysics"
        GIT_TAG "v5.2.0"
        UPDATE_DISCONNECTED TRUE
        SOURCE_SUBDIR "Jolt"
)

# set(BUILD_SHARED_LIBS ON)
set(INTERPROCEDURAL_OPTIMIZATION=${IS_IPO_SUPPORTED})
if(NOT JoltPhysics_POPULATED)
    FetchContent_MakeAvailable(JoltPhysics)

  set(PHYSICS_REPO_ROOT ${joltphysics_SOURCE_DIR})
  include(${joltphysics_SOURCE_DIR}/Jolt/Jolt.cmake)
  #target_compile_definitions(Jolt PUBLIC "$<$<CONFIG:RelWithDebInfo>:JPH_PROFILE_ENABLED;JPH_DEBUG_RENDERER>")
  #target_compile_definitions(Jolt PUBLIC "$<$<CONFIG:Debug>:JPH_PROFILE_ENABLED;JPH_DEBUG_RENDERER>")
  if (FLOATING_POINT_EXCEPTIONS_ENABLED AND "${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
      target_compile_definitions(Jolt PUBLIC "$<$<CONFIG:RelWithDebInfo>:JPH_FLOATING_POINT_EXCEPTIONS_ENABLED>")
      target_compile_definitions(Jolt PUBLIC "$<$<CONFIG:Debug>:JPH_FLOATING_POINT_EXCEPTIONS_ENABLED>")
  endif()
endif()

target_link_libraries(${PROJECT_NAME} INTERFACE Jolt)