project(bmp LANGUAGES C)

set(LIBRARY_HEADER "${CMAKE_CURRENT_SOURCE_DIR}/bmpread.h")
set(LIBRARY_SOURCE "${CMAKE_CURRENT_SOURCE_DIR}/bmpread.c")
add_library(${PROJECT_NAME} STATIC
    ${LIBRARY_HEADER}
    ${LIBRARY_SOURCE}   
)

target_include_directories(${PROJECT_NAME} 
    PUBLIC
        ./
)