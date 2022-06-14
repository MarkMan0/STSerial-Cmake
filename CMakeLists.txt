cmake_minimum_required(VERSION 3.23)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_TOOLCHAIN_FILE $ENV{STM32_CMAKE_TOOLCHAIN_FILE})
project(stm32-fetch-cmsis-hal C CXX ASM)

stm32_fetch_cmsis(F3)
stm32_fetch_hal(F3)

find_package(CMSIS COMPONENTS STM32F303K8 REQUIRED)
find_package(HAL COMPONENTS STM32F3 REQUIRED)
set(CMAKE_INCLUDE_CURRENT_DIR TRUE)


file(GLOB PROJECT_SOURCES CONFIGURE_DEPENDS  "src/*.cpp")


set(DEBUG_FLAGS -DDEBUG -DUSE_FULL_ASSERT -fno-inline)
set(RELEASE_FLAGS -DNDEBUG)


message(${CMAKE_BUILD_TYPE})
if(${CMAKE_BUILD_TYPE} STREQUAL "Debug")
    set(FLAGS ${DEBUG_FLAGS})
else()
    set(FLAGS ${RELEASE_FLAGS})
endif()

add_executable(STSerial ${PROJECT_SOURCES} stm32f3xx_hal_conf.h)
target_compile_options(STSerial PUBLIC ${FLAGS})
target_link_libraries(STSerial CMSIS::STM32::F303K8 STM32::NoSys 
        HAL::STM32::F3::UART
        HAL::STM32::F3::UARTEx
        HAL::STM32::F3::GPIO
        HAL::STM32::F3::CORTEX 
        HAL::STM32::F3::RCC
        HAL::STM32::F3::DMA
    )

stm32_print_size_of_target(STSerial)
stm32_generate_binary_file(STSerial)
