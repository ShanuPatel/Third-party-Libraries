project(ImGuizmo)

set(HEADER_FILES "${CMAKE_CURRENT_SOURCE_DIR}/ImGuizmo.h"
)
set(SOURCE_FILES "${CMAKE_CURRENT_SOURCE_DIR}/ImGuizmo.cpp"
)
add_library(${PROJECT_NAME}
${HEADER_FILES}
${SOURCE_FILES}
)

target_include_directories(${PROJECT_NAME}
	PUBLIC
		./
	PRIVATE
      ./
)
target_link_libraries(${PROJECT_NAME} PUBLIC imgui)
