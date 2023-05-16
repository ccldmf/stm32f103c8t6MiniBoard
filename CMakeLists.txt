cmake_minimum_required(VERSION 3.0.0)
project(stm32f103c8t6 C CXX ASM)

# adjust project folder name if necessary
set(STM32_PROJ_FOLDER .)

message(STATUS "Building project: ${PROJECT_NAME}")
set(STM32_CMAKE_DIR "${CMAKE_SOURCE_DIR}")

## Set the SDK_PATH for project root folder
set(SDK_PATH ${CMAKE_SOURCE_DIR})

# set the default build type
if(NOT CMAKE_BUILD_TYPE) 
    set(CMAKE_BUILD_TYPE Debug)
endif(NOT CMAKE_BUILD_TYPE)

if(CMAKE_BUILD_TYPE MATCHES Debug)
  message(STATUS "Build type: Debug")
elseif(CMAKE_BUILD_TYPE MATCHES Release)
  message(STATUS "Build type: Release")
endif()

## Setup cross-compile toolchain 
set(CROSS_COMPILE_PREFIX arm-none-eabi-)
include(${STM32_CMAKE_DIR}/cmake/gcc-arm-none-eabi.cmake)
include(${STM32_CMAKE_DIR}/cmake/target-def.cmake)

## Setup the Kcofig cmake file
include(${STM32_CMAKE_DIR}/cmake/menuconfig/menuconfig.cmake)
## Call the project() for menuconfig.cmake
project(${PROJECT_NAME})

## auto-set variables from user input
set(MCU_FLAGS "${CPU} -mthumb ${FPU} ${FLOAT_ABI}")
set(LINK_FLAGS "${MCU_FLAGS} -Wl,--print-memory-usage,--gc-sections,--sort-section=alignment")
set(EXTRA_LINK_FLAGS "-Xlinker -Map=${PROJECT_NAME}.map -Wl,--cref,--no-warn-mismatch -g -specs=nano.specs -specs=nosys.specs -pipe -ffreestanding -fmerge-all-constants -flto")
include(${STM32_CMAKE_DIR}/cmake/stm32-gcc-flags.cmake)

# print summary of configurations
message(STATUS "MCU_FAMILY: ${MCU_FAMILY}")
message(STATUS "MCU_NAME: ${MCU_NAME}")
message(STATUS "Specified C compiler: ${CMAKE_C_COMPILER}")
message(STATUS "Linker script: ${LINKER_SCRIPT}")
message(STATUS "Use LL library: ${USE_LL_LIB}")
message(STATUS "Use HAL library: ${USE_HAL_LIB}")
message(STATUS "Use Segger SystemView library: ${USE_SYSTEM_VIEW}")

## version information
set(VERSION_MAJOR 0 CACHE STRING "Project major version number.")
set(VERSION_MINOR 1 CACHE STRING "Project minor version number.")
set(VERSION_PATCH 0 CACHE STRING "Project patch version number.")

if(USE_SYSTEM_VIEW)
add_definitions("-DENABLE_SYSTEMVIEW")
endif()

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/${STM32_PROJ_FOLDER}/Core/Inc)

## add project components
set(ELF_TARGET ${PROJECT_NAME}.elf)
set(PROJ_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/Drivers)

# common libraries
add_subdirectory(${PROJ_LIB_PATH}/CMSIS ${CMAKE_BINARY_DIR}/Drivers/CMSIS)
add_subdirectory(${PROJ_LIB_PATH}/STM32F1xx_HAL_Driver ${CMAKE_BINARY_DIR}/Drivers/STM32F1xx_HAL_Driver)

# STM32CubeMX generated source files
file(GLOB Srcs
    ${STM32_PROJ_FOLDER}/Core/Src/*.c
    ${STM32_PROJ_FOLDER}/startup/startup_stm32f103xb.s
)

add_executable(${ELF_TARGET} ${Srcs})
target_compile_definitions(${ELF_TARGET} PUBLIC -D${MCU_FAMILY})
target_link_libraries(${ELF_TARGET} PUBLIC cmsis stm32f1xx_hal -lc -lm -lnosys)

target_link_libraries(${ELF_TARGET} PRIVATE -T${LINKER_SCRIPT} ${EXTRA_LINK_FLAGS})
target_include_directories(${ELF_TARGET} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Core/Inc>)

## add post-build targets
include(${STM32_CMAKE_DIR}/cmake/stm32-gcc-postbuild.cmake)
include(${STM32_CMAKE_DIR}/cmake/stm32-gcc-jlink.cmake)

# Flash command (st-flash required)
add_custom_target(flash COMMAND st-flash write ${PROJECT_NAME}.bin 0x8000000)
