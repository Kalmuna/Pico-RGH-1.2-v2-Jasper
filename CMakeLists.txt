cmake_minimum_required(VERSION 3.13)

# Pull in Raspberry Pi Pico SDK
include(pico_sdk_import.cmake)

project(pico_rgh12_project C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Initialize the SDK
pico_sdk_init()

# Add executable and link libraries
add_executable(pico_rgh12
    pico_rgh12.c
)
target_link_libraries(pico_rgh12 pico_stdlib hardware_pio)

# Generate the PIO header
pico_generate_pio_header(pico_rgh12 ${CMAKE_CURRENT_LIST_DIR}/rgh12.pio)

# Create .uf2 file
pico_add_extra_outputs(pico_rgh12)
